---
title: "Datenverarbeitung"
teaching: 20
exercises: 0
---




:::::::::::::::::::::::::::::::::::::: questions

- Wie transformiere ich Tabellen?
- Wie baue ich komplexe Workflows mit pipes?
- Wie bearbeite ich Text?
- Wie führe ich mehrere Datensätze zusammen?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Überblick über die Möglichkeiten der Datenverarbeitung mit `tidyverse` Paketen

::::::::::::::::::::::::::::::::::::::::::::::::


## Tabellen transformieren

Die Transformation von Daten erfolgt i.d.R. mittels des `dplyr` packages.

*Grundlegend gilt: das erste Argument einer Funktion ist immer der Datensatz!*

Innerhalb von `dplyr` Funktionen können *Spaltennamen ohne Anführungzeichen* (quoting) verwendet werden.

Basistransformationen sind:

-   Filtern von Zeilen mit gegebenen Kriterien (formulieren was man BEHALTEN will!)
    -   `filter(storms, year == 2020)` = alle Sturmdaten aus dem Jahr 2020
    -   `filter(storms, year == 2020 & month == 6)` = alle Sturmdaten aus
        dem Juni 2020
    -  `filter(storms, !is.na(category))` = alle Sturmdaten, bei denen die Kategorie bekannt ist
- Konkrete Zeilenauswahl (via Index oder Anzahl)
    -   `slice(storms, 1, 3, 5)` = die Zeilen 1, 3 und 5
    -   `slice_tail(storms, n=10)` = die 10 letzten Zeilen
    -   `slice_max(storms, pressure)` = die Zeile mit dem höchsten Wert in der Spalte `pressure`
-   Sortieren von Zeilen
    -   `arrange(storms, year)` = Sturmdaten nach Jahr aufsteigend sortieren
    -   `arrange(storms, year, desc(month))` = Sturmdaten aufsteigend nach Jahr sortieren und innerhalb eines Jahres absteigend nach Monat
-   Duplikate entfernen
    -  `distinct(storms)` = alle Zeilen mit identischen Werten in allen Spalten entfernen
    -   `distinct(storms, year, month, day)` = alle Zeilen mit gleichen Werten in den Spalten `year`, `month` und `day` entfernen (reduziert die Spalten auf die Ausgewählten)
    -  `distinct(storms, year, month, day, .keep_all = TRUE)` = alle Zeilen mit gleichen Werten in den Spalten `year`, `month` und `day` entfernen, aber *alle Spalten behalten*
-   Auswählen/Entfernen von Spalten
    -   `select(storms, name, year, month, day)` = nur Spalten mit Zeitinformation und Namen der Stürme behalten
    -   `select(storms, -year, -month, -day)` = Spalte mit Zeitinformation entfernen
-   Umbenennen von Spalten
    -   `rename(storms, sturmname = name)` = Spalte `name` in `sturmname` umbenennen
-   Zusammenfassen von Daten: nur eine Zeile mit aggregierten Informationen (z.B. Mittelwert, Summe, Anzahl, etc.) pro Gruppe
    -   `summarize(storms, max_wind = max(wind), num_datasets = n())` = maximale Windgeschwindigkeit und Anzahl der Datensätze (Zeilen)
-   Gruppierung von Daten = "Zerlegung" des Datensatzes in Teiltabellen, für die anschliessende Arbeitsschritte unabängig voneinander durchgeführt werden. Wird i.d.R. verwendet, wenn die Aufgabe "pro ..." oder "für jede ..." lautet.
    -   `group_by(storms, year)` = Gruppierung der Sturmdaten nach Jahr (aber noch keine Aggregation!)
    -   `group_by(storms, year) |>  summarize(max_wind = max(wind))` = maximale Windgeschwindigkeit *pro Jahr* (eine "summarize" Zeile pro Teiltabelle = Gruppe = Jahr)
    -  `group_by(storms, year) |> filter(wind == max(wind)) |> ungroup()` = alle Sturmdaten, bei denen die maximale Windgeschwindigkeit *pro Jahr* erreicht wurde (keine Zusammenfassung!)
    - Grouping ist ein extrem mächtiges Werkzeug, das in vielen Situationen verwendet wird, um Daten zu transformieren. Allerdings braucht es etwas Übung, um zu verstehen, wie es funktioniert.

