# Teamkontext (nicht eigener Analysegegenstand)

Dieser Ordner dokumentiert Tabellen, die **nicht von mir modelliert oder ausgewertet** wurden.

Sie stammen aus den Teamteilen

- Teil I – strukturelle Aspekte (Energiequellen / Aufkommen)
- Teil II – ökonomische Aspekte (Energiepreise, Strommarkt)

Ich habe sie in mein Power-BI-Modell **nur geladen**, um die Verbindung zum ökologischen Teil (Teil III) zu zeigen. Die Verknüpfung läuft über das Jahr (`dim_year`) bzw. den Energieträger (`ID_Energie`).

## Tabellen

| Tabelle im Modell | Inhalt | Herkunft |
|---|---|---|
| `F_fact_Anteil EE_Strome…` | Anteil erneuerbarer Energien an der Laststromerzeugung | energy-charts.info, bereitgestellt durch das Team |
| `F_fact_CO2_Strom_Quar…` | CO₂-Emissionen der Stromerzeugung nach Energieträger/Quartal | energy-charts.info, bereitgestellt durch das Team |
| `F_fact_CO2-Preise_Mon…` | CO₂-Preis (monatlich) | Team / energy-charts.info |
| `dim_CO2_Fossile Energi…` | Dimension Energieträger | Team |

## Verwendung in meinem Bericht

Nur **zwei Visuals** nutzen diese Teamdaten (Schnittstelle, keine eigene Marktanalyse):

1. Zusammenhang zwischen der Emissionsstruktur der fossilen Stromerzeugung und dem CO₂-Preis (2015–2025)
2. Lenkungswirkung des CO₂-Preises in der Energiewirtschaft in Deutschland (2015–2025)

Bevölkerung, BIP, Treibhausgasemissionen nach Wirtschaftssektoren, EU-Maßnahmen/KPI sowie das restliche Datenmodell sind **meine eigene Arbeit**.

Rohdateien der Teamkolleginnen/-kollegen (z. B. Jupyter-Notebooks, stündliche Strommarktdaten) liegen **nicht** in diesem Repository.
