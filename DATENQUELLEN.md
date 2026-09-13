# DGM1-Quellen der Bundesländer

Recherchestand 13. September 2026. Noch nicht in die Anwendung eingebaut,
gedacht als Grundlage für einen späteren Kachelabruf.

---

## Ausgangslage

Alle 16 Länder stellen das DGM1 inzwischen als Open Data bereit. Die Lage ist
aber weit weniger einheitlich, als die AdV-Produktbezeichnung vermuten lässt:

- **Lizenzen** sind entweder `dl-de/by-2-0` oder `cc-by/4.0` — beide erlauben
  die Weiterverwendung mit Quellenangabe, verlangen aber unterschiedliche
  Attributionstexte.
- **Formate** unterscheiden sich: GeoTIFF, Cloud-Optimized GeoTIFF, XYZ-ASCII.
  Einige Länder liefern gar kein GeoTIFF.
- **Kachelung** ist meist 1 × 1 km, teilweise aber 2 × 2 km.
- **Zugang** reicht vom direkten Verzeichnis-Download über Kartenclients bis zu
  Portalen mit Warenkorb.
- **Aktualität** schwankt erheblich: Schleswig-Holstein führt Daten ab 2005,
  Nordrhein-Westfalen ab 2019.

---

## Die 16 Länder