- Spalten hinzufügen/ausrechnen oder bestehende Spalten verändern (z.B. Einheiten umrechnen)
    -   `mutate(storms, wind_kmh = wind * 1.60934)` = Windgeschwindigkeit in km/h berechnen und als *neue Spalte hinzufügen*
    -   `mutate(storms, wind = wind * 1.60934)` = Windgeschwindigkeit in km/h umrechnen und *bestehende Spalte überschreiben*
    -  `mutate(storms, wind_kmh = wind * 1.60934, wind_kmh_rounded = round(wind_kmh, 1))` = es können auch mehrere Spalten auf einmal berechnet werden und dabei direkt neue angelegte Spalten in anschliessenden Formeln verwendet werden (hier Runden auf eine Nachkommastelle)

- (Teil)Tabellen "drehen" = pivotieren. Hiermit können Spalten in Zeilen und umgekehrt umgeformt werden. Das ist am Anfang etwas verwirrend, aber ungemein praktisch, um Daten in eine "tidy" Form zu bringen (siehe oben), die für die weitere Verarbeitung besser geeignet ist. Das Beispiel hierfür ist etwas länger, um für Anschauungszwecke die Datentabelle erst etwas einzukürzen.


``` r
storms |>
  filter(name == "Arthur" & year == 2020) |> # speziellen Sturm auswählen
  select(name, year, month, day, wind, pressure) |> # (zur Vereinfachung) nur spezifische Spalten
  # Verteile Wind und Druck in separate Zeilen mit entsprechenden Labels in einer neuen Spalte "measure"
  pivot_longer(cols = c(wind, pressure), names_to = "measure", values_to = "value") |>
  slice_head(n=4) # nur die ersten 4 Zeilen anzeigen
```

``` output
# A tibble: 4 × 6
  name    year month   day measure  value
  <chr>  <dbl> <dbl> <int> <chr>    <int>
1 Arthur  2020     5    16 wind        30
2 Arthur  2020     5    16 pressure  1008
3 Arthur  2020     5    17 wind        35
4 Arthur  2020     5    17 pressure  1006
```

Details und weitere Funktionen und Beispiele sind im

