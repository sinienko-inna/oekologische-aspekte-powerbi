# Wie der Bericht entstanden ist

Dokumentation des ökologischen Teils (Teil III) des Teamprojekts
**Die Energiewende in Deutschland: wirtschaftliche, strukturelle und ökologische Aspekte.**

Der Ablauf ist aus dem Power-BI-Modell, den Rohdateien und den Berichtsseiten rekonstruiert. Der Bericht (`.pbix`) ist vollständig von mir erstellt.

## 1. Aufgabenstellung

Das Team hat die Energiewende in drei Schichten geteilt:

| Teil | Inhalt | Werkzeug |
|---|---|---|
| I | Strukturelle Aspekte (Energiequellen, Aufkommen) | Tableau / SQL (Kolleginnen/Kollegen) |
| II | Ökonomische Aspekte (Strommarkt, Preise) | Python, energy-charts (Kolleginnen/Kollegen) |
| III | Ökologische Aspekte | Power Query, Power BI — **dieser Bericht** |

Meine drei Leitfragen:

1. Wie entwickeln sich Treibhausgasemissionen international und in Deutschland – und welche Klimaziele stehen dahinter?
2. Wie hängen Dekarbonisierung, Wirtschaft (BIP, Sektoren) und Energiewende zusammen?
3. Welche Lenkungswirkung hat der CO₂-Preis in der Energiewirtschaft, wenn ökologische und energiewirtschaftliche Daten verknüpft werden?

Zeitraum der Analyse: im Kern **1990–2024** (BIP bis 2030 als Ausblick; CO₂-Preis-Schnittstelle 2015–2025).

## 2. Daten beschaffen

| Bedarf | Vorgehen | Ergebnisdatei |
|---|---|---|
| THG nach Land, Sektor, Substanz | Download EDGAR (JRC), Report 2025 | `data/raw/EDGAR_GHG.xlsx` |
| Bevölkerung | Our World in Data, Reihe *population-long-run-with-projections* | `data/raw/Abfrage1.xlsx`, Blatt 1 |
| BIP Deutschland | Statista, GDP in Germany (inkl. Projektion bis 2030) | `data/raw/Abfrage1.xlsx`, Blatt 2 |
| Politische Ziele und KPI | Kein fertiger Datensatz: Gesetze, Richtlinien und Zielwerte für **EU** und **Deutschland** selbst recherchiert und mit KI-Unterstützung als Katalog strukturiert | `data/raw/politische Maßnahme.xlsx` |
| Strom-CO₂, EE-Anteil, CO₂-Preis | Vom Team geladen (energy-charts.info), nicht selbst erhoben | nur im `.pbix`, siehe [data/team-context](../data/team-context/README.md) |

Quellen und Lizenzhinweise: [README](../README.md#quellen) und [data/raw](../data/raw/README.md).

## 3. Aufbereitung in Power Query

Die Excel-/CSV-Quellen wurden in Power BI als Abfragen geladen (Import). Typische Schritte:

- Spalten- und Ländernamen vereinheitlichen (`Country Code` / DEU)
- Datentypen (Jahr, Beträge)
- Filter auf den Analysezeitraum, wo nötig
- Dimensionen von Fakten trennen (Land, Sektor, THG-Substanz, Jahr)
- Ländergruppen (EU-27, G7) über eine Brückentabelle, weil ein Land mehreren Gruppen angehören kann
- Maßnahmenkatalog in KPI-Dimension, Maßnahmentabelle und EU-/DE-Umsetzung aufteilen

Die Teamtabellen wurden **unverändert geladen** und nur über Jahr bzw. Energieträger verknüpft — ohne eigene Transformation der Strommarktlogik.

## 4. Datenmodell

Sternschema: Fakten in der Mitte, Dimensionen außen, Measures in `Kennzahlen`.  
Details: [02-data-model.md](02-data-model.md), Screenshot: [screenshots/Datenmodell.png](../screenshots/Datenmodell.png).

Bewusst **nicht** direkt Fakt an Fakt gebunden. Gemeinsame Zeitachse ist `dim_year`, damit ökologische Reihen und Teamdaten zum CO₂-Preis zusammengehören, ohne die Tabellen zu vermischen.

## 5. Kennzahlen und Visualisierung

Measures liegen in der Tabelle `Kennzahlen` (DAX). Die Formeln stehen in der `.pbix`; in den Seiten sichtbar sind u. a.:

- THG-Änderung zum Basisjahr (Vergleich EU-27 / G7)
- THG pro Kopf
- Kettenwachstumsraten THG und BIP
- Intensität t CO₂eq je USD BIP
- THG-Reduktion vs. 1990 (−47 % / −52 % / −65 % auf der KPI-Seite)
- Anteil EE, CO₂-Preisänderung vs. 2010 (KPI-Seite, teils Teamdaten)

Seitenfolge und Screenshots: [screenshots/README.md](../screenshots/README.md).

| Seite | Rolle in der Argumentation |
|---|---|
| 1 Weltsituation | Globale Verteilung vs. deutscher Rückgang; politische Zeitachse |
| 2 Vergleich zu Basis | Wirksamkeit relativ zum Basisjahr, G7 vs. EU-27 |
| 3 Weltdynamik | Gas-Mix und Pro-Kopf-Pfad Deutschland |
| 4–5 | Entkopplung THG und BIP (Raten und Niveau) |
| 6 | Sektorale Verschiebung 1990 → 2024 |
| 7 | Schnittstelle: fossile Strom-CO₂ und Lenkungswirkung des CO₂-Preises |
| 8 | Ziele vs. erreichte KPI |

Seite 7 nutzt die Teamtabellen. Alle übrigen Seiten beruhen auf den eigenen Quellen.

## 6. Grenzen

- EDGAR: territoriale Emissionen; Landnutzung und internationale Luft-/Schifffahrt in Länderreihen nicht analog zu allen OWID-CO₂-Reihen.
- BIP: Statista, laufende Preise, Mrd. USD; Intensität ist daher eine grobe Relation, kein preisbereinigtes offizielles Intensitätsmaß des UBA.
- Maßnahmenkatalog: selbst zusammengestellt, keine amtliche Vollständigkeitsgarantie.
- Teamdaten auf Seite 7: Korrelation / Streuung 2015–2025, keine kausale Marktstudie (die liegt in Teil II).
- Der Bericht ist interaktiv; GitHub zeigt statische Screenshots. Die `.pbix` bleibt die Arbeitsdatei.
