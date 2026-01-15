# HA Total Climate Card

A comprehensive Home Assistant custom card for total climate control featuring real-time thermal comfort monitoring, multi-zone thermostat management, dehumidifier control, and environmental tracking.

![Total Climate Card](screenshots/main-card.png)

## Features

### 🌡️ **Thermal Comfort Monitoring**
- **Real "Feels Like" Temperature** - Indoor and outdoor heat index display
- Actual perceived temperature based on temp + humidity
- Perfect for migraine sufferers and comfort optimization

### 🏠 **Multi-Zone Climate Control**
- Control multiple thermostats from one card
- Independent zone management (Upstairs/Downstairs)
- Real-time temperature and humidity display
- Mode switching (Heat, Cool, Auto, Off)
- Fan control (Auto, On, Circulate)

### 💧 **Dehumidifier Integration**
- Dedicated dehumidifier control
- Target humidity adjustment (5% increments)
- Current vs target humidity display
- Respects entity min/max limits

### 📊 **Environmental Monitoring Buttons**
Smart bottom buttons with color-coded thresholds:
- **Barometric Pressure** - Migraine tracking with alerts
- **Indoor Humidity** - Comfort level monitoring
- **Filter Life** - Days remaining tracker with warnings
- **Air Quality** - Placeholder for CO2/VOC sensors
- **Emergency Heat** - Backup heating control

### 🎨 **Color-Coded Alerts**
- **Green** - Optimal conditions
- **Yellow** - Warning thresholds
- **Red** - Critical levels (with pulsing animation)

### 📱 **Mobile Compatible**
- Works on Home Assistant mobile app
- Responsive design for all screen sizes
- Touch-optimized controls

### ☁️ **Weather Integration**
- Current conditions display
- 7-day forecast
- One-click access to detailed weather popup

## Installation

### Method 1: HACS (Recommended)
1. Open HACS in Home Assistant
2. Click on "Frontend"
3. Click the 3 dots menu (top right)
4. Select "Custom repositories"
5. Add this repository URL
6. Click "Install"
7. Restart Home Assistant

### Method 2: Manual Installation
1. Download `ha-total-climate-card.js`
2. Copy to `/config/www/ha-total-climate-card.js`
3. Add to your Lovelace resources:
   ```yaml
   resources:
     - url: /local/ha-total-climate-card.js
       type: module
   ```
4. Restart Home Assistant

## Requirements

### Required Integrations
- **Home Assistant Climate Integration** (thermostats)
- **Weather Integration** (any - Pirate Weather, Met.no, etc.)

