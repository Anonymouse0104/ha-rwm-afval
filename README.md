<div align="center">

<img src="custom_components/rwm_afval/brand/icon.png" width="128" alt="RWM Afvalkalender icon">

# RWM Afvalkalender

[![Open in HACS](https://my.home-assistant.io/badges/hacs_repository.svg)](https://my.home-assistant.io/redirect/hacs_repository/?owner=Anonymouse0104&repository=ha-rwm-afval&category=integration) [![Ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/anonymouse0501)

[Nederlands](#nederlands) | [English](#english)

</div>

## Nederlands

Ophaaldagen van **RWM afval & reiniging** (Beek, Echt-Susteren, Sittard-Geleen, Stein) als sensoren en kalender in Home Assistant.

De data komt **rechtstreeks van rwm.nl**, dezelfde bron als de afvalkalender op de website. Er zit geen tussenserver van derden tussen.

> **Geen officiële RWM-integratie.** Dit is een onafhankelijk hobbyproject en wordt niet gemaakt, ondersteund of goedgekeurd door RWM afval & reiniging. Vragen of problemen? Meld ze hier via GitHub Issues, niet bij RWM.

### Installatie via HACS

1. Open HACS en zoek naar **RWM Afvalkalender**
2. Installeer de integratie en herstart Home Assistant
3. **Instellingen → Apparaten & diensten → Integratie toevoegen → RWM Afvalkalender**
4. Vul postcode en huisnummer in. Woon je in Echt-Susteren, kies dan ook je kern voor oud papier.

Meerdere adressen? Voeg de integratie gewoon nog een keer toe.

### Wat je krijgt

| Entiteit | Inhoud |
|---|---|
| `sensor.rwm_afvalkalender_<afvalsoort>` | Eerstvolgende datum per afvalsoort (GFT, restafval, PMD, ...), met attributen `dagen_tot_ophaling` en `volgende_ophalingen` |
| `sensor.rwm_afvalkalender_<afvalsoort>_dagen_tot_ophaling` | Aantal dagen tot de volgende ophaling per afvalsoort (0 = vandaag, 1 = morgen) |
| `sensor.rwm_afvalkalender_volgende_ophaling` | Wat er als eerste wordt opgehaald, met `datum` en `dagen_tot_ophaling` |
| `sensor.rwm_afvalkalender_ophaling_vandaag` | Afvalsoort(en) van vandaag, of `Geen` |
| `sensor.rwm_afvalkalender_ophaling_morgen` | Afvalsoort(en) van morgen, of `Geen` |
| `calendar.rwm_afvalkalender` | Alle ophaaldagen als hele-dag-afspraken |

De exacte entity-id's kunnen per installatie verschillen. Je vindt ze onder het apparaat **RWM Afvalkalender**.

### Voorbeeld: herinnering de avond ervoor

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
            entity_id: sensor.rwm_afvalkalender_ophaling_morgen
            state: "Geen"
    actions:
      - action: notify.notify   # vervang door je eigen notify-service
        data:
          message: "Morgen wordt opgehaald: {{ states('sensor.rwm_afvalkalender_ophaling_morgen') }}"
```

#### Herinnering via WhatsApp

Liever een appje dan een pushmelding? Met [WhatsApp Notifier](https://github.com/Anonymouse0104/ha-whatsapp-notifier) stuur je dezelfde herinnering naar jezelf of de huisgroep, zonder externe dienst. Vervang in het voorbeeld hierboven de `notify.notify` regel door `notify.send_message` met de notify-entiteit van je chat, of door `whatsapp_notifier.send_message`. Antwoordt iemand, dan kan de herinnering stoppen.

### Oud papier in Echt-Susteren

RWM levert oud papier voor Echt-Susteren niet als kalenderdata. Op de RWM-site staat alleen een tekst per kern, zoals "1e zaterdag van de maand" of "laatste maandag". De integratie leest die tekst uit en rekent er datums van, inclusief de uitzonderingen die RWM vermeldt ("de ophaaldag van 16/02 wordt vervangen door 09/02"). Uitzonderingen zonder jaartal gelden alleen voor het lopende jaar.

Je kern wijzigen: **Apparaten & diensten → RWM Afvalkalender → Configureren**.

### Icoon

Het icoon in Home Assistant is een eigen ontwerp en heeft niets met het logo van RWM te maken. Het wordt getoond vanaf Home Assistant 2026.3. Op oudere versies zie je een standaardicoon.

### Betrouwbaarheid

- De data wordt twee keer per dag ververst.
- Is RWM even niet bereikbaar, dan blijft de laatst bekende data actief, ook na een herstart. In de attributen zie je via `laatst_bijgewerkt` hoe oud de data is.
- Na een mislukte poging probeert de integratie het elke 15 minuten opnieuw, tot het weer lukt. Daarna geldt weer het normale ritme van twee keer per dag. Een storing geeft één waarschuwing in het logboek, niet elk kwartier een nieuwe.
- RWM heeft geen officiële API. Verandert RWM de website, dan moet de integratie mogelijk worden aangepast. De GitHub Action draait elke week de tests.

### Nieuwe versie uitbrengen (voor de beheerder)

1. Pas `version` aan in `custom_components/rwm_afval/manifest.json` (bijvoorbeeld `1.2.3`)
2. Commit en push naar `main`
3. Wacht tot de workflow **Valideren** groen is (tests, hassfest en HACS-controle)
4. Maak een tag met dezelfde versie en push alleen die tag:

   ```bash
   git tag v1.2.3
   git push origin v1.2.3
   ```

De workflow **Release** controleert of de tag gelijk is aan de versie in `manifest.json` en maakt dan automatisch een GitHub-release. HACS toont de update daarna bij iedereen die de integratie heeft.

### Steun

Bespaart deze integratie je tijd, dan kun je me [steunen op Ko-fi](https://ko-fi.com/anonymouse0501). Het is helemaal optioneel en de integratie blijft in beide gevallen gratis.

---

## English

Collection days from **RWM afval & reiniging** (Beek, Echt-Susteren, Sittard-Geleen, Stein, the Netherlands) as sensors and a calendar in Home Assistant.

The data comes **directly from rwm.nl**, the same source as the waste calendar on the website. There is no third-party server in between.

> **Not an official RWM integration.** This is an independent hobby project and is not made, supported or endorsed by RWM afval & reiniging. Questions or problems? Report them here via GitHub Issues, not to RWM.

### Install via HACS

1. Open HACS and search for **RWM Afvalkalender**
2. Install the integration and restart Home Assistant
3. **Settings → Devices & services → Add integration → RWM Afvalkalender**
4. Enter your postcode and house number. If you live in Echt-Susteren, also pick your village for old paper.

Multiple addresses? Just add the integration again.

### What you get

| Entity | Content |
|---|---|
| `sensor.rwm_afvalkalender_<waste_type>` | Next date per waste type (GFT, residual waste, PMD, ...), with attributes `dagen_tot_ophaling` and `volgende_ophalingen` |
| `sensor.rwm_afvalkalender_<waste_type>_dagen_tot_ophaling` | Number of days until the next collection per waste type (0 = today, 1 = tomorrow) |
| `sensor.rwm_afvalkalender_volgende_ophaling` | What is collected first, with `datum` and `dagen_tot_ophaling` |
| `sensor.rwm_afvalkalender_ophaling_vandaag` | Waste type(s) of today, or `Geen` (none) |
| `sensor.rwm_afvalkalender_ophaling_morgen` | Waste type(s) of tomorrow, or `Geen` (none) |
| `calendar.rwm_afvalkalender` | All collection days as all-day events |

The exact entity IDs can differ per installation. You find them under the device **RWM Afvalkalender**.

### Example: reminder the evening before

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
            entity_id: sensor.rwm_afvalkalender_ophaling_morgen
            state: "Geen"
    actions:
      - action: notify.notify   # replace with your own notify service
        data:
          message: "Collected tomorrow: {{ states('sensor.rwm_afvalkalender_ophaling_morgen') }}"
```

#### Reminder via WhatsApp

Prefer a chat message over a push notification? With [WhatsApp Notifier](https://github.com/Anonymouse0104/ha-whatsapp-notifier) you send the same reminder to yourself or the household group, without a third-party service. In the example above, replace the `notify.notify` line with `notify.send_message` and the notify entity of your chat, or with `whatsapp_notifier.send_message`. If someone replies, the reminder can stop.

### Old paper in Echt-Susteren

RWM does not provide old paper collection for Echt-Susteren as calendar data. The RWM site only has a text per village, such as "1st Saturday of the month" or "last Monday". The integration reads that text and calculates dates from it, including the exceptions RWM mentions ("the collection of 16/02 is replaced by 09/02"). Exceptions without a year apply to the current year only.

To change your village: **Devices & services → RWM Afvalkalender → Configure**.

### Icon

The icon in Home Assistant is an original design and has nothing to do with the RWM logo. It is shown from Home Assistant 2026.3. On older versions you see a default icon.

### Reliability

- The data is refreshed twice a day.
- If RWM is briefly unreachable, the last known data stays active, also after a restart. The attribute `laatst_bijgewerkt` shows how old the data is.
- After a failed attempt the integration retries every 15 minutes until it works again. After that the normal rhythm of twice a day applies. An outage gives one warning in the log, not a new one every quarter of an hour.
- RWM has no official API. If RWM changes the website, the integration may need to be updated. The GitHub Action runs the tests every week.

### Releasing a new version (for the maintainer)

1. Change `version` in `custom_components/rwm_afval/manifest.json` (for example `1.2.3`)
2. Commit and push to `main`
3. Wait until the **Valideren** workflow is green (tests, hassfest and HACS check)
4. Create a tag with the same version and push only that tag:

   ```bash
   git tag v1.2.3
   git push origin v1.2.3
   ```

The **Release** workflow checks that the tag equals the version in `manifest.json` and then creates a GitHub release automatically. HACS then shows the update to everyone who has the integration.

### Support

If this integration saves you some time, you can [support me on Ko-fi](https://ko-fi.com/anonymouse0501). It's entirely optional, and the integration stays free either way.
