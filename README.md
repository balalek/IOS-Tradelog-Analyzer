# Analyzátor obchodních logů

**Autor:** Martin Baláž  
**Kurz:** Operační systémy (IOS)  
**Jazyk:** Shell script

## Popis projektu

POSIX kompatibilní shell skript pro analýzu obchodních logů burzy. Tento nástroj zpracovává CSV data obchodů a poskytuje různé analýzy a vizualizace obchodních transakcí.

## Přehled

Skript `tradelog` analyzuje záznamy obchodů ve formátu CSV, podporuje filtrování podle data, burzovních symbolů a poskytuje několik analytických příkazů včetně výpočtu zisku, sledování pozic a vizuálních reprezentací obchodních dat.

## Funkce

- **Více analytických příkazů**: Výpis tickerů, výpočet zisku, zobrazení pozic, posledních cen a histogramů
- **Flexibilní filtrování**: Filtrování podle časového rozsahu, konkrétních tickerů a nastavitelné šířky grafů
- **Podpora formátů souborů**: Zpracovává běžné `.log` soubory i gzipované `.log.gz` soubory
- **POSIX kompatibilní**: Napsáno v přenositelném shell skriptu pro maximální kompatibilitu
- **Podpora pipe**: Může zpracovávat data ze stdin nebo ze souborů

## Použití

```bash
./tradelog [-h | --help]
./tradelog [FILTR...] [PŘÍKAZ] [LOG[LOG2...]]
```

## Příkazy

- **`list-tick`** – Výpis seznamu vyskytujících se burzovních symbolů (tickerů)
- **`profit`** – Výpis celkového zisku z uzavřených pozic
- **`pos`** – Výpis hodnot aktuálně držených pozic seřazených sestupně dle hodnoty
- **`last-price`** – Výpis poslední známé ceny pro každý ticker
- **`hist-ord`** – Výpis histogramu počtu transakcí dle tickeru
- **`graph-pos`** – Výpis grafu hodnot držených pozic dle tickeru

## Filtry

- **`-a DATETIME`** – Uvažovány jsou pouze záznamy PO tomto datu (bez tohoto data)
  - Formát: `YYYY-MM-DD HH:MM:SS`
- **`-b DATETIME`** – Uvažovány jsou pouze záznamy PŘED tímto datem (bez tohoto data)
  - Formát: `YYYY-MM-DD HH:MM:SS`
- **`-t TICKER`** – Uvažovány jsou pouze záznamy odpovídající danému tickeru
  - Může být použito vícekrát pro zahrnutí více tickerů
- **`-w WIDTH`** – U výpisu grafů nastavuje jejich šířku (délku nejdelšího řádku)
  - Musí být kladné celé číslo

## Formát log souborů

Skript očekává CSV soubory s následujícím formátem:
```
DATETIME;TICKER;ACTION;PRICE;CURRENCY;UNITS
```

Kde:
- **DATETIME**: Časové razítko transakce (YYYY-MM-DD HH:MM:SS)
- **TICKER**: Burzovní symbol (např. AAPL, GOOG, MSFT)
- **ACTION**: Buď "buy" nebo "sell"
- **PRICE**: Cena za jednotku
- **CURRENCY**: Kód měny
- **UNITS**: Počet obchodovaných jednotek

## Příklady použití

### Základní použití
```bash
# Výpis všech tickerů v logu
./tradelog list-tick obchody.log

# Výpočet celkového zisku
./tradelog profit obchody.log

# Zobrazení aktuálních pozic
./tradelog pos obchody.log
```

### S filtry
```bash
# Zobrazení pozic pouze pro Apple akcie
./tradelog -t AAPL pos obchody.log

# Výpočet zisku po 1.1.2023
./tradelog -a "2023-01-01 00:00:00" profit obchody.log

# Zobrazení histogramu s vlastní šířkou
./tradelog -w 50 hist-ord obchody.log
```

### Použití více souborů
```bash
# Analýza více log souborů
./tradelog profit obchody1.log obchody2.log obchody3.log

# Kombinace běžných a gzipovaných souborů
./tradelog list-tick obchody.log stare_obchody.log.gz
```

### Použití pipe
```bash
# Zpracování dat ze stdin
cat obchody.log | ./tradelog profit

# Filtrování a pipe
grep "AAPL" obchody.log | ./tradelog pos
```

## Požadavky

- POSIX kompatibilní shell prostředí
- Standardní Unix utility: `awk`, `sort`, `uniq`, `cat`, `gzip`
- Proměnné prostředí: `POSIXLY_CORRECT=yes`, `LC_NUMERIC=en.US.UTF-8`

## Technické detaily

- **Jazyk**: Shell skript (POSIX sh)
- **Závislosti**: Standardní Unix utility (awk, sort, uniq, cat, gzip)
- **Vstup**: CSV formátované obchodní logy
- **Výstup**: Textové reporty a ASCII grafy
