# Quick-Site-Design

*[English version](README.en.md)*

**Baustelleneinrichtung planen, solange die Idee noch frisch ist.**

Quick-Site-Design ist ein Werkzeug für die frühe Phase der Baustelleneinrichtung.
Statt Maße zu schätzen oder mit Symbolen auf einem Lageplan zu schieben, lädst du
das amtliche Geländemodell deines Bauplatzes und stellst darauf, was wirklich
draufsteht: Container, Turmdrehkrane, Mobilkrane, Baustraßen. Alles maßstäblich,
alles auf echter Höhenlage.

Der Nutzen liegt im Tempo. Eine Kranaufstellung ist in zwei Minuten gesetzt und in
weiteren zwei umgestellt. Du siehst sofort, ob die Ausladung reicht, ob sich zwei
Kranradien überschneiden, ob die Containeranlage auf die verfügbare Fläche passt
und wie die Baustraße im Gelände liegt. Das ersetzt keine Ausführungsplanung –
aber es erspart die Runden, in denen man sie sonst dreimal verwirft.

Technisch ist es eine einzige HTML-Datei von rund 140 KB. Kein Server, keine
Installation, kein Build. Öffnen, Geländemodell hineinziehen, loslegen.

**[→ Anwendung öffnen](https://Mucnese.github.io/quick-site-design/)**

---

## Funktionsumfang

**Geobasisdaten**
- GeoTIFF-Geländemodelle, mehrere Kacheln gleichzeitig. Die Pixel aller Kacheln
  werden in ein gemeinsames Raster gemittelt, unterschiedliche Auflösungen und
  Überlappungen sind daher unkritisch.
- CityGML LOD2, ebenfalls als Stapel. Die Achsenreihenfolge wird erkannt,
  Gebäude außerhalb der Kachel verworfen.
- Detailstufe von 140 Stützpunkten bis zur unveränderten Quellauflösung.
- Koordinatenbezugssystem wird aus den GeoKeys gelesen.

**Bausteine**
- Containeranlagen bis 25 nebeneinander, 3 Reihen, 5 Stockwerke. Ab der zweiten
  Reihe liegt jede zweite quer, wie ein Querriegel.
- 34 Turmdrehkrane von Liebherr und WOLFFKRAN, sämtliche Kennwerte aus den
  Original-Datenblättern.
- 9 Liebherr-Mobilkrane von 50 t bis 750 t.
- Baustraßen als Polygonzug mit ausgerundeten Ecken; die Trasse legt sich auf
  das Gelände, Stützpunkte lassen sich nachträglich versetzen.

**Bedienung**
- Linksklick auf Gelände oder Gebäude setzt einen Messpunkt und zeigt
  Rechtswert, Hochwert und Höhe direkt am Punkt.
- Warnung bei sich überschneidenden Kranradien.
- Drei Oberflächen (AutoCAD, Revit, Forma), elf Schriftarten, Deutsch/Englisch.

---

## Demo

Im Ordner [`demo/`](demo/) liegt ein vollständiger Beispieldatensatz zum
Ausprobieren, rund 8 MB als ZIP:

| Datei | Inhalt |
|---|---|
| `692_5336.tif` | DGM1, 1000 × 1000 Punkte, 1 m Raster, Höhen 503,1 bis 520,7 m |
| `692_5336.gml` | LoD2-Gebäudemodell, CityGML 1.0, 2913 Gebäude |

Beide in ETRS89 / UTM Zone 32N (EPSG:25832), Südwestecke bei E 692000 /
N 5336000, gelegen bei etwa 48,153 Nord und 11,588 Ost.

**So geht es los:**

1. ZIP herunterladen und entpacken
2. `index.html` im Browser öffnen
3. Unter **Daten → Gelände · GeoTIFF** die Datei `692_5336.tif` wählen
4. Danach unter **Gebäude · CityGML** die Datei `692_5336.gml` wählen
5. Einen Baustein aus der Leiste unten wählen und ins Gelände klicken

Zwei Beobachtungen, die dabei normal sind: Das Einlesen der Gebäudedatei
dauert je nach Rechner einige Sekunden, weil die unkomprimierte XML-Datei
74 MB groß ist. Und die Gebäudedatei deckt ungefähr 2 × 2 km ab, also mehr
als die Geländekachel — alles außerhalb wird beim Laden verworfen, es
bleiben rund 16.000 Flächen übrig. Der Statushinweis nennt beide Zahlen.

Die Daten stammen aus amtlichen Geobasisdaten der deutschen Landesvermessung.
Beim Weiterverwenden sind die Nutzungsbedingungen des herausgebenden
Landesamtes zu beachten.

---

## Wichtiger Hinweis zu den Krandaten

Die hinterlegten Kranwerte sind **Richtwerte**. Sie stammen aus den
veröffentlichten Datenblättern der Hersteller, sind aber von Rüstzustand,
Turmkombination und Ballastierung abhängig.

**Für die Einsatzplanung ist ausschließlich die Traglasttabelle des jeweiligen
Krans maßgeblich.** Die Anwendung ersetzt keine statische Prüfung, keine
Standsicherheitsberechnung und keine Abstimmung mit dem Kranvermieter.

Abstützmaße und Fahrzeuglängen dienen allein der Darstellung und sind
schematisch.

---

## Datenquellen

Geländemodelle und Gebäudemodelle bekommst du kostenfrei bei den
Landesvermessungsämtern. Die Portale unterscheiden sich je Bundesland; such
nach „DGM1" oder „LoD2" zusammen mit dem Landesnamen. Die meisten Länder geben
die Daten unter der Datenlizenz Deutschland heraus.

Das Programm erwartet GeoTIFF mit Höhenwerten als Float und eine Georeferenz im
Header. Für CityGML reicht die übliche LOD2-Ausgabe der Länder.

---

## Selbst betreiben

Die Datei `index.html` ist eigenständig. Drei Bibliotheken werden zur Laufzeit
von einem CDN geladen:

| Bibliothek | Lizenz |
|---|---|
| three.js r128 | MIT |
| OrbitControls (three.js examples) | MIT |
| geotiff.js 2.1.3 | MIT |

Alle drei sind mit der GPLv3 verträglich. Ohne Internetverbindung startet die
Anwendung nicht; für den Offline-Betrieb müssen die Bibliotheken mit
ausgeliefert und lokal eingebunden werden.

### Mit GitHub Pages

1. Repository `quick-site-design` anlegen, Sichtbarkeit **Public**
2. Inhalt dieses Ordners hochladen
3. Settings → Pages → Source auf `Deploy from a branch`, Branch `main`, Ordner `/ (root)`
4. Nach ein bis zwei Minuten ist die Seite unter
   `https://Mucnese.github.io/quick-site-design/` erreichbar

Heißt das Repository anders, ändert sich der letzte Teil der Adresse
entsprechend. Dann bitte auch den Verweis oben im README anpassen.

### Auf eigenem Webspace

`index.html` in das Web-Verzeichnis legen. Mehr ist nicht nötig.

---

## Entwicklung

Die ausgelieferte Datei wird aus vier Quelldateien zusammengesetzt:

```
src/shell_head.html   HTML-Gerüst und CSS
src/app.js            Szene, Gelände, Bausteine, Geometrie
src/ui.js             Bedienoberfläche
src/boot.js           Startsequenz und Renderschleife
```

Bauen mit `python3 build.py`. Getestet wird gegen selbstgebaute Stubs, ein
Browser ist dafür nicht nötig:

```bash
node test/test.js            # Kernlogik
node test/test-ui.js         # Oberfläche im gemeinsamen Scope
node test/validate-html.js   # fertige Datei
```

Alle drei müssen grün sein, bevor ausgeliefert wird. Details zu den
Festlegungen im Code stehen in `PROJEKT.md`.

---

## Lizenz

GNU General Public License, Version 3 oder später. Siehe [LICENSE](LICENSE).

Das bedeutet in Kurzform: Du darfst die Software nutzen, weitergeben und
verändern. Gibst du eine veränderte Fassung weiter, muss auch diese unter der
GPLv3 stehen und der Quelltext offenliegen.

Die Software wird ohne jede Gewährleistung bereitgestellt.
