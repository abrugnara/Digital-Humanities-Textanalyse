# DH TextAnalysis Framework

Ein Jupyter-Notebook-Framework für die quantitative Textanalyse in den Digital Humanities. Entwickelt für den Einsatz in Lehrveranstaltungen und eigene Forschungsprojekte – läuft auf Desktop und Laptop ohne manuelle Pfadanpassung.

---

## Inhaltsverzeichnis

1. [Voraussetzungen & Installation](#1-voraussetzungen--installation)
2. [Ordnerstruktur](#2-ordnerstruktur)
3. [Schnellstart](#3-schnellstart)
4. [Blockstruktur & Zellen im Überblick](#4-blockstruktur--zellen-im-überblick)
5. [Was kann ich anpassen?](#5-was-kann-ich-anpassen)
6. [Outputs & Dateien](#6-outputs--dateien)
7. [Backup & Archiv](#7-backup--archiv)
8. [Nummerierungsschema](#8-nummerierungsschema)
9. [Konkordanzliste Alt → Neu](#9-konkordanzliste-alt--neu)
10. [Bekannte Einschränkungen](#10-bekannte-einschränkungen)
11. [Lizenz](#11-lizenz)

---

## 1. Voraussetzungen & Installation

### Python-Version

Python **3.9 oder höher** wird empfohlen. Ältere Versionen können zu Kompatibilitätsproblemen mit `nbformat` und `wordcloud` führen.

### Jupyter

```bash
pip install notebook
# oder
pip install jupyterlab
```

### Pakete

Alle benötigten Pakete werden beim ersten Ausführen von **Zelle 100** automatisch installiert, sofern sie fehlen. Für eine manuelle Installation:

```bash
pip install nltk pandas matplotlib wordcloud nbformat numpy seaborn spacy
python -m spacy download de_core_news_sm
```

Nach der Installation müssen die NLTK-Sprachressourcen einmalig heruntergeladen werden. Auch das übernimmt Zelle 100 automatisch. Manuell:

```python
import nltk
nltk.download("stopwords")
```

### Vollständige `requirements.txt`

```
nltk>=3.8
pandas>=2.0
matplotlib>=3.7
wordcloud>=1.9
nbformat>=5.9
numpy>=1.24
seaborn>=0.12
spacy>=3.7
# Sprachmodell separat: python -m spacy download de_core_news_sm
```

---

## 2. Ordnerstruktur

Das Framework erstellt alle benötigten Unterordner **automatisch** (Zelle 130), sobald der Basispfad in Zelle 110 konfiguriert ist.

```
00 DH_Projekt/
│
├── Buddenbrook/               ← Projektname (in Zelle 110 konfigurierbar)
│   ├── 01_Raw/                ← Quelltexte als .txt ablegen (UTF-8)
│   ├── 02_Output/             ← Grafiken (.png), CSV, KWIC-Exporte
│   ├── 03_AntConc/            ← Bereinigter Text für AntConc
│   ├── 04_Archiv/             ← Archivierte Einzelzellen (.ipynb, versioniert)
│   └── 05_Backup/             ← Full-Backups des Notebooks (.ipynb, versioniert)
│
├── MeinProjekt2/              ← Zweites Projekt (gleiche Struktur)
│   ├── 01_Raw/
│   └── ...
│
└── DH_TextAnalysis_Framework_v03.ipynb
```

**Wichtig:** Quelltexte müssen als **UTF-8 kodierte `.txt`-Dateien** in `01_Raw/` abgelegt werden. Andere Formate (`.docx`, `.pdf`) müssen vorher konvertiert werden.

---

## 3. Schnellstart

1. Repository klonen oder Notebook herunterladen.
2. Notebook in Jupyter öffnen.
3. In **Zelle 110** den Projektnamen und die OneDrive-Pfade anpassen.
4. Quelltextdatei als `.txt` in `01_Raw/` ablegen.
5. **„Alle ausführen"** – das Notebook läuft vollständig durch, ohne Fehlermeldungen.
6. Ergebnisse liegen danach in `02_Output/`.

---

## 4. Blockstruktur & Zellen im Überblick

Das Notebook ist in neun thematische Blöcke gegliedert. Die Zellenummern folgen einem **100er/10er-Schema** (Block A = 100er, Block B = 200er usw.), damit neue Zellen jederzeit eingefügt werden können, ohne die Nummerierung zu verschieben.

---

### Block A – Infrastruktur & Setup `[Zellen 100–130]`

Muss immer zuerst ausgeführt werden. Kein manueller Eingriff nötig.

| Zelle | Bezeichnung | Pflicht |
|---|---|---|
| **100** | pip-Install & Requirements-Check | MUST |
| **110** | Master-Cockpit: Imports, Pfade, Motive | MUST |
| **120** | Visualisierungs-Zentrale (`speichere_grafik()`) | optional |
| **130** | Ordner-Bootstrap: alle Projektordner anlegen | MUST |

**Was passiert hier:** Zelle 100 prüft, ob alle Python-Pakete installiert sind, und installiert fehlende automatisch nach. Zelle 110 ist die zentrale Konfigurationsstelle des gesamten Frameworks: Projektname, Pfade für Desktop und Laptop sowie die Motiv-Definitionen werden hier festgelegt. Zelle 130 legt die komplette Ordnerstruktur an, sofern sie noch nicht existiert.

---

### Block B – Datei-Import & Diagnose `[Zellen 210–240]`

| Zelle | Bezeichnung | Pflicht |
|---|---|---|
| **210** | Datei-Import via tkinter-Dialog | MUST |
| **220** | Dynamischer Korpus-Loader (alle `.txt`) | MUST |
| **230** | Metadaten-Scanner | optional |
| **240** | Diagnose & manuelle Pfad-Korrektur | optional |

**Was passiert hier:** Zelle 210 öffnet ein Dateiauswahl-Fenster (Windows/macOS/Linux). Ist kein GUI verfügbar, greift ein automatischer Fallback auf die erste `.txt`-Datei in `01_Raw/`. Zelle 220 lädt alle `.txt`-Dateien aus dem Rohtext-Ordner als pandas-DataFrame, was die Basis für Mehrtext-Analysen bildet. Zelle 240 ist nützlich, wenn die automatische Pfadfindung aus Zelle 110 nicht greift und ein manueller Pfad-Override nötig ist.

---

### Block C – Textbereinigung & Tokenisierung `[Zellen 310–330]`

| Zelle | Bezeichnung | Pflicht |
|---|---|---|
| **310** | Basis-Reinigung (Demo-Text) | optional |
| **320** | Tokenisierung | MUST |
| **330** | Bilinguale Stopwort-Reinigung (DE + EN) | MUST |
| **340** | Lemmatisierung via spaCy | optional |

**Was passiert hier:** Zelle 320 zerlegt den Rohtext mit einem regulären Ausdruck in eine Wortliste (`wort_liste`). Zelle 330 bereinigt diese Liste um deutsche und englische Stoppwörter (via NLTK) sowie projektspezifische Zusatz-Stoppwörter und erzeugt die `saubere_liste`. Zelle 340 reduziert alle Wortformen auf ihre Grundform (Lemma): `glänzte`, `glänzend`, `Glänzen` → `glänzen`. Das erhöht die Präzision von Häufigkeits- und Motiv-Analysen erheblich. Nach der Lemmatisierung steht `lemma_liste` zur Verfügung, die in allen Analyse-Zellen alternativ zu `saubere_liste` verwendet werden kann. Zelle 310 ist eine reine Lehrzelle ohne Einfluss auf die Analyse.

---

### Block D – Deskriptive Analyse `[Zellen 410–435]`

| Zelle | Bezeichnung | Pflicht |
|---|---|---|
| **410** | Top-20-Wörter (Häufigkeitsplot) | empfohlen |
| **415** | Type-Token-Ratio (TTR) – Lexikalische Diversität | optional |
| **416** | Zipf-Kurve (Qualitätscheck Textbereinigung) | optional |
| **420** | WordCloud generieren & speichern | empfohlen |
| **430** | Test-Check: Wildcard-Treffer | optional |
| **435** | Test: Kommt mein Wort vor? | optional |

**Was passiert hier:** Erste Sichtung des bereinigten Textes. Zelle 410 zeigt die häufigsten Wörter als Balkendiagramm. Zelle 415 berechnet die **Type-Token-Ratio (TTR)**: das Verhältnis einzigartiger Wörter zur Gesamtwortzahl – ein Maß für lexikalische Diversität. Zusätzlich wird der MATTR (Moving Average TTR) berechnet, der im Gegensatz zum klassischen TTR unabhängig von der Textlänge ist. Zelle 416 zeigt die **Zipf-Kurve**: Auf einer doppelt-logarithmischen Achse sollte die Häufigkeitsverteilung eine Gerade ergeben. Abweichungen am linken Rand deuten auf verbliebene Stoppwörter hin, Abweichungen am rechten Rand auf OCR-Fehler oder Eigennamen. Beide Zellen speichern ihre Grafiken automatisch in `02_Output/`.

---

### Block E – Chronologische & Thematische Analyse `[Zellen 510–570]`

| Zelle | Bezeichnung | Pflicht |
|---|---|---|
| **510** | Chronologische Segmentanalyse (10 Teile) | MUST |
| **512** | Normierte Motiv-Häufigkeiten (pro 1.000 Wörter) | empfohlen |
| **515** | Test: Häufigste Wörter im ersten Segment | optional |
| **516** | Test: Wort-Varianten-Suche | optional |
| **520** | Narrative Kapitel-Segmentierung | empfohlen |
| **530** | Deep Dive: Wörter hinter einem Thema | optional |
| **540** | Motivverlauf Multi-Themen (Visualisierung) | empfohlen |
| **550** | Chronologischer Motivvergleich (robust) | empfohlen |
| **560** | Kapitel-Visualisierung (Linienplot) | empfohlen |
| **570** | Test: Trefferprüfung Segment | optional |

**Was passiert hier:** Zelle 510 teilt den bereinigten Text in zehn gleich große Segmente und zählt pro Segment, wie oft jedes Motiv aus `themen_felder` vorkommt. Dabei wird eine **Hybrid-Suche** eingesetzt: Suchbegriffe ohne `!` werden exakt gesucht (findet nur `Geld`, nicht `Geldes`), Suchbegriffe mit `!` am Ende als Präfix-Suche (findet `glänzt`, `glänzend`, `Glänzen`). Zelle 520 segmentiert alternativ nach tatsächlichen Kapitel-Ankerpunkten im Text (z.B. „Erster Teil", „Zweiter Teil"). **Zelle 512** normiert alle Motiv-Häufigkeiten auf 1.000 Wörter (`Treffer / Segmentlänge × 1.000`) und verbindet dabei zwei Datenquellen: die gleichlangen Segmente aus Zelle 510 und die unterschiedlich langen Kapitel aus Zelle 520. Besonders bei der Kapitel-Segmentierung ist die Normierung unverzichtbar, da ein langes Kapitel automatisch mehr absolute Treffer erzeugt als ein kurzes. Zellen 515 und 516 stehen bewusst nach 510, weil sie `chronologie_ergebnisse` benötigen.

---

### Block F – Konkordanz & Sentiment `[Zellen 610–640]`

| Zelle | Bezeichnung | Pflicht |
|---|---|---|
| **610** | KWIC-Analyse (Keyword in Context) | empfohlen |
| **620** | Wortfeld-Konkordanz | empfohlen |
| **625** | Kollokationsanalyse (PMI) | empfohlen |
| **630** | Emotionale Trend-Analyse (Stimmungskurve) | empfohlen |
| **640** | Sentiment-Kontrast: Glanz vs. Verfall | empfohlen |

**Was passiert hier:** Zelle 610 sucht ein einzelnes Wort im Originaltext und zeigt es mit seinem unmittelbaren Kontext (Keyword in Context). Zelle 620 sucht nach einem gesamten Wortfeld und markiert jeden Fund kontextuell. Beide Ergebnisse werden als `.txt` in `02_Output/` exportiert und sind direkt in Hausarbeiten zitierbar. Zelle 625 berechnet **Kollokationen** via Pointwise Mutual Information (PMI): Welche Wörter stehen statistisch auffällig oft neben dem Suchwort – nicht nur häufig, sondern häufiger als der Zufall erwarten ließe? Das Ergebnis wird als Balkendiagramm und `.csv` exportiert. Zellen 630 und 640 berechnen Stimmungsindizes aus positiven und negativen Wortlisten.

---

### Block G – Output & Export `[Zellen 710–730]`

| Zelle | Bezeichnung | Pflicht |
|---|---|---|
| **710** | AntConc-Export (.txt) | empfohlen |
| **720** | CSV-Export der Motiv-Tabelle | empfohlen |
| **730** | Abschluss-Bericht (Konsistenz-Check) | empfohlen |

**Was passiert hier:** Zelle 710 exportiert den bereinigten Text als `.txt` für die Weiterverarbeitung in AntConc (Konkordanz-Software). Zelle 720 speichert die Motiv-Häufigkeiten aller Segmente als `.csv` mit Semikolon-Trennung (direkt in Excel öffenbar) und automatischem Zeitstempel. Zelle 730 gibt einen Gesamt-Überblick über alle Analyse-Ergebnisse und prüft, ob die Pipeline vollständig durchgelaufen ist.

---

### Block H – Backup & Archiv `[Zellen 810–820]`

| Zelle | Bezeichnung | Pflicht |
|---|---|---|
| **810** | Archiv-Generator (Einzelzelle versioniert) | auf Abruf |
| **820** | Full-Backup des Notebooks (versioniert) | auf Abruf |

**Was passiert hier:** Zelle 810 exportiert eine einzelne, zuvor mit `%%writefile spezial_analyse_temp.py` gesicherte Zelle als eigenständiges, versioniertes `.ipynb` in `04_Archiv/`. Zelle 820 sichert das gesamte Notebook als versioniertes `.ipynb` in `05_Backup/`. Beide Zellen sind gegen versehentliche Ausführung gesichert: der Full-Backup läuft nur, wenn `BACKUP_JETZT_AUSFÜHREN = True` gesetzt ist.

**Versionierungsformat:**
```
Archiv : 510_Chronologische_Analyse_v001.ipynb
Backup : BACKUP_2026-04-13_DESKTOP-XYZ_Framework_v03_v001.ipynb
```

---

### Block I – Pädagogische Zellen `[Zellen 910–960]`

| Zelle | Inhalt |
|---|---|
| **910** | Einrückungen in Python |
| **920** | Variablen anlegen |
| **930** | Name vs. Inhalt (Variable vs. String-Literal) |
| **940** | String-Concatenation |
| **950** | Datentypen / TypeError-Falle |
| **960** | Typ-Konvertierung (int/str) |

Diese Zellen sind reine Übungszellen für Python-Grundlagen. Sie haben keinen Einfluss auf die Analyse-Pipeline und können einzeln oder vollständig übersprungen werden.

---

## 5. Was kann ich anpassen?

### Projekt wechseln

In **Zelle 110**, eine Zeile ändern:

```python
AKTIVES_PROJEKT = "MeinNeuesProjekt"
```

Danach Zelle 130 ausführen – alle Ordner werden automatisch neu angelegt.

---

### OneDrive-Laufwerk anpassen (Desktop ≠ Laptop)

In **Zelle 110** die Liste `PFAD_OPTIONEN` erweitern:

```python
PFAD_OPTIONEN = [
    r"O:\OneDrive\Studium\...\00 DH_Projekt",  # Desktop
    r"D:\OneDrive\Studium\...\00 DH_Projekt",  # Laptop
    r"C:\OneDrive\Studium\...\00 DH_Projekt",  # Alternativer Buchstabe
]
```

Das Framework wählt automatisch den ersten existierenden Pfad. Kein manuelles Umschalten nötig.

---

### Motive definieren und erweitern

In **Zelle 110**, das Dictionary `themen_felder` anpassen:

```python
themen_felder = {
    "Geld":    ["Geld", "Taler", "Börse", "Geschäft"],
    "Familie": ["Tony", "Thomas", "Hanno", "Konsul"],
    "Verfall": ["Tod", "krank", "müde", "Ruine"],
    "Glanz":   ["hell!", "Licht!", "glänz!", "Sonne!"],
    # Neues Motiv einfach hinzufügen:
    "Religion": ["Gott", "Kirche", "beten!", "Seele"],
}
```

**Suchlogik:**
- Ohne `!` → **exakte Suche**: `"Geld"` findet nur `Geld`, nicht `Geldes` oder `Taschengeld`
- Mit `!` am Ende → **Präfix-Suche**: `"glänz!"` findet `glänzt`, `glänzend`, `Glänzen`

---

### Lemmatisierung aktivieren

In **Zelle 340** wird `lemma_liste` erzeugt. Um sie in der Analyse zu verwenden, einfach in den gewünschten Folge-Zellen (410–640) `saubere_liste` durch `lemma_liste` ersetzen:

```python
# Beispiel in Zelle 410:
zaehler_final = Counter(lemma_liste)   # statt Counter(saubere_liste)
```

### Kollokations-Suchwort und Fenster anpassen

In **Zelle 625**:

```python
suchwort        = "Familie"   # ← beliebiges Wort
fenster_breite  = 5           # ← Kontextfenster (Wörter links + rechts)
min_haeufigkeit = 3           # ← Mindesthäufigkeit des Nachbarworts
top_n           = 20          # ← Anzahl der angezeigten Kollokationen
```

### Stoppwörter ergänzen

In **Zelle 330**, die Liste `extra_stops` erweitern:

```python
stop_words_lokal.update(["p", "pp", "ibid", "cf", "sagte", "doch",
                          # eigene Ergänzungen:
                          "kapitel", "seite", "band"])
```

---

### KWIC-Suchwort ändern

In **Zelle 610**:

```python
suchwort       = "Verfall"   # ← beliebiges Wort eintragen
kontext_breite = 5            # ← Anzahl der Wörter vor/nach dem Treffer
```

---

### Wortfeld für Konkordanz wählen

In **Zelle 620**:

```python
ziel_feld = "Familie"   # ← muss exakt so in themen_felder (Zelle 110) stehen
```

---

### Kapitel-Ankerpunkte anpassen

In **Zelle 520**, die Liste `anker_punkte` an den eigenen Text anpassen:

```python
anker_punkte = [
    "Erster Teil",
    "Zweiter Teil",
    # ... oder für andere Texte:
    "Chapter One",
    "Chapter Two",
]
```

---

### Segment-Anzahl ändern

In **Zelle 510**:

```python
anzahl_teile = 10   # ← z.B. auf 20 für feinere Granularität erhöhen
```

---

### Sentiment-Wortlisten anpassen

In **Zelle 630**:

```python
pos_woerter = ["glück", "erfolg", "lachen", "hoffnung", "gewinn", "fest", "freude"]
neg_woerter = ["tod", "krank", "angst", "verlust", "weinen", "schmerz", "ende"]
# ← beliebig erweitern, Kleinschreibung beachten
```

---

### Themen-Vergleich für Zelle 550 wählen

In **Zelle 550**:

```python
themen_zum_vergleich = ["Geld", "Verfall"]   # ← zwei beliebige Themen aus themen_felder
```

---

## 6. Outputs & Dateien

Alle Ausgabedateien landen automatisch in `02_Output/` des aktiven Projekts.

| Datei | Erzeugt in | Inhalt |
|---|---|---|
| `Top20_Projektname.png` | Zelle 410 | Häufigkeitsbalken Top-20-Wörter |
| `Wordcloud_Projektname.png` | Zelle 420 | WordCloud des bereinigten Textes |
| `Z540_Motivverlauf_Multi_Projektname_JJJJMMTT_HHMM.png` | Zelle 540 | Motivverlauf über alle Segmente |
| `Stimmungsverlauf_Projektname.png` | Zelle 630 | Sentiment-Kurve |
| `KWIC_Suchwort_Projektname.txt` | Zelle 610 | Alle Keyword-in-Context-Fundstellen |
| `Konkordanz_Feld_Thema_Projektname.txt` | Zelle 620 | Wortfeld-Konkordanz |
| `Motiv_Statistik_Projektname_JJJJ-MM-TT_HH-MM.csv` | Zelle 720 | Motiv-Häufigkeiten pro Segment (Excel-kompatibel) |
| `Z415_TTR_Projektname_JJJJMMTT_HHMM.png` | Zelle 415 | MATTR-Kurve (Lexikalische Diversität) |
| `Z416_Zipf_Projektname_JJJJMMTT_HHMM.png` | Zelle 416 | Zipf-Kurve |
| `Z512_Norm_Segmente_Projektname_JJJJMMTT_HHMM.png` | Zelle 512 | Normierter Motivverlauf (Segmente) |
| `Z512_Norm_Kapitel_Projektname_JJJJMMTT_HHMM.png` | Zelle 512 | Normierter Motivverlauf (Kapitel) |
| `Kollokation_Suchwort_Projektname.csv` | Zelle 625 | PMI-Kollokationstabelle |
| `Z625_Kollokation_Suchwort_Projektname_JJJJMMTT_HHMM.png` | Zelle 625 | PMI-Balkendiagramm |
| `AntConc_Bereinigt_Projektname.txt` | Zelle 710 | Bereinigter Text für AntConc |

---

## 7. Backup & Archiv

### Einzelzelle archivieren

1. Die gewünschte Zelle mit folgender Magic-Zeile als **erste Zeile** versehen:
   ```python
   %%writefile spezial_analyse_temp.py
   ```
2. Diese Zelle ausführen (die Datei `spezial_analyse_temp.py` wird erzeugt).
3. **Zelle 810** ausführen.

Das Ergebnis in `04_Archiv/`:
```
510_Chronologische_Segmentanalyse_v001.ipynb
510_Chronologische_Segmentanalyse_v002.ipynb   ← nächste Version
```

### Full-Backup des Notebooks

In **Zelle 820** den Schalter umlegen und ausführen:

```python
BACKUP_JETZT_AUSFÜHREN = True
```

Das Ergebnis in `05_Backup/`:
```
BACKUP_2026-04-13_DESKTOP-XYZ_DH_TextAnalysis_Framework_v03_v001.ipynb
```

Versionsnummern werden automatisch inkrementiert (`v001`, `v002`, …). Bestehende Backups werden nie überschrieben.

---

## 8. Nummerierungsschema

| Block | Thema | Zellenbereich |
|---|---|---|
| **A** | Infrastruktur & Setup | 100–190 |
| **B** | Datei-Import & Diagnose | 200–290 |
| **C** | Textbereinigung & Tokenisierung | 300–390 |
| **D** | Deskriptive Analyse | 400–490 |
| **E** | Chronologische & Thematische Analyse | 500–590 |
| **F** | Konkordanz & Sentiment | 600–690 |
| **G** | Output & Export | 700–790 |
| **H** | Backup & Archiv | 800–890 |
| **I** | Pädagogische Zellen | 900–990 |

Neue Zellen können zwischen bestehenden eingefügt werden, ohne die Nummerierung zu verschieben. Beispiel: Eine neue Zelle zwischen 510 und 515 erhält die Nummer **512**.

---

## 9. Konkordanzliste Alt → Neu

| Alte Zell-Nr. | Neue Zell-Nr. | Kurztitel |
|---|---|---|
| 0 | **110** | Master-Cockpit |
| 0.1 | **120** | Visualisierungs-Zentrale |
| 1.3 | **130** | Ordner-Bootstrap |
| 1 | **210** | Datei-Import via Dialog |
| 1.4 | **220** | Korpus-Loader |
| 1.0 | **230** | Metadaten-Scanner |
| 1.2 | **240** | Diagnose & Pfad-Korrektur |
| 2 | **310** | Basis-Reinigung (Demo) |
| 2.1 | **320** | Tokenisierung |
| 2.3 | **330** | Bilinguale Stopwort-Reinigung |
| 3 | **410** | Top-20-Wörter |
| 5 | **420** | WordCloud |
| 2.2 | **430** | Test-Check Wildcard |
| 90 | **435** | Test: Kommt mein Wort vor? |
| 12 | **510** | Chronologische Segmentanalyse |
| 91 | **515** | Test: Häufigste Wörter Segment 1 |
| 93 | **516** | Test: Wort-Varianten-Suche |
| 12.5 | **520** | Narrative Kapitel-Segmentierung |
| 12.1 | **530** | Deep Dive: Wörter hinter einem Thema |
| 13 | **540** | Motivverlauf Multi-Themen |
| 13.1 | **550** | Chronologischer Motivvergleich |
| 12.6 | **560** | Kapitel-Visualisierung |
| 14.0 | **570** | Test: Trefferprüfung Segment |
| 6.0 | **610** | KWIC-Analyse |
| 6.1 | **620** | Wortfeld-Konkordanz |
| 14 | **630** | Emotionale Trend-Analyse |
| 14.1 | **640** | Sentiment-Kontrast |
| 5.1 | **710** | AntConc-Export |
| 5.2 | **720** | CSV-Export Motiv-Tabelle |
| 99 | **730** | Abschluss-Bericht |
| 910 | **810** | Archiv-Generator |
| 999 | **820** | Full-Backup |
| 1.1 | **910** | Training: Einrückungen |
| 7 | **920** | Training: Variablen |
| 8 | **930** | Training: Name vs. Inhalt |
| 9 | **940** | Training: Concatenation |
| 10 | **950** | Training: Datentypen |
| 11 | **960** | Training: Typ-Konvertierung |
| 4 | — | *(deprecated, entfernt)* |
| *(neu)* | **100** | pip-Install & Requirements-Check |
| *(neu)* | **340** | Lemmatisierung via spaCy |
| *(neu)* | **415** | Type-Token-Ratio (TTR) |
| *(neu)* | **416** | Zipf-Kurve |
| *(neu)* | **512** | Normierte Motiv-Häufigkeiten |
| *(neu)* | **625** | Kollokationsanalyse (PMI) |

---

## 10. Bekannte Einschränkungen

**tkinter auf macOS/Linux:** Das Dateiauswahl-Fenster (Zelle 210) benötigt eine funktionierende Tk-Installation. Auf manchen Linux-Systemen muss `python3-tk` nachinstalliert werden:
```bash
sudo apt install python3-tk
```
Ist kein GUI verfügbar, greift der automatische Fallback auf die erste `.txt`-Datei in `01_Raw/`.

**WordCloud (Zelle 420):** Auf Systemen ohne C-Compiler kann die Installation von `wordcloud` fehlschlagen. In diesem Fall:
```bash
pip install wordcloud --prefer-binary
```

**`display()` in Zelle 520 und 530:** Die Funktion `display()` ist eine Jupyter-spezifische Funktion und steht in reinen Python-Skripten nicht zur Verfügung. Das Notebook ist ausschließlich für den Einsatz in Jupyter Notebook oder JupyterLab konzipiert.

**Kapitel-Segmentierung (Zelle 520):** Die Ankerpunkte in `anker_punkte` müssen exakt mit dem Wortlaut im Quelltext übereinstimmen, einschließlich Groß-/Kleinschreibung und Sonderzeichen. Bei abweichender Orthographie (z.B. Scan-Fehler) werden Kapitel nicht erkannt.

**Mehrere Projekte gleichzeitig:** Das Framework arbeitet mit globalen Variablen. Sollen zwei Projekte gleichzeitig verglichen werden, ist eine separate Notebook-Instanz pro Projekt zu öffnen.

---

## 11. Lizenz

Dieses Framework wurde im Rahmen einer universitären Lehrveranstaltung entwickelt. Nutzung, Anpassung und Weiterentwicklung für Forschungs- und Lehrzwecke ausdrücklich erwünscht. Bei Verwendung in Publikationen bitte zitieren.
