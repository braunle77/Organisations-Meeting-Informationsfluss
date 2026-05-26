# Organisations-Meeting-Informationsfluss

Interaktives Analyse-Dashboard für Meeting-Strukturen in Organisationen.  
Liest eine Excel-Tabelle ein, bereinigt und normalisiert die Daten, und erzeugt daraus eine vollständig offline-fähige HTML-Seite.

---

## Wozu

- Meeting-Landschaft einer Organisation visualisieren
- Informationsfluss zwischen Abteilungen sichtbar machen
- Redundante Meetings identifizieren (Teilnehmer-Überschneidungen)
- Kommunikationslücken aufdecken
- Bereinigtes Ergebnis als Tabelle zurück in Confluence spielen

Das Dashboard ist als Gesprächsgrundlage für Management-Reviews gedacht, nicht als statischer Bericht.

---

## Voraussetzungen

```bash
pip install pandas openpyxl networkx plotly pyvis
```

---

## Eingabedatei

Die Quelldatei ist eine Excel-Tabelle (`.xlsx`) mit folgenden Spalten:

| Spalte | Beschreibung |
|--------|-------------|
| Abteilung | Kürzel der Organisationseinheit |
| Meeting-Name | Bezeichnung des Meetings |
| Zweck | Kurzbeschreibung des Meeting-Inhalts |
| Verantwortlich | Kürzel der verantwortlichen Person |
| Teilnehmer | Komma- oder semikolon-getrennte Kürzel |
| Rhythmus | Freitext (z.B. „wöchentlich", „alle 2 Wochen montags") |
| Informationsfluss (rein / raus / an wen) | Beschreibung des Informationsflusses |
| Status | „Aktiv" oder „Geplant" |
| Learnings | Optionale Notizen/Verbesserungsideen |

Rohdatei bleibt **lokal** und wird nicht ins Repository eingecheckt (`.gitignore`).

---

## Ablauf

### 1. Daten bereinigen

```bash
python3 daten_bereinigen.py
```

Liest standardmäßig `~/Downloads/LeitMet.xlsx`. Anderer Pfad:

```bash
python3 daten_bereinigen.py --input /pfad/zur/datei.xlsx
```

Erzeugt: `meetings_bereinigt.json`

Optionen:
- `--input`      Pfad zur Quelldatei (`.xlsx` oder `.csv`)
- `--output`     Pfad zur JSON-Ausgabe (Standard: `meetings_bereinigt.json`)
- `--confluence` Zusätzlich eine CSV für den Confluence-Import erzeugen (`confluence_tabelle.csv`)

### 2. Dashboard erzeugen

```bash
python3 dashboard_erstellen.py
```

Liest `meetings_bereinigt.json`, erzeugt `meeting_strukturanalyse.html`.

### 3. Dashboard öffnen

```bash
open meeting_strukturanalyse.html
```

Die HTML-Datei ist vollständig offline-fähig (Plotly.js eingebettet, ~5 MB) und kann per E-Mail oder Confluence geteilt werden.

---

## Confluence-Export

```bash
python3 daten_bereinigen.py --confluence
```

Erzeugt `confluence_tabelle.csv` (Semikolon-getrennt, UTF-8-BOM).  
In Numbers/Excel öffnen → alles markieren → kopieren → in Confluence-Tabelle einfügen.

---

## Dashboard-Tabs

| Tab | Inhalt |
|-----|--------|
| Netzwerk | Wer kommuniziert mit wem? Zentralität und Brücken |
| Kalender | Wann findet was statt? Rhythmus-Verteilung |
| Überschneidungen | Jaccard-Ähnlichkeit der Teilnehmergruppen |
| Abteilungen | Meeting-Anzahl und -Frequenz pro Abteilung |
| Informationsfluss | Sankey-Diagramm der Informationswege |
| Alle Meetings | Filterbare Tabelle mit allen Daten |
| Auffälligkeiten | Kommentierte Beobachtungen als Gesprächsgrundlage |

---

## Dateien

| Datei | Beschreibung |
|-------|-------------|
| `daten_bereinigen.py` | Datenbereinigung und Normalisierung |
| `dashboard_erstellen.py` | Chart-Generierung und HTML-Zusammenbau |
| `meetings_bereinigt.json` | Bereinigte Daten (Zwischenschritt) |
| `meeting_strukturanalyse.html` | Fertiges Dashboard |
| `confluence_tabelle.csv` | Exporttabelle für Confluence |

Quelldaten (`*.xlsx`, `*.csv`) sind in `.gitignore` ausgeschlossen.

---

## Anpassen

Die Skripte enthalten Konfigurationsblöcke am Anfang, die für ein neues Projekt angepasst werden:

**`daten_bereinigen.py`**
- `ALIAS_MAP` – Namenskürzel-Normalisierung
- `PERSON_ABT` – Personen-zu-Abteilung-Zuordnung
- `PERSON_SUBTEAM` – optionale Subteam-Zuordnung
- `SKIP_ROWS` – Zeilen, die beim Import übersprungen werden

**`dashboard_erstellen.py`**
- `PERSON_ABT` / `PERSON_SUBTEAM` – wie oben
- `FK_LIST` – Liste der Führungskräfte (erhalten Stern-Symbol im Netzwerk)
- `ABT_FARBEN` – Farben pro Abteilung
