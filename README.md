# Wall Panels (ESPHome)

ESPHome-config voor vier Waveshare ESP32-S3 Touch-LCD-4 wandpanelen die via de
`remote_webview`-component een Home Assistant-dashboard streamen (server draait op
de NAS, 192.168.1.135:8181).

## Panelen
| File | Paneel | Ruimte | Dashboard-pad |
|------|--------|--------|---------------|
| `waveshare-panel-1.yaml` | Panel 1 | Woonkamer | `dashboard-hadash1` |
| `waveshare-panel-2.yaml` | Panel 2 | Speelkamer | `dashboard-hadash2` |
| `waveshare-panel-3.yaml` | Panel 3 | Zolder | `dashboard-hadash3` |
| `waveshare-panel-4.yaml` | Panel 4 | Terras | `dashboard-hadash4` |

## Structuur
- `common/waveshare-panel.yaml` - gedeelde basis (package); hier zit alle logica.
- `waveshare-panel-N.yaml` - dunne per-paneel files die alleen substitutions
  zetten (naam, ruimte, dashboard-url) en de basis includen.
- `secrets.yaml.example` - sjabloon; kopieer naar `secrets.yaml` en vul in.
- `.gitignore` - houdt `secrets.yaml` en build-artefacten buiten git.

## Installatie (ESPHome add-on)
Plaats in je ESPHome-configmap (`/config/esphome/`):

    waveshare-panel-1.yaml ... waveshare-panel-4.yaml
    secrets.yaml            (niet in git!)
    common/waveshare-panel.yaml

1. Kopieer `secrets.yaml.example` naar `secrets.yaml` en vul je waarden in.
2. Compileer/flash elk `waveshare-panel-N.yaml`.
3. Maak in HA de dashboards `dashboard-hadash3` en `dashboard-hadash4` aan voor
   de nieuwe panelen (of pas de `dashboard_url` aan naar bestaande paden).

## Een nieuw paneel toevoegen
Kopieer een bestaande `waveshare-panel-N.yaml`, geef het een uniek `device_name`,
`friendly_name`, `room_name` en `dashboard_url`. Verder niets - de rest komt uit
de gedeelde base.

## Geheimen (secrets.yaml)
`wifi_ssid`, `wifi_password`, `api_key`, `ota_password`, `ap_password`.
De oude config had de api-key en de OTA-/AP-wachtwoorden hardcoded en gedeeld -
genereer/roteer ze opnieuw. Alle panelen delen nu dezelfde `api_key`; wil je per
paneel een unieke key, splits dan naar aparte secrets en zet `api:` per paneel.

## Verbeteringen t.o.v. het origineel
- Secrets uit de YAML naar `secrets.yaml` (`!secret`).
- Een gedeelde base-package i.p.v. bijna-identieke files per paneel.
- Herstartknop en namen geparametriseerd per ruimte/paneel.
- `logger` op INFO (was DEBUG).
- `api: reboot_timeout: 0s` zodat een paneel niet reboot als HA even wegvalt.
- `CONFIG_SPIRAM_XIP_FROM_PSRAM` aan -> geen schermflikkering tijdens OTA.
- gt911 op core (PR #14358 gemerged); alleen de IO-expander (PR #10071) is nog
  een externe component, GEPIND op een vaste commit voor reproduceerbare builds.

## Aandachtspunten
- Controleer per paneel de `room_name` en het dashboard-pad.
- IP's (HA 192.168.1.52, NAS 192.168.1.135) staan in de substitutions; pas aan als ze wijzigen.
- Pin-update: zodra PR #10071 in ESPHome core zit, vervang het externe component
  door de ingebouwde en verwijder de gepinde source.
