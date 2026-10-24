# Combi Boiler Visual Card

An animated, dependency-free Lovelace card that draws a combi boiler installation and
animates it with live Home Assistant data.

![Combi Boiler Visual Card](docs/screenshot.png)

The card was originally written as a single `custom:button-card` template
(see [`boiler kaart.yaml`](boiler%20kaart.yaml)) and is now available as a standalone
custom element, so `button-card` is no longer required.

## Features

- Full boiler schematic (boiler, plate heat exchanger, three-way valve, pump, fan,
  expansion vessel, radiator, tap and pipework) rendered as inline SVG.
- Live values: flow/return temperature, ΔT, system pressure, DHW temperature,
  tap flow, water meter flow, pump modulation and burner power.
- Animated pipe flow, spinning pump and fan, glowing radiator and coloured burner,
  all driven by the actual sensor states.
- Everything is configurable — point the card at your own entities.
- Click a value badge to open the normal Home Assistant more-info dialog.
- Honours `prefers-reduced-motion`.
- No external dependencies, no build step needed at runtime.

## Installation

### HACS (recommended)

1. Make sure [HACS](https://hacs.xyz) is installed.
2. In HACS open the menu (⋮) → **Custom repositories**.
3. Add `https://github.com/boas-de-lely/combi-boiler-visual-card` with category **Dashboard**.
4. Search for **Combi Boiler Visual Card** in HACS and download it.
5. Reload your browser (hard refresh) and add the card to a dashboard.

### Manual

1. Download `combi-boiler-visual-card.js` from the [latest release](../../releases/latest).
2. Copy it to `/config/www/community/combi-boiler-visual-card/combi-boiler-visual-card.js`.
3. Add the resource **Settings → Dashboards → ⋮ → Resources**:

   ```yaml
   url: /local/community/combi-boiler-visual-card/combi-boiler-visual-card.js
   type: module
   ```

4. Reload your browser and add the card.

## Configuration

```yaml
type: custom:combi-boiler-visual-card
entities:
  flow_temperature: sensor.boiler_current_flow_temperature
  return_temperature: sensor.boiler_return_temperature
  system_pressure: sensor.boiler_system_pressure
  dhw_temperature: sensor.boiler_dhw_current_intern_temperature
  dhw_flow: sensor.boiler_dhw_current_tap_water_flow
  water_flow: sensor.watermeter_watermeter_flow
  pump_modulation: sensor.boiler_heating_pump_modulation
  burner_power: sensor.boiler_burner_current_power
  gas: binary_sensor.boiler_gas
  flame_current: sensor.boiler_flame_current
  heating_active: binary_sensor.boiler_heating_active
  heating_pump: binary_sensor.boiler_heating_pump
  dhw_active: binary_sensor.boiler_dhw_active
  dhw_pump: binary_sensor.boiler_dhw_charge_pump
  fan: binary_sensor.boiler_fan
tap_flow_threshold: 0.05
```

`type` is the only required option. Every entity key is optional and falls back to the
defaults shown below, so you only need to list the ones that differ.

| Key | Default | Description |
| --- | --- | --- |
| `flow_temperature` | `sensor.boiler_current_flow_temperature` | Central heating flow temperature |
| `return_temperature` | `sensor.boiler_return_temperature` | Central heating return temperature |
| `system_pressure` | `sensor.boiler_system_pressure` | System water pressure |
| `dhw_temperature` | `sensor.boiler_dhw_current_intern_temperature` | Domestic hot water temperature |
| `dhw_flow` | `sensor.boiler_dhw_current_tap_water_flow` | Domestic hot water flow rate |
| `water_flow` | `sensor.watermeter_watermeter_flow` | Water meter flow rate |
| `pump_modulation` | `sensor.boiler_heating_pump_modulation` | Heating pump modulation |
| `burner_power` | `sensor.boiler_burner_current_power` | Current burner power |
| `gas` | `binary_sensor.boiler_gas` | Gas valve state |
| `flame_current` | `sensor.boiler_flame_current` | Flame ionisation current |
| `heating_active` | `binary_sensor.boiler_heating_active` | Central heating demand |
| `heating_pump` | `binary_sensor.boiler_heating_pump` | Central heating pump |
| `dhw_active` | `binary_sensor.boiler_dhw_active` | Domestic hot water demand |
| `dhw_pump` | `binary_sensor.boiler_dhw_charge_pump` | DHW charge pump |
| `fan` | `binary_sensor.boiler_fan` | Fan state |

| Option | Default | Description |
| --- | --- | --- |
| `tap_flow_threshold` | `0.05` | Flow rate (in the sensor's unit) above which the tap is considered open |

### Minimal example

```yaml
type: custom:combi-boiler-visual-card
entities:
  flow_temperature: sensor.my_boiler_flow
  return_temperature: sensor.my_boiler_return
  dhw_active: binary_sensor.my_boiler_dhw
```

## What the animations mean

| Visual | Active when |
| --- | --- |
| Red/blue dots through the radiator loop | Heating demand, heating pump running and no DHW demand |
| Red/blue dots through the plate heat exchanger | DHW demand, or tap flow above `tap_flow_threshold` |
| Yellow dots through the gas pipe | `gas` is `on` |
| Orange burner and bright flame | `gas` is `on` and `flame_current` is above 0 |
| Spinning impeller and blue pump housing | `heating_pump` or `dhw_pump` is `on` |
| Spinning fan | `fan` is `on` |
| Red glow around the radiator | Heating demand and no DHW demand |
| Running tap water | DHW demand or tap flow above the threshold |

Values that are `unknown`, `unavailable` or missing are shown as `—`.

## Development

The source lives in `src/combi-boiler-visual-card.js` and is copied verbatim (with a
banner) into `dist/combi-boiler-visual-card.js`, which is the file HACS installs.

```bash
npm run build   # rebuild dist/ from src/
npm run check   # syntax check
```

`dist/` is committed, and CI fails when it is out of sync with `src/`.

## License

[MIT](LICENSE)