- [`dplyr`      Cheatsheet](https://raw.githubusercontent.com/rstudio/cheatsheets/master/data-transformation.pdf) und
- [`tidyr`     Cheatsheet](https://raw.githubusercontent.com/rstudio/cheatsheets/master/tidyr.pdf)

zusammengefasst.


## Workflows mittels Piping

Seit R 4.1.0 ist der Pipe-Operator `|>` standardmäßig in R enthalten.
Alternativ bietet das `magrittr` package den Pipe-Operator `%>%`, welcher eine etwas umfangreichere Funktionalität bietet.
Für die meisten Anwendungen ist der Standard-Pipe-Operator `|>` ausreichend und äquivalent zu `%>%` verwendbar.

Der Pipe-Operator ist ein nützliches Werkzeug, um den Code lesbarer zu machen und die Arbeitsschritte in einer logischen Reihenfolge zu verketten.
Hierbei wird das Ergebnis des vorherigen Arbeitsschrittes als erstes Argument des nächsten Arbeitsschrittes verwendet.
Da das erste Argument von (den meisten) Funktionen der Datensatz ist, ermöglicht dies die Verknüpfung von Arbeitsschritten zu einem Workflow ohne Zwischenergebnisse speichern zu müssen oder Funktionsaufrufe zu verschachteln.

Zum Beispiel:


``` r
# Beispiel: maximale Windgeschwindigkeit pro Monat im Jahr 2020

# geschachtelte Schreibweise = schwer lesbar, da von "innen nach aussen" gelesen werden muss
summarize( group_by( filter(storms, year == 2020), month), max_wind = max(wind))

# mit temporären Variablen (die i.d.R. nicht wieder verwendet werden...!)
storms_2020 <- filter(storms, year == 2020)
storms_2020_monthly <- group_by(storms_2020, month)
summarize(storms_2020_monthly, max_wind = max(wind))

# Workflow mit Pipe-Operator = einfach lesbar und erweiterbar  *thumbs-up*
storms |> # Datensatz
  filter(year == 2020) |> # Zeilenauswahl
  group_by(month) |> # Gruppierung
  summarize(max_wind = max(wind)) # Zusammenfassung
```

Pipe-basierte workflows sind ...

- einfach lesbar, da sie der normalen Leserichtung entsprechen.
- reduzieren die Anzahl der temporären Variablen, die im globalen Workspace herumliegen und die Lesbarkeit des Codes beeinträchtigen.
- einfach zu erweitern, da neue Arbeitsschritte einfach an das Ende der Kette angehängt werden können (z.B. `write_csv("bla.csv")` oder bestehende Arbeitsschritte ausgetauscht werden können.
- einfach zu debuggen, da Zwischenergebnisse einfach ausgegeben werden können (einfach `view()` oder `print()` an das Ende der entsprechenden Zeile anhängen).
- einfach zu wiederzuverwenden, da der gesamte Workflow in einer Zeile zusammengefasst ist und immer als Block aufgerufen wird. Dadurch können keine Zwischenschritte vergessen werden und der Workflow ist immer konsistent.

*Wichtig:* R Kommandos können über mehrere Zeilen verteilt werden, wie im Beispiel zu sehen ist, was die Übersichtlichkeit des Codes erhöht.
Hierzu wird z.B. der Pipe-Operator `|>` am Ende der Zeile geschrieben und der nächste Arbeitsschritt in der nächsten Zeile fortgesetzt.
Gleiches gilt für jeden Operator (`+`,  `==`, `&`, ..) sowie unvollständige Funktionsaufrufe, bei denen die Klammerung noch nicht geschlossen ist (d.h. schließende Klammer wird in der nächsten oder einer späteren Zeile geschrieben).

## Textdaten verändern und verwenden

Um Textdaten (*Strings*) zu verändern, gibt es verschiedene Funktionen des `stringr` Paketes, die in Kombination mit `mutate()` oder anderen Funktionen verwendet werden können.
Die meisten Funktionen des `stringr` Paketes sind sehr intuitiv und haben eine ähnliche Syntax wie die Funktionen des `dplyr` Paketes.
Zudem sind viele mit regulären Ausdrücken verwendbar, um Textdaten aufgrund von allgemeineren Textmustern zu erkennen und zu verändern.
Alle Funktionen des `stringr` Paketes beginnen mit `str_` und sind in der [Dokumentation](https://stringr.tidyverse.org/reference/index.html) aufgeführt.
Einige wichtige sind:

- `str_c()`: Verkettet mehrere Strings zu einem
- `str_detect()`: Prüft, ob ein String einen bestimmten Textteil enthält
- `str_replace()`: Ersetzt einen Teil eines Strings durch einen anderen
- `str_to_lower()`/`str_to_upper`: Wandelt alle Buchstaben in Klein-/Grossbuchstaben um
- `str_sub()`: Extrahiert einen Teil eines Strings
- `str_trim()`: Entfernt Leerzeichen am Anfang und Ende eines Strings
- `str_extract()`: Extrahiert einen Teil eines Strings, der einem bestimmten Muster entspricht
- `str_remove()`: Entfernt einen Teil eines Strings, der einem bestimmten Muster entspricht
- `str_split()`: Teilt einen String an einem bestimmten Trennzeichen auf

Die meisten Funktionen liefern nur einen Treffer oder verändern nur den ersten Treffer.
Daher gibt es von vielen Funktionen Varianten, die alle Treffer finden und/oder verarbeiten, z.B. `str_detect_all()`, `str_replace_all()`, `str_extract_all()`.

Die Funktionen sind im [`stringr` Cheatsheet](https://raw.githubusercontent.com/rstudio/cheatsheets/master/strings.pdf) zusammengefasst.

Beispiele für deren Verwendung mit `mutate()` und Co. sind:


``` r
# Beispiel: Grossbuchstaben in Spalte "name" in Kleinbuchstaben umwandeln
# (und nur eindeutige Einträge in Spalte "name" anzeigen)
mutate(storms, name = str_to_lower(name)) |> distinct(name)
# Beispiel: Jahrhundert aus Spalte "year" extrahieren
mutate(storms, century = str_sub(year, 1, 2)) |> distinct(name, century)
 # oder via regulärem Ausdruck
mutate(storms, century = str_extract(year, "^\\d{2}"))
mutate(storms, century = str_remove(year, "..$"))
# Beispiel: Nur Stürme mit "storm" im Status behalten
filter(storms, str_detect(status, "storm")) |> distinct(name)
```

Beachte:

1.  wenn die (Daten)Eingabe einer `stringr` Funktion ein *Vektor* ist, wird die Funktion auf jedes Element des Vektors angewendet und gibt einen Vektor mit den Ergebnissen zurück. (= *vektorisierte Prozessierung* = zentrales Verarbeitungsprinzip in R)
2.  wenn die Eingabe *kein* String ist (z.B. eine Zahl), wird die Eingabe in einen String umgewandelt und die Funktion auf den entstandenen String angewendet. (= *automatische Typumwandlung*, sogenanntes *coercion* in R)

## Daten zusammenführen

Oftmals liegen daten in mehreren Tabellen vor, die zusammengeführt werden müssen, um die Daten zu analysieren.
Zum Beispiel können Daten in einer Tabelle die Anzahl der Sturmtage pro Jahr enthalten und in einer anderen Tabelle die Kosten für Sturmschäden pro Jahr.


``` r
# Datensatz mit Sturmschäden pro Jahr (zufällige Werte) für 2015-2024
costs <-
  tibble(year = 2015:2024, # Jahresbereich festlegen
         costs = runif(length(year), 1e6, 1e8)) # Zufallsdaten gleichverteilt erzeugen

# Anzahl der Sturmtage pro Jahr
stormyDays <-
  storms |>
  # Datensatz in Einzeljahre aufteilen
  group_by(year) |>
  # nur eine Zeile pro Monat/Jahr Kombination (pro Jahr) behalten
  distinct(month, day) |>
  # zählen, wie viele Zeilen pro Jahr noch vorhanden sind
  count() |>
  ungroup()


# Beispiel (1): Sturmtaginformation (links) mit Kosteninformation (rechts) ergänzen
# BEACHTE: für Jahre ohne Eintrag in `costs` wird `NA` eingetragen !
left_join(stormyDays, costs, by = "year") |>
  # nur die ersten und letzten 3 Zeilen anzeigen
  slice( c(1:3, (n()-2):n()) )

# Beispiel (2): nur Datensätze für die beides, d.h. Sturmtage als auch Kosten, vorhanden ist
inner_join(stormyDays, costs, by = "year")
```



::::::::::::::::::::::::::::::::::::: keypoints

- Speichern sie Daten nur in Variablen zwischen, wenn sie diese Daten mehrfach benötigen.
- Verwenden sie Pipes (`|>`) um Daten durch eine Reihe von Transformationen zu leiten.
- Versuchen sie die Datenverarbeitung in kleine, leicht verständliche Schritte zu unterteilen.
- Vermeiden sie unnötige Schleifen und Schachtelungen, das meiste lässt sich mit Grouping, vektorisierten Operationen und Joins kompakter und eleganter lösen.
- Auch explizite Elementzugriffe (z.B. `df$col`) und -operationen können i.d.R. effizient durch `dplyr` Funktionen ersetzt werden.

::::::::::::::::::::::::::::::::::::::::::::::::


-----------------------------------------------

Dieses Dokument wurde mit Unterstützung von GitHub Copilot erstellt, einem KI-gestützten Autocompletion-Tool, das auf der OpenAI GPT-3-Technologie basiert.

