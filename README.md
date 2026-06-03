# Wall Panels (ESPHome)

ESPHome-config voor twee Waveshare ESP32-S3 Touch-LCD-4 wandpanelen die via de
`remote_webview`-component een Home Assistant-dashboard streamen (server draait op
de NAS, 192.168.1.135:8181).

## Structuur
- `common/waveshare-panel.yaml` - gedeelde basis (package); hier zit alle logica.
- `waveshare-panel-1.yaml` / `waveshare-panel-2.yaml` - dunne per-paneel files die
  alleen substitutions zetten (naam, ruimte, dashboard-url) en de basis includen.
- `secrets.yaml.example` - sjabloon; kopieer naar `secrets.yaml` en vul in.
- `.gitignore` - houdt `secrets.yaml` en build-artefacten buiten git.

## Installatie (ESPHome add-on)
Plaats in je ESPHome-configmap (`/config/esphome/`):

    waveshare-panel-1.yaml
    waveshare-panel-2.yaml
    secrets.yaml            (niet in git!)
    common/waveshare-panel.yaml

1. Kopieer `secrets.yaml.example` naar `secrets.yaml` en vul je waarden in.
2. Compileer/flash `waveshare-panel-1.yaml` en `waveshare-panel-2.yaml`.

## Geheimen (secrets.yaml)
`wifi_ssid`, `wifi_password`, `api_key`, `ota_password`, `ap_password`.
De oude config had de api-key en de OTA-/AP-wachtwoorden hardcoded en gedeeld -
genereer/roteer ze opnieuw. De twee panelen delen nu dezelfde `api_key`; wil je
per paneel een unieke key, splits dan naar aparte secrets en zet `api:` per paneel.

## Verbeteringen t.o.v. het origineel
- Secrets uit de YAML naar `secrets.yaml` (`!secret`).
- Een gedeelde base-package i.p.v. twee bijna-identieke files.
- Herstartknop en namen geparametriseerd per ruimte/paneel.
- `logger` op INFO (was DEBUG).
- `api: reboot_timeout: 0s` zodat een paneel niet reboot als HA even wegvalt.
- Externe componenten staan nog op PR-branches; overweeg te pinnen op een vaste commit.

## Aandachtspunten
- Controleer voor paneel 2 de `room_name` en het dashboard-pad (`dashboard-hadash2`).
- IP's (HA 192.168.1.52, NAS 192.168.1.135) staan in de substitutions; pas aan als ze wijzigen.