### Recommended Integrations
- **[Thermal Comfort](https://github.com/dolezsa/thermal_comfort)** - For "feels like" temperature calculation
- **Humidifier/Dehumidifier Integration** - For humidity control

## Configuration

### Basic Configuration

```yaml
type: custom:ha-total-climate-card
name: Family Room
thermostats:
  - entity: climate.upstairs_thermostat
    name: Upstairs
  - entity: climate.downstairs_thermostat
    name: Downstairs
  - entity: humidifier.dehumidifier
    name: Dehumidifier Control
weather_entity: weather.home
outdoor_temperature: sensor.outdoor_temperature
outdoor_humidity: sensor.outdoor_humidity
```

### Full Configuration with All Features

```yaml
type: custom:ha-total-climate-card
name: Total Climate Control
thermostats:
  - entity: climate.upstairs_thermostat
    name: Upstairs
  - entity: climate.downstairs_thermostat
    name: Downstairs
  - entity: humidifier.dehumidifier
    name: Dehumidifier Control
weather_entity: weather.pirateweather
outdoor_temperature: sensor.outdoor_temperature
outdoor_humidity: sensor.outdoor_humidity
indoor_heat_index: sensor.thermal_comfort_heat_index
outdoor_heat_index: sensor.thermal_comfort_heat_index_2
bottom_buttons:
  - label: PRESSURE
    icon: 🌡️
    entity: weather.pirateweather
    attribute: pressure
    unit: " inHg"
    decimal_places: 2
    thresholds:
      warning_low: 29.5
      warning_high: 30.2
      critical_low: 29.0
      critical_high: 30.5
    tap_action:
      action: more-info
  - label: HUMIDITY
    icon: 💧
    entity: climate.upstairs_thermostat
    attribute: current_humidity
    unit: "%"
    thresholds:
      warning_low: 35
      warning_high: 65
      critical_low: 25
      critical_high: 75
    tap_action:
      action: more-info
  - label: FILTER
    icon: 🔧
    entity: input_number.filter_days_remaining
    unit: " days"
    thresholds:
      warning_low: 30
      critical_low: 15
    tap_action:
      action: call-service
      service: input_number.set_value
      service_data:
        entity_id: input_number.filter_days_remaining
        value: 90
  - label: AIR QUAL
    icon: 💨
    entity: sensor.co2_sensor
  - label: EMERG HEAT
    icon: 🔥
    entity: switch.emergency_heat
cards:
  - show_current: true
    show_forecast: true
    type: weather-forecast
    entity: weather.pirateweather
    forecast_type: twice_daily
```

### Configuration Options

#### Card Options
| Option | Type | Required | Description |
|--------|------|----------|-------------|
| `type` | string | Yes | Must be `custom:ha-total-climate-card` |
| `name` | string | Yes | Card title |
| `thermostats` | list | Yes | List of climate entities |
| `weather_entity` | string | Yes | Weather entity for forecast |
| `outdoor_temperature` | string | No | Outdoor temperature sensor |
| `outdoor_humidity` | string | No | Outdoor humidity sensor |
| `indoor_heat_index` | string | No | Indoor thermal comfort sensor |
| `outdoor_heat_index` | string | No | Outdoor thermal comfort sensor |
| `bottom_buttons` | list | No | Custom monitoring buttons |
| `cards` | list | No | Weather forecast card config |

#### Bottom Button Options
| Option | Type | Required | Description |
|--------|------|----------|-------------|
| `label` | string | Yes | Button label text |
| `icon` | string | No | Emoji or icon |
| `entity` | string | Yes | Entity to monitor |
| `attribute` | string | No | Specific attribute to display |
| `unit` | string | No | Unit suffix (e.g., " °F", "%") |
| `decimal_places` | number | No | Decimal precision |
| `thresholds` | object | No | Color threshold settings |
| `tap_action` | object | No | Action on button click |

#### Threshold Options
```yaml
thresholds:
  warning_low: 30      # Yellow below this value
  warning_high: 70     # Yellow above this value
  critical_low: 20     # Red below this value
  critical_high: 80    # Red above this value
```

## Optional Automations

The `automations/` folder contains optional automation YAML files you can import:

### Smart Comfort Control
Automatically maintains your target "feels like" temperature based on thermal comfort sensors.

**Features:**
- Runs 6pm-6am automatically
- Adjusts thermostat based on actual comfort level
- Voice control compatible
- Manual override switch

[See automations/smart-comfort-control.yaml](automations/smart-comfort-control.yaml)

### Filter Life Tracker
Tracks HVAC filter life with countdown and replacement reminders.

**Features:**
- Daily automatic countdown
- Color-coded warnings (90 days = green, 30 days = yellow, 15 days = red)
- One-click reset after filter replacement

[See automations/filter-life-tracker.yaml](automations/filter-life-tracker.yaml)

## Thermal Comfort Setup

For "feels like" temperature displays, install the Thermal Comfort custom integration:

1. Install via HACS or manually from: https://github.com/dolezsa/thermal_comfort
2. Add to `configuration.yaml`:

```yaml
sensor:
  - platform: thermal_comfort
    sensors:
      indoor_comfort:
        friendly_name: Indoor Thermal Comfort
        temperature_sensor: sensor.indoor_temperature
        humidity_sensor: sensor.indoor_humidity
      
      outdoor_comfort:
        friendly_name: Outdoor Thermal Comfort
        temperature_sensor: sensor.outdoor_temperature
        humidity_sensor: sensor.outdoor_humidity
```

3. Restart Home Assistant
4. Add entity IDs to card config

## Troubleshooting

### Card Not Loading
- Verify the JS file is in `/config/www/`
- Check browser console (F12) for errors
- Clear browser cache (Ctrl+Shift+R)
- Verify resource is added to Lovelace

### Mobile App Not Working
- Clear mobile app cache: Settings → Companion App → Reset Frontend Cache
- Ensure you're using event listeners (not inline onclick handlers)

### "Feels Like" Shows "--"
- Verify Thermal Comfort integration is installed
- Check entity IDs match your configuration
- Ensure temperature and humidity sensors are working

### Buttons Not Changing Color
- Verify thresholds are configured correctly
- Check entity is returning numeric values
- Review threshold logic in configuration

## Credits

Created by **Traci S Aaron (Mystic369)**

Built with collaboration and assistance from Claude (Anthropic).

Special thanks to the Home Assistant community for testing and feedback!

## Support

- 🐛 [Report Issues](https://github.com/Mystic369/ha-total-climate-card/issues)
- 💬 [Home Assistant Community Forum](https://community.home-assistant.io/)
- 📘 [Home Assistant Facebook Group](https://www.facebook.com/groups/HomeAssistant/)

## License

MIT License - Feel free to use, modify, and share!

## Changelog

### v1.0.0 (January 2026)
- Initial release
- Multi-zone thermostat control
- Thermal comfort monitoring
- Dehumidifier control
- Smart bottom buttons with thresholds
- Mobile app compatibility
- Weather integration
