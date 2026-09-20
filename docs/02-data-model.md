# Datenmodell

Sternschema im Power-BI-Bericht Teil III.  
Screenshot: [screenshots/Datenmodell.png](../screenshots/Datenmodell.png).

Prozess und Quellen: [01-process.md](01-process.md), [data/raw](../data/raw/README.md).

## Prinzip

- **Fakten** enthalten messbare Größen (eine Zeile = ein Land/Jahr/Sektor bzw. ein Jahr).
- **Dimensionen** enthalten Beschreibungen (Land, Gruppe, Sektor, Substanz, Jahr, KPI).
- **Measures** stehen in `Kennzahlen`, nicht als Spalten in den Fakten.
- Ländergruppen (EU-27, G7) laufen über die Brücke `dim_bridge_country_group` (viele-zu-viele).
- Ökologie und Teamdaten treffen sich über `dim_year` (und bei Strom-CO₂ über `ID_Energie`).

Im Screenshot sind die **Teamtabellen rot umrandet**. Sie wurden nur geladen, um zwei Visuals auf Seite 7 zu ermöglichen.

## Eigene Tabellen

### Dimensionen

| Tabelle | Körnung / Schlüssel | Wichtige Felder | Rolle |
|---|---|---|---|
| `dim_country` | `Country Code` | Ländercode | zentrale Länderdimension |
| `dim_group` | `Group_ID` | `Group_Name` (z. B. EU-27, G7) | Ländergruppen für Slicer |
| `dim_bridge_country_group` | `Country Code` + `Group_ID` | — | Brücke Land ↔ Gruppe |
| `dim_sector` | `ID_Sector` | `Sector` | Wirtschaftssektoren (Energiewirtschaft, Verkehr, Gebäude, …) |
| `dim_THG` | `ID_GHG` | `Substance` (CO₂, CH₄, N₂O, F-Gase) | Treibhausgasart |
| `dim_year` | `Jahr` | — | gemeinsame Zeitachse aller Fakten |
| `dim_Ziel KPI` | `Index` | `KPI` | Katalog der Zielindikatoren (Anteil EE, THG-Minderung, Reaktoren, …) |

### Fakten

| Tabelle | Körnung | Wichtige Felder | Quelle |
|---|---|---|---|
| `fact_THG` | Land × Jahr × Sektor × Substanz | `THG_DE`, `Emission_sector`, `ID_GHG`, `ID_Sector`, `Jahr`, `Country Code` | EDGAR |
| `fact_Bevölk` | Land × Jahr | `Country code`, `Jahr` (+ Bevölkerungszahl aus Blatt 1) | OWID, `Abfrage1.xlsx` Blatt 1 |
| `fact_BIP_DE_99-30` | Jahr (DE) | Bruttoinlandsprodukt, Mrd. USD | Statista, `Abfrage1.xlsx` Blatt 2 |

### Maßnahmen (kein klassisches Star-Fact)

| Tabelle | Inhalt | Quelle |
|---|---|---|
| `Ziel Maßnahme` | `Ebene` (EU/DE), `EU-Maßnahme & Ziel`, `Werte`, `Ziel-Frist`, Verknüpfung zu `dim_Ziel KPI` | `politische Maßnahme.xlsx` |
| `E-Maßnahme` | `Hauptziel`, `Umsetzung in Deutschland (EU)` | dieselbe Datei |

### Measures

| Tabelle | Inhalt |
|---|---|
| `Kennzahlen` | DAX-Measures (Reduktion vs. Basisjahr, Intensität, Indizes, KPI-Werte). Formeln nur in der `.pbix` |

## Teamtabellen (nur Schnittstelle)

Siehe [data/team-context/README.md](../data/team-context/README.md).

| Tabelle | Inhalt | Verknüpfung |
|---|---|---|
| `F_fact_Anteil EE_Strome…` | Anteil EE an der Laststromerzeugung, Jahr | `dim_year` |
| `F_fact_CO2_Strom_Quar…` | CO₂ der Stromerzeugung, `ID_Energie`, Jahr, Quartal | `dim_year`, `dim_CO2_Fossile Energi…` |
| `F_fact_CO2-Preise_Mon…` | Datum, Jahr, Preis €/t (auch 2010/2025-Bezüge) | `dim_year` |
| `dim_CO2_Fossile Energi…` | Energieträger, `ID_Energie` | zu Strom-CO₂-Fakt |

Verwendung im Bericht: nur Seite 7 (zwei Streudiagramme).

## Beziehungen (vereinfacht)

```text
dim_group 1 ──< dim_bridge_country_group >── 1 dim_country
dim_country 1 ──< fact_THG
dim_country 1 ──< fact_Bevölk
dim_sector  1 ──< fact_THG
dim_THG     1 ──< fact_THG
dim_year    1 ──< fact_THG, fact_Bevölk, fact_BIP_DE_99-30
dim_year    1 ──< Ziel Maßnahme (über Jahr / Frist, wo modelliert)
dim_Ziel KPI 1 ──< Ziel Maßnahme
E-Maßnahme     ──  Ziel Maßnahme
dim_year    1 ──< F_fact_Anteil EE…, F_fact_CO2_Strom…, F_fact_CO2-Preise…
dim_CO2_Fossile 1 ──< F_fact_CO2_Strom…
```

Filterrichtung in Power BI: von Dimension zu Fakt (1:*). Die Brücke ermöglicht Slicer EU-27 / G7 auf `fact_THG`, ohne Länder duplizieren zu müssen.

## Warum diese Form

- **Land + Jahr** als Gerüst für internationale Vergleiche (Seiten 1–3).
- **Sektor + Substanz** für die deutsche Struktur 1990 vs. 2024 (Seite 6) und den Gas-Mix (Seite 3).
- **BIP als eigener Fakt nur für DE**, weil die Statista-Reihe national ist, nicht länderscharf wie EDGAR.
- **Maßnahmen getrennt von Emissionen**, damit Ziele (EEG, RED, Kyoto, Green Deal) als Zeitachse neben den Fakten stehen, ohne in EDGAR hineingemischt zu werden.
- **Teamfakten nicht in `fact_THG` mergen**: unterschiedliche Körnung (Quartal/Monat vs. Jahr, Energieträger vs. Wirtschaftssektor). Die Verbindung über `dim_year` reicht für die Lenkungswirkung auf Seite 7.
