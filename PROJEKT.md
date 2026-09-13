# Quick-Site-Design — Projektstand

Stand: 12. September 2026. Auslieferung ist eine einzige eigenständige Datei: `index.html` (~122 KB).

---

## Was das Ding macht

Eine Webseite, die digitale Geländemodelle (GeoTIFF/DGM) und Gebäudemodelle (CityGML LOD2) aus Open Data einliest und daraus eine dreidimensionale Baustelleneinrichtungsplanung macht. Der Nutzer platziert Container, Turmdrehkrane, Mobilkrane und Baustraßen auf dem echten Gelände und kann Koordinaten abgreifen.

Läuft ohne Server, ohne Installation, ohne Build-Schritt. Doppelklick auf die Datei genügt, eine Internetverbindung wird nur für drei CDN-Bibliotheken gebraucht.

---

## Aufbau

Die ausgelieferte Datei wird aus fünf Quelldateien zusammengesetzt:

| Datei | Zeilen | Inhalt |
|---|---|---|
| `shell_head.html` | 404 | HTML-Gerüst, komplettes CSS, `<script src>`-Tags der Bibliotheken |
| `app.js` | 1922 | Kernlogik: Szene, Gelände, Bausteine, Geometrie, Objektverwaltung |
| `ui.js` | 1110 | Bedienoberfläche: Formulare, Palette, Dateiimport, Tweak-Panel |
| `boot.js` | 45 | Startsequenz und Renderschleife |

Der Zusammenbau entfernt die Node-Exportblöcke am Ende von `app.js` und `ui.js` und hängt alles in ein einziges `<script>`. Deshalb liegen beide Dateien im selben Scope und greifen direkt auf gemeinsame Variablen zu (`scene`, `TERRAIN`, `objects`, `activeTool`, …). Das ist Absicht, nicht Nachlässigkeit.

**Build-Befehl** (Python, im Arbeitsverzeichnis):

```python
def strip(src):
    i = src.find("if (typeof module !== 'undefined' && module.exports) {")
    return src[:i].rstrip() + "\n" if i >= 0 else src

out = (open('shell_head.html').read()
       + strip(open('app.js').read()) + "\n"
       + strip(open('ui.js').read()) + "\n"
       + open('boot.js').read() + "\n</script>\n</body>\n</html>\n")
open('/mnt/user-data/outputs/index.html', 'w').write(out)
```

### Externe Bibliotheken

```
three.js r128        cdnjs
OrbitControls r128   jsDelivr  (cdnjs führt den examples-Pfad nicht — wichtig!)
geotiff.js 2.1.3     jsDelivr  (dist-browser-Bundle)
```

---

## Testaufbau

Kein Browser verfügbar, deshalb wird gegen selbstgebaute Stubs getestet.

| Datei | Tests | Zweck |
|---|---|---|
| `test.js` | 127 | Kernlogik gegen `three-stub.js` |
| `test-ui.js` | 82 | `app.js` + `ui.js` im gemeinsamen Scope via `vm`, gegen DOM-Stub |
| `validate-html.js` | 29 | Fertige HTML-Datei: Struktur, IDs, Syntax, Laufzeit im Stub-Browser |

`three-stub.js` (321 Zeilen) bildet die genutzte Three.js-Teilmenge nach und wirft bei Fehlbedienung — dadurch fallen Tippfehler und falsche Indizes auf. `xml-stub.js` (127 Zeilen) ist ein kleiner XML-Parser als `DOMParser`-Ersatz.

**Alle drei müssen grün sein, bevor ausgeliefert wird.**

```bash
for f in test.js test-ui.js validate-html.js; do node $f | grep Bestanden; done
```

---

## Festlegungen, die nicht verhandelbar sind

Diese Punkte sind mehrfach Ursache von Fehlern gewesen:

**Achsenkonvention:** `+x = Ost`, `-z = Nord`, `y = Höhe in Metern über `TERRAIN.zmin``. Nord auf `+z` ergibt von oben betrachtet ein Spiegelbild. Gilt für Gelände, GeoTIFF-Import, CityGML und Koordinatenanzeige gleichermaßen.

**Maßstab:** 1 Three.js-Einheit = 1 Meter. Keine Überhöhung, wurde auf Wunsch entfernt.

**Kein mitgeliefertes Gelände.** Die Seite startet leer, das Datenfenster ist hervorgehoben, die Bausteine sind gesperrt bis eine Datei geladen ist.

**Kein `localStorage`/`sessionStorage`.** Einstellungen gelten nur für die Sitzung.

**Drehung:** Bei Kranen dreht nur der Oberbau. `g.rotation.y = baseRot`, `slew.rotation.y = rot - baseRot` — dadurch bleibt der Auslegerwinkel absolut, unabhängig vom Fundament.

**Platzierung** läuft über `pointerdown`/`pointerup`, nicht `click`. Mit Bewegungstoleranz (5 px), Wiederholungssperre (250 ms) und Beschränkung auf die linke Maustaste. Sonst werden doppelte Objekte gesetzt.

