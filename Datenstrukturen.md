---
title: "Datenstrukturen"
teaching: 30   # fix me
exercises: 30  # fix me
---



:::::::::::::::::::::::::::::::::::::: questions

- Welche "Datenarten" gibt es?
- Wie organisiere ich tabellarische Daten?
- Was ist "tidy"?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Standarddatentypen in R
- `data.frame` und `tibble`
- "tidy" Konzept

::::::::::::::::::::::::::::::::::::::::::::::::


Für die Verarbeitung von tabellarischen Daten werden in R i.d.R. `data.frame`s verwendet.
Das `tidyverse` package liefert eine erweiterte Version des `data.frame`s, das `tibble`, welches etwas transparenter in seiner Verwendung ist.
In einem `tibble` (oder `data.frame`) entspricht jede Zeile einem Datensatz (oder einer Beobachtung) und jede Spalte einer Variable (oder einem Merkmal) dieses Datensatzes.
Damit haben alle Daten in der gleichen Spalte den gleichen Datentyp, z.B. "Zahl" (`numeric`), "Kommazahl" (`double`), "Ganzzahl" (`integer`), "Text" (`character`), "Datum" (`date`), etc.
Im Folgenden wird der [Beispieldatensatz `storms`](https://dplyr.tidyverse.org/reference/storms.html) aus dem `dplyr` package gezeigt.


``` r
dplyr::storms
```

``` output
# A tibble: 19,537 × 13
   name   year month   day  hour   lat  long status      category  wind pressure
   <chr> <dbl> <dbl> <int> <dbl> <dbl> <dbl> <fct>          <dbl> <int>    <int>
 1 Amy    1975     6    27     0  27.5 -79   tropical d…       NA    25     1013
 2 Amy    1975     6    27     6  28.5 -79   tropical d…       NA    25     1013
 3 Amy    1975     6    27    12  29.5 -79   tropical d…       NA    25     1013
 4 Amy    1975     6    27    18  30.5 -79   tropical d…       NA    25     1013
 5 Amy    1975     6    28     0  31.5 -78.8 tropical d…       NA    25     1012
 6 Amy    1975     6    28     6  32.4 -78.7 tropical d…       NA    25     1012
 7 Amy    1975     6    28    12  33.3 -78   tropical d…       NA    25     1011
 8 Amy    1975     6    28    18  34   -77   tropical d…       NA    30     1006
 9 Amy    1975     6    29     0  34.4 -75.8 tropical s…       NA    35     1004
10 Amy    1975     6    29     6  34   -74.8 tropical s…       NA    40     1002
# ℹ 19,527 more rows
# ℹ 2 more variables: tropicalstorm_force_diameter <int>,
#   hurricane_force_diameter <int>
```
Bei der Verwendung von `tibble`s sind unter den Spaltennamen die Datentypen der Spalten abgekürzt dargestellt.

`data.frame`s oder `tibble`s können auch aus anderen Datenstrukturen erstellt werden, z.B. aus `vectors`, `lists` oder `matrices`.
Kleinere Datensätze können auch direkt als `tibble` erstellt werden, indem die Daten in die Funktion `tibble()` eingegeben werden.


``` r
marriages <- 
  tibble(
    name = c("Alice", "Bob", "Charlie"),
    age = c(25, 30, 35),
    married = c(TRUE, FALSE, TRUE)
  )
```


Ziel der Datenverarbeitung ist es, die Daten so zu transformieren, dass sie für die Analyse und Visualisierung geeignet sind und ein Format wie oben gezeigt vorweisen.
Wenn das der Fall ist, sprich

- jede Zeile entspricht einem Datensatz und
- jede Spalte entspricht einer Variable,

wird der Datensatz als "tidy" bezeichnet.


::::::::::::::::::::::::::::::::::::: keypoints

- Spalten in einem `tibble` werden auch Variablen (des Datensatzes) genannt.
  - Eine Spalte ist ein `vector`, d.h. es können nur Werte des gleichen Datentyps enthalten sein.
- Zeilen in einem `tibble` werden auch Beobachtungen (des Datensatzes) genannt.
- Ein Datensatz ist "tidy", 
  - wenn jede Zeile einem Datensatz und jede Spalte einer Variable entspricht. 
  - Vereinfacht: wenn man beim Visualisieren der Daten nur jeweils eine Zeile pro Datenpunkt benötigt und keine doppelt verwendet wird.

::::::::::::::::::::::::::::::::::::::::::::::::


-----------------------------------------------

Dieses Dokument wurde mit Unterstützung von GitHub Copilot erstellt, einem KI-gestützten Autocompletion-Tool, das auf der OpenAI GPT-3-Technologie basiert.