| Land | Stelle | Einstieg | Lizenz | Erfassung |
|---|---|---|---|---|
| Baden-Württemberg | LGL-BW | [opengeodata.lgl-bw.de](https://opengeodata.lgl-bw.de/) | dl-de/by-2-0 | 2016–2022 |
| Bayern | Bayer. Vermessungsverwaltung | [geodaten.bayern.de/opengeodata](https://geodaten.bayern.de/opengeodata/OpenDataDetail.html?pn=dgm1) | cc-by/4.0 | 2015–2024 |
| Berlin | — | in den Brandenburg-Daten enthalten | — | — |
| Brandenburg | LGB | [geoportal.brandenburg.de](https://geoportal.brandenburg.de/) | dl-de/by-2-0 | 2008–2024 |
| Bremen | LGV Bremen | [geoportal.bremen.de](https://geoportal.bremen.de/geoportal/) | cc-by/4.0 | 2015–2017 |
| Hamburg | LGV Hamburg | [metaver.de](https://metaver.de/trefferanzeige?docuuid=A39B4E86-15E2-4BF7-BA82-66F9913D5640) | dl-de/by-2-0 | 2022 |
| Hessen | HVBG | [hvbg.hessen.de](https://hvbg.hessen.de/landesvermessung/geotopographie/3d-daten/digitale-gelaendemodelle) | dl-de/by-2-0 | 2016–2023 |
| Mecklenburg-Vorpommern | LAiV MV | [laiv.geodaten-mv.de](https://laiv.geodaten-mv.de/afgvk/Geotopographie/Download?produkt=DGM1) | dl-de/by-2-0 | 2012–2024 |
| Niedersachsen | LGLN | [opengeodata.lgln.niedersachsen.de](https://opengeodata.lgln.niedersachsen.de/) | cc-by/4.0 | 2010–2022 |
| Nordrhein-Westfalen | Geobasis NRW | [opengeodata.nrw.de](https://www.opengeodata.nrw.de/produkte/geobasis/hm/dgm1_tiff/) | dl-de/by-2-0 | 2019–2024 |
| Rheinland-Pfalz | LVermGeo RP | [geoshop.rlp.de](https://geoshop.rlp.de/opendata-dgm1.html) | dl-de/by-2-0 | 2008–2024 |
| Saarland | LVGL-SL | [saarland.de/lvgl](https://www.saarland.de/lvgl/DE/themen-aufgaben/themen/geotopographie/digitalegelaendemodelle/digitalegelaendemodelle) | dl-de/by-2-0 | 2015–2016 |
| Sachsen | GeoSN | [geodaten.sachsen.de](https://www.geodaten.sachsen.de/downloadbereich-digitale-hoehenmodelle-4851.html) | dl-de/by-2-0 | 2016–2024 |
| Sachsen-Anhalt | LVermGeo ST | [lvermgeo.sachsen-anhalt.de](https://www.lvermgeo.sachsen-anhalt.de/de/gdp-dgm-dom-lsa.html) | dl-de/by-2-0 | 2009–2023 |
| Schleswig-Holstein | LVermGeo SH | [opendata.schleswig-holstein.de](https://opendata.schleswig-holstein.de/dataset/digitales-gelandemodell-1-dgm1) | cc-by/4.0 | 2005–2023 |
| Thüringen | TLBG | [tlbg.thueringen.de](https://tlbg.thueringen.de/geobasisdaten/3d-informationen/digitale-gelaendemodelle) | dl-de/by-2-0 | 2013–2024 |

Berlin hat kein eigenes DGM1-Produkt; die Höhenwerte stecken im
Brandenburg-Datensatz.

---

## Was beim Einbau zu beachten wäre

**Kein einheitlicher Abruf.** Nur wenige Länder bieten ein offenes
Verzeichnis, aus dem sich eine Kachel direkt über eine vorhersagbare URL ziehen
lässt. Nordrhein-Westfalen ist das beste Beispiel: unter
`opengeodata.nrw.de/produkte/geobasis/hm/dgm1_tiff/` liegen die Kacheln nach
einem festen Namensschema. Andere Länder verlangen eine Sitzung, einen
Warenkorb oder einen Kartenclient.

**Formatbrüche.** Mindestens Bremen, Schleswig-Holstein und Teile Thüringens
liefern XYZ-ASCII statt GeoTIFF. Diese Daten müssten vor dem Einlesen
umgewandelt werden — im Browser machbar, aber Aufwand. Niedersachsen liefert
Cloud-Optimized GeoTIFF, was sich mit geotiff.js gut verträgt.

**CORS.** Ein Browser darf Dateien von fremden Servern nur laden, wenn diese es
per HTTP-Header erlauben. Ob die Landesportale das tun, habe ich nicht geprüft —
bei den meisten vermutlich nicht. Ohne CORS bräuchte es einen eigenen
Vermittlungsdienst, und damit wäre Quick-Site-Design keine reine
Browseranwendung mehr. Der im nächsten Abschnitt beschriebene Dienst umgeht
genau dieses Problem.

---

## Der Abkürzungsweg: hoehendaten.de

Ein aggregierender Dienst bündelt die DGM1-Daten aller 16 Länder und bietet eine
API an. **Die API unterstützt CORS ausdrücklich** — die Hürde aus dem vorigen
Abschnitt entfällt damit.

**Basis-URL:** `https://api.hoehendaten.de:14444`

### Was `RawTIFRequest` liefert

```
POST /v1/rawtif
```

Eingabe ist ein Referenzpunkt, wahlweise als Lon/Lat (EPSG:4326) oder als UTM
(EPSG:25832 oder 25833). Daraus leitet der Dienst die zugehörige 1 × 1 km-Kachel
ab und gibt deren unveränderte Höhendaten als GeoTIFF zurück, base64-kodiert im
JSON-Körper, der Body ist gzip-komprimiert.

Mitgeliefert werden Metadaten zu Herkunft, Aktualität und Attribution. Das ist
wichtig, weil beide Lizenzen eine Quellenangabe verlangen — der Dienst liefert
den korrekten Text je Land gleich mit.

Liegt der Punkt auf einer Landesgrenze, enthält die Antwort zwei oder drei
Kacheln, jeweils vom betroffenen Land.

### Was das für Quick-Site-Design bedeutet

Der Nutzer gibt Koordinaten ein oder klickt in eine Karte, die Anwendung holt
die Kachel und baut daraus das Gelände. Kein Dateidownload, kein Umweg über
sechzehn Portale, keine Formatumwandlung — der Dienst hat die XYZ-Daten und die
2 × 2 km-Kachelungen bereits vereinheitlicht.

Die bestehende Verarbeitungskette bliebe unverändert: `readTiffTiles` bekommt
einen ArrayBuffer, gleich ob aus einem Dateifeld oder aus einer Antwort. Für
mehrere Kacheln würde man mehrfach abfragen und `gridTiles` wie bisher
verschmelzen lassen.

### Quelltext und Lizenz

Das Projekt liegt offen: **[github.com/Klaus-Tockloth/hoehendaten.de](https://github.com/Klaus-Tockloth/hoehendaten.de)**,
MIT-Lizenz, Betreiber ist Klaus Tockloth.

Wichtig: Das Repository enthält **nur die Webseite** — HTML-Seiten, CSS, die
Kartenansicht und eine Caddy-Konfiguration. Der Dienst hinter
`api.hoehendaten.de` liegt nicht offen. Ein Selbstbetrieb der API ist auf
dieser Grundlage also nicht möglich.

Die MIT-Lizenz verträgt sich mit der GPLv3, falls wir je Teile der Webseite
übernehmen wollten. Für die Nutzung der API sagt sie nichts aus.

### Grenzen

- **1200 Kacheln pro Stunde**, also 20 pro Minute. Für die Handbedienung
  reichlich, für einen Stapelabruf großer Gebiete knapp.
- **Ein Einzelprojekt.** Das Repository hat 33 Commits, keine Sterne, keine
  Forks, keine weiteren Mitwirkenden. Fällt der Betreiber aus, fällt der Dienst
  mit aus. Das Laden eigener Dateien muss deshalb als Weg erhalten bleiben.
- **Kein Selbstbetrieb möglich**, weil der Serverteil nicht offenliegt.
- **Nutzungsbedingungen** für den Einsatz in einem fremden Werkzeug sind nicht
  dokumentiert. Vor einem Einbau beim Betreiber nachfragen; Kontakt über das
  [Impressum](https://hoehendaten.de/impressum.html).
- **Nur DGM.** Gebäudemodelle bietet der Dienst nicht.

### Weitere brauchbare Endpunkte

| Aufruf | Zweck |
|---|---|
| `/v1/point` | Höhe zu einem Lon/Lat-Punkt |
| `/v1/utmpoint` | Höhe zu einem UTM-Punkt |
| `/v1/contours` | Höhenschichtlinien einer Kachel |
| `/v1/hillshade` | Schummerung einer Kachel |
| `/v1/slope` | Hangneigung einer Kachel |

`/v1/utmpoint` wäre unabhängig vom Kachelabruf nützlich: eine Höhenabfrage zur
Kontrolle, ohne ein Gelände zu laden. Die Punktabfragen haben ein eigenes
Limit von 18.000 pro Stunde.

---

## LoD2-Gebäudemodelle

Nicht Teil dieser Recherche. Die Gebäudemodelle liegen überwiegend in denselben
Portalen, teils unter abweichenden Lizenzen. Das müsste getrennt erhoben
werden.

---

## Quellen

Die Tabelle stützt sich auf die Zusammenstellung von
[hoehendaten.de](https://hoehendaten.de/), ergänzt um die Einstiegsseiten der
Landesämter. Lizenz- und Zeitangaben stammen aus derselben Quelle und wurden
nicht gegen die Metadaten der Länder einzeln geprüft. Vor einem Einbau sollten
sie stichprobenartig nachkontrolliert werden — insbesondere die Erfassungs-
zeiträume ändern sich laufend.
