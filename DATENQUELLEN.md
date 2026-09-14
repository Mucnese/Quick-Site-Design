# DGM1-Quellen der Bundesländer

Arbeitsnotiz. Recherchestand 13. September 2026, noch nicht in die Anwendung
eingebaut.

---

## Die 16 Länder

| Land | Stelle | Portal | Lizenz | Erfassung |
|---|---|---|---|---|
| Baden-Württemberg | LGL-BW | [opengeodata.lgl-bw.de](https://opengeodata.lgl-bw.de/) | dl-de/by-2-0 | 2016–2022 |
| Bayern | Bayer. Vermessungsverwaltung | [geodaten.bayern.de](https://geodaten.bayern.de/opengeodata/OpenDataDetail.html?pn=dgm1) | cc-by/4.0 | 2015–2024 |
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

---

## Unterschiede zwischen den Ländern

**Lizenz.** Elf Länder nutzen `dl-de/by-2-0`, fünf `cc-by/4.0`. Beide erlauben
die Weiterverwendung, verlangen aber unterschiedlich formulierte
Quellenangaben.

**Format.** Überwiegend GeoTIFF, in Niedersachsen als Cloud-Optimized GeoTIFF.
Bremen, Schleswig-Holstein und Teile Thüringens liefern XYZ-ASCII; diese Daten
brauchen eine Umwandlung, etwa mit GDAL.

**Kachelung.** Meist 1 × 1 km, teilweise 2 × 2 km.

**Zugang.** Von offenen Verzeichnissen mit vorhersagbaren Dateinamen bis zu
Portalen mit Warenkorb. Nordrhein-Westfalen ist das zugänglichste Beispiel:
unter `opengeodata.nrw.de/produkte/geobasis/hm/dgm1_tiff/` liegen die Kacheln
nach festem Namensschema.

**Aktualität.** Von 2005 (Schleswig-Holstein) bis 2024 (mehrere Länder).

---

## Hürden für einen automatischen Kachelabruf

**CORS.** Ein Browser lädt Dateien von fremden Servern nur, wenn diese es per
HTTP-Header erlauben. Ob die Landesportale das tun, ist ungeprüft; bei den
meisten vermutlich nicht. Ohne CORS bräuchte es einen eigenen
Vermittlungsdienst — damit wäre Quick-Site-Design keine reine Browseranwendung
mehr.

**Sechzehn Einzelanbindungen.** Jedes Land hätte eine eigene Zugriffslogik.
Formatumwandlung für XYZ-Länder käme dazu.

Solange das nicht gelöst ist, bleibt das Laden über die Dateifelder der Weg.

---

## LoD2-Gebäudemodelle

Nicht Teil dieser Recherche. Die Gebäudemodelle liegen überwiegend in denselben
Portalen, teils unter abweichenden Lizenzen.

---

## Verlässlichkeit

Lizenz- und Zeitangaben wurden nicht gegen die Metadaten der Länder einzeln
geprüft. Vor einem Einbau stichprobenartig nachkontrollieren; besonders die
Erfassungszeiträume ändern sich laufend.