**Gebäude brauchen `side: THREE.DoubleSide`.** LOD2-Wandpolygone sind uneinheitlich gewickelt; ohne Doppelseitigkeit fehlt etwa die Hälfte der Wände.

**Auswahlrahmen** werden aus `userData.selBox` gebaut, nicht mit `THREE.BoxHelper`. Der Helper ignoriert Instanz-Matrizen und liegt bei Containerblöcken völlig falsch.

**`showRoadNodes` darf die Knotenauswahl nicht zurücksetzen.** Es ruft `dropNodeGroup()` auf, nicht `clearRoadNodes()` — sonst verliert jeder Neuaufbau den gewählten Knoten.

**Esc prüft zuerst den aktiven Knoten**, sonst geht die Objektauswahl mit verloren.

**Detailstufe „Original"** wird als `maxGrid === 0` übergeben. Ein `||`-Fallback macht daraus die Voreinstellung — der Sonderfall muss ausdrücklich geprüft werden.

**Fallstrick:** `FIELD_ORDER[type] || 9` — `select` hat den Rang 0, der `||`-Kurzschluss macht daraus 9. Deshalb gibt es `fieldRank()`.

---

## Funktionsumfang

### Sprachen
Deutsch und Englisch, umschaltbar über die Knöpfe **DE** und **EN** links neben dem Ansichtsknopf (`setLanguage`). Wörterbuch in `STRINGS` (`ui.js`), Zugriff über `T(key)`. Feste Markup-Texte tragen `data-i18n="key"` und werden von `applyLanguage()` gesetzt; dynamische Texte laufen über `refreshTexts()`. Beide Sprachen müssen dieselben Schlüssel haben — es gibt einen Test dafür.

### Datenimport
- **GeoTIFF**, mehrere Dateien gleichzeitig. Kacheln werden nicht aneinandergeklebt, sondern alle Pixel in ein gemeinsames Zielraster gemittelt. Unterschiedliche Auflösungen und Überlappungen sind dadurch unkritisch. Lücken werden aus Nachbarwerten gefüllt (`fillGaps`).
- **CityGML**, mehrere Dateien gleichzeitig. Defekte Dateien werden übersprungen und namentlich gemeldet. Achsenreihenfolge (Rechts-/Hochwert) wird automatisch erkannt. Gebäude außerhalb der Kachel werden verworfen.
- **Detailstufe**: 140 / 260 / 500 / 900 Stützpunkte oder Originalauflösung. Die eingelesenen Kacheln bleiben im Speicher (`lastDemTiles`), ein Stufenwechsel ruft nur `gridTiles()` erneut auf.
- **KBS** wird aus den GeoKeys gelesen (`ProjectedCSTypeGeoKey`), Tabelle in `EPSG_NAMES`.

### Bausteine
- **Container**: 1–25 nebeneinander, 1–3 Reihen hintereinander, 1–5 Stockwerke. Reihe 1 längs, Reihe 2 quer, Reihe 3 wieder längs. `InstancedMesh`, ein Draw-Call pro Block.
- **Turmdrehkran**: 34 Modelle, sämtliche Werte aus den Original-Datenblättern. 18 Liebherr (85 EC-B 5 bis 1188 EC-H 40, dazu 91 K und 125 K) und 16 WOLFFKRAN (4518 bis 7534.16 clear). Bauarten `schnell`, `flat` (spitzenlos), `head` (mit Turmkopf). Die Turmbreite ist unabhängig vom Modell von 1,1 bis 3,5 m in 0,1-m-Schritten einstellbar.
- **Mobilkran**: 9 Liebherr LTM von 1050-3.1 (50 t) bis 1750-9.1 (750 t).
- **Baustraße**: Punkt für Punkt setzen, Leertaste bestätigt. Bei Auswahl erscheinen die Stützpunkte als Kugeln (`showRoadNodes`); ein Klick wählt einen Knoten, der nächste Geländeklick versetzt ihn. „Punkt löschen" entfernt ihn, solange mindestens zwei bleiben. Ecken werden mit `filletPath()` ausgerundet; passt der Radius nicht zwischen zwei Stützpunkte, wird er dort verkleinert und das gemeldet. Trasse folgt dem DGM.

### Bedienung
- Linksklick auf Gelände oder Gebäude setzt einen Messpunkt (hellgrüne Kugel, `depthTest: false`, konstante Bildschirmgröße). R/H/Z erscheinen als Beschriftung direkt am Punkt, pro Bild neu projiziert.
- Klick auf ein Objekt zentriert die Kamera darauf.
- Geräteübersicht oben links gruppiert nach Typ. Klick auf eine Gruppe wählt alle aus und zoomt so weit zurück, dass alle ins Bild passen.
- Nach dem Platzieren öffnet sich der Editor des neuen Objekts; „Weiterer" übernimmt die Einstellungen für den nächsten.
- Der Kraneditor zeigt den Hinweis „Nur als Richtwert zu benutzen" und einen Knopf zum Datenblatt des Herstellers (`sheetUrlFor`).
- **Ansichtsfenster** unten rechts: Design (AutoCAD, Revit, Forma), Schriftart (11 gängige Schriften als Auswahlliste), Schriftgröße, Akzentfarbe, Anordnung, Fensterbreite. Alles über CSS-Variablen auf `document.documentElement`. Der Eckenradius kommt aus dem Design und ist nicht mehr einzeln einstellbar.

