# RWM Afvalkalender voor Home Assistant

Ophaaldagen van **RWM afval \& reiniging** (Beek, Echt-Susteren, Sittard-Geleen, Stein) als sensoren en kalender in Home Assistant.

De data komt **rechtstreeks van rwm.nl**, dezelfde bron als de afvalkalender op de website. Er zit geen tussenserver van derden tussen.

## Installatie via HACS

1. HACS → menu (⋮) → **Custom repositories**
2. URL: `https://github.com/Anonymouse0104/ha-rwm-afval`, type **Integration**
3. Zoek **RWM Afvalkalender**, installeer en herstart Home Assistant
4. **Instellingen → Apparaten \& diensten → Integratie toevoegen → RWM Afvalkalender**
5. Vul postcode en huisnummer in. Woon je in Echt-Susteren, kies dan ook je kern voor oud papier.

Meerdere adressen? Voeg de integratie gewoon nog een keer toe.

## Wat je krijgt

|Entiteit|Inhoud|
|-|-|
|`sensor.rwm\\\_afvalkalender\\\_<afvalsoort>`|Eerstvolgende datum per afvalsoort (GFT, restafval, PMD, ...), met attributen `dagen\\\_tot\\\_ophaling` en `volgende\\\_ophalingen`|
|`sensor.rwm\\\_afvalkalender\\\_volgende\\\_ophaling`|Wat er als eerste wordt opgehaald, met `datum` en `dagen\\\_tot\\\_ophaling`|
|`sensor.rwm\\\_afvalkalender\\\_ophaling\\\_vandaag`|Afvalsoort(en) van vandaag, of `Geen`|
|`sensor.rwm\\\_afvalkalender\\\_ophaling\\\_morgen`|Afvalsoort(en) van morgen, of `Geen`|
|`calendar.rwm\\\_afvalkalender`|Alle ophaaldagen als hele-dag-afspraken|

De exacte entity-id's kunnen per installatie verschillen. Je vindt ze onder het apparaat **RWM Afvalkalender**.

## Voorbeeld: herinnering de avond ervoor

```yaml
automation:
  - alias: Afval buiten zetten
    triggers:
      - trigger: time
        at: "19:30:00"
    conditions:
      - condition: not
        conditions:
          - condition: state
            entity\\\_id: sensor.rwm\\\_afvalkalender\\\_ophaling\\\_morgen
            state: "Geen"
    actions:
      - action: notify.notify   # vervang door je eigen notify-service
        data:
          message: "Morgen wordt opgehaald: {{ states('sensor.rwm\\\_afvalkalender\\\_ophaling\\\_morgen') }}"
```

## Oud papier in Echt-Susteren

RWM levert oud papier voor Echt-Susteren niet als kalenderdata. Op de RWM-site staat alleen een tekst per kern, zoals "1e zaterdag van de maand" of "laatste maandag". De integratie leest die tekst uit en rekent er datums van, inclusief de uitzonderingen die RWM vermeldt ("de ophaaldag van 16/02 wordt vervangen door 09/02"). Uitzonderingen zonder jaartal gelden alleen voor het lopende jaar.

Je kern wijzigen: **Apparaten \& diensten → RWM Afvalkalender → Configureren**.

## Betrouwbaarheid

* De data wordt twee keer per dag ververst.
* Is RWM even niet bereikbaar, dan blijft de laatst bekende data actief, ook na een herstart. In de attributen zie je via `laatst\\\_bijgewerkt` hoe oud de data is.
* RWM heeft geen officiële API. Verandert RWM de website, dan moet de integratie mogelijk worden aangepast. De GitHub Action draait elke week de tests.

## Nieuwe versie uitbrengen (voor de beheerder)

1. Pas `version` aan in `custom\\\_components/rwm\\\_afval/manifest.json` (bijvoorbeeld `1.0.1`)
2. Commit en push
3. Maak een tag met dezelfde versie en push die: `git tag v1.0.1 \\\&\\\& git push --tags`

GitHub maakt dan automatisch een release, en HACS toont de update bij iedereen die de integratie heeft.