---

## Wichtige Funktionen im Überblick

```
app.js
  initScene / initSharedResources     Szene, geteilte Geometrien und Materialien
  buildTerrain / gridTiles            Gelände aufbauen und rastern
  readTiffTile / readTiffTiles        GeoTIFF einlesen
  parseCityGML / mergeCityGML         Gebäude einlesen und zusammenführen
  buildBuildingsMesh                  Ein Mesh für alle Gebäude (ein Draw-Call)
  getHeightAt / getAbsoluteHeightAt   Bilineare Höheninterpolation
  worldToUTM / utmToWorld             Koordinatenumrechnung
  containerLayout / buildContainer    Containeranlage
  buildTowerCrane / buildMobileCrane  Krane
  filletPath / densifyPath / buildRoad  Baustraße
  addObject / rebuildObject / moveObject / removeObject
  selectObjects / frameObjects / updateControls   Auswahl und Kamerafahrt
  setMeasureMarker / measureScreenPos Messpunkt
  computeStats / radiusConflicts      Kennzahlen und Kollisionswarnung

ui.js
  SCHEMAS / sortFields / buildForm    Parameterformulare
  selectTool / showObjectEditor / showGroupEditor
  handleDemFiles / handleGmlFiles     Dateiimport
  rebuildTerrainDetail / detailLimit  Detailstufe
  finishRoad / setRoadHint            Baustraßen-Zeichenmodus
  renderOverview / buildGroups        Geräteübersicht
  applySettings / buildTweakPanel     Darstellung
  onCanvasPointerDown / Up / onCanvasClick / onKeyDown
```

---

## Datenqualität

Interpolierte Modelle wurden entfernt. Aufgenommen sind nur Krane mit Hersteller- oder Vermieterangaben:

- **Mobilkrane**: LTM 1050-3.1, 1090-4.2, 1150-5.3, 1250-6.1, 1350-6.1, 1450-8.1, 1500-8.1, 1650-8.1, 1750-9.1
- **Turmdrehkrane**: alle 34 Modelle aus den Original-Datenblättern extrahiert (PDFs, Textebene). Die Extraktoren liegen unter `krane/extract.py` (Liebherr) und `wolff/extract_wolff.py` (WOLFFKRAN).
  - Liebherr: Ausladung, Maximallast und Spitzenlast aus der Traglasttabelle, Hakenhöhe als größter Wert der Hubhöhentabelle. Zwei Werte hat der Nutzer korrigiert: 85 EC-B 5 = 41,9 m, 270 EC-B 12 = 82,4 m.
  - WOLFFKRAN: Ausladung, Maximallast und Lastmoment aus dem Kopf des Datenblatts. Bei vier Modellen (5020.8, 6023.6, 6023.8, 6031.8) liegt die Traglasttabelle nur als Grafik vor — deren Spitzenlast steht deshalb auf `null`. Hakenhöhen sind in den WOLFF-Blättern nicht angegeben (turmabhängig) und stehen durchgehend auf `null`.

Nicht belegte Felder stehen auf `null`. Die Oberfläche gibt dann keinen Höchstwert vor, sondern lässt den Reglerbereich aus dem Schema stehen.





---

## Bekannte Grenzen

- CityGML lässt sich erst nach dem Geländemodell laden (Gebäude werden relativ zum Geländeursprung eingepasst).
- Flächen mit Löchern (Innenhöfe) werden ignoriert, es wird nur der äußere Ring gelesen.
- Fächertriangulierung kann bei stark konkaven Dachflächen unsauber aussehen. Ear-Clipping wäre der nächste Schritt.
- Originalauflösung bei 1000×1000 Punkten: Aufbau dauert Sekunden, Drehen wird träge. Bis etwa 500×500 flüssig. Oberhalb von 2,5 Millionen Stützpunkten (`GRID_CELL_CAP`) wird die Originalstufe mit einer Meldung abgelehnt.
- Einstellungen im Ansichtsfenster überleben kein Neuladen.
- Für die Bauarten `head` und `wipp` gibt es derzeit kein Modell, weil keine belegten Daten vorlagen.

---

## Offene Ideen

- Ear-Clipping statt Fächertriangulierung für Dachflächen
- Flaches Hilfsgelände aus CityGML-Koordinaten ableiten, damit Gebäude auch ohne DGM gehen
- Automatischer Kachelabruf aus den Geoportalen der Bundesländer anhand von Koordinaten (die Länder haben sehr unterschiedliche Schnittstellen: teils WCS/WFS, teils nur ZIP-Downloads mit eigener Kachelsystematik)
- Fassung ohne externe CDN-Abhängigkeiten für den Offline-Betrieb
- Profilschnitte und Massenberechnung für die Baustraße

---

