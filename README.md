# Brilliant Blueprint Bureau

**Simple automation blueprints for common smart home use cases.**

## Philosophy

Many public blueprints provide extremely detailed settings, allowing for all kinds of complex setups. However, that often results in surprisingly cumbersome configuration even for simple use cases.

These blueprints prioritize simplicity for default scenarios. They won't include every possible feature, but they make the most common use cases straightforward to set up.

---

## Available Blueprints

1. **[Biased Bi-Button Bilresa Blueprint](#biased-bi-button-bilresa-blueprint)** - Full-featured IKEA Bilresa two-button switch controller with dimming
2. **[Ridiculously Redundant Remote Ritual](#ridiculously-redundant-remote-ritual)** - IKEA Rodret/Styrbar remote controller with precise dimming
3. **[Totally Trivial Tradfri Toggle Tool](#totally-trivial-tradfri-toggle-tool)** - IKEA Tradfri five-button remote with toggle, dimming, and color cycling
4. **[Satisfyingly Simple Smart Switch Setup](#satisfyingly-simple-smart-switch-setup)** - Toggle lights/switches with physical wall switches (e.g., Shelly relays)
5. **[Magnificently Minimal Motion Manager](#magnificently-minimal-motion-manager)** - Motion-activated lighting with luminance control and optional time windows

---

## On Dimming

IKEA smart buttons provide smooth dimming within their ecosystem, but replicating this consistently across all Home Assistant light integrations is challenging. These blueprints attempt to recreate that experience, though native Matter binding may eventually provide better solutions.

---

## Installation

### Option 1: Import via Home Assistant UI
1. Copy the blueprint's GitHub raw URL
2. In Home Assistant, navigate to **Settings → Automations & Scenes → Blueprints**
3. Click **Import Blueprint** and paste the URL
4. Click **Preview** and then **Import**

### Option 2: Manual Installation
1. Copy the desired `.yaml` file from the `blueprints/automation/` directory
2. Place it in your Home Assistant `config/blueprints/automation/` directory
3. Restart Home Assistant or reload automations

---

## Biased Bi-Button Bilresa Blueprint

**File:** `biased-bi-button-bilresa-blueprint.yaml`

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/bnutzer/brilliant-blueprint-bureau/main/blueprints/automation/biased-bi-button-bilresa-blueprint.yaml)

### Overview

A comprehensive controller for the IKEA Bilresa two-button Matter-over-Thread switch. The term "biased" refers to the button behavior: Button 1 is biased toward ON/brightness up, while Button 2 is biased toward OFF/brightness down.

In December 2024, IKEA released their first native Matter-over-Thread devices, including the Bilresa two-button switch. Setting up even the most trivial use case (on/off and dimming) required extensive Home Assistant automation configuration. This blueprint simplifies that to just a few clicks.

### Requirements

- **IKEA Bilresa two-button switch** (battery-powered Matter-over-Thread device)
  - ⚠️ Does NOT support the scroll-wheel Bilresa variant
- **Target devices:** Smart lights and/or switches (individual entities or groups)
- **Optional:** Input text helper for precise dimming (recommended for best experience)

### Button Behavior

| Button | Action | Lights | Switches |
|--------|--------|--------|----------|
| **Button 1** | Single press | Turn ON | Turn ON |
| | Double press | Brightness +10% | _(no effect)_ |
| | Long press | Dim UP (smooth transition) | Turn ON |
| | Long release | Stop at current brightness | _(no effect)_ |
| **Button 2** | Single press | Turn OFF | Turn OFF |
| | Double press | Brightness -10% | _(no effect)_ |
| | Long press | Dim DOWN (smooth transition) | Turn OFF |
| | Long release | Stop at current brightness | _(no effect)_ |

### Configuration Options

- **Button 1 Event Entity** - Select the `event.*_button_1` entity from your Bilresa switch
- **Button 2 Event Entity** - Select the `event.*_button_2` entity from your Bilresa switch
- **Targets** - Choose lights and/or switches to control (supports groups)
- **Transition Time** (2-20 seconds, default 4) - Duration for 0%→100% dimming on long press
- **State Helper** (optional) - Input text helper for precise brightness calculation on long release
  - Home Assistant can create this for you in-place when configuring the blueprint
  - Without this: Uses approximate ±10% brightness steps
  - With this: Calculates exact brightness based on hold duration

### Usage Example

**Scenario:** Control the batcave lighting with a Bilresa switch mounted on batman's control panel

1. Create a new automation from the blueprint
2. Select `event.bilresa_batcave_button_1` for Button 1
3. Select `event.bilresa_batcave_button_2` for Button 2
4. Select `light.batcave_spotlight` as the target
5. (Optional) For the State Helper field, let Home Assistant create a helper in-place (or create one named "Bilresa Batcave transition state")
6. Set transition time to 3 seconds for faster dimming
7. Save the automation

Now you can:
- Single press Button 1 to illuminate the batcave
- Single press Button 2 to plunge the batcave into darkness
- Double press buttons to nudge brightness ±10%
- Hold buttons to smoothly dim up/down, release to stop

---

## Ridiculously Redundant Remote Ritual

**File:** `ridiculously-redundant-remote-ritual.yaml`

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/bnutzer/brilliant-blueprint-bureau/main/blueprints/automation/ridiculously-redundant-remote-ritual.yaml)

### Overview

A straightforward controller for the IKEA Rodret dimmer (two buttons) or Styrbar remote (four buttons) via Zigbee2MQTT. Features precise dimming control with optional state tracking for smooth brightness transitions.

### Requirements

- **IKEA Rodret dimmer** (E2201) OR **IKEA Styrbar remote** (E2001/E2002) connected via Zigbee2MQTT
- **Target devices:** Smart lights (individual entities or groups)
- **Optional:** Input text helper for precise dimming (recommended for best experience)

### Button Behavior

| Button | Action | Behavior |
|--------|--------|----------|
| **Up** | Single press | Turn ON |
| | Long press | Dim UP (smooth transition to 100%) |
| | Release | Stop at current brightness |
| **Down** | Single press | Turn OFF |
| | Long press | Dim DOWN (smooth transition to 1%) |
| | Release | Stop at current brightness |
| **Left** (Styrbar only) | Single press | Cycle to previous color/temperature |
| **Right** (Styrbar only) | Single press | Cycle to next color/temperature |

### Configuration Options

- **Styrbar/Rodret Remote** - Select your Rodret or Styrbar device from Zigbee2MQTT
- **Light Targets** - Choose lights to control (supports groups)
- **Transition Time** (2-20 seconds, default 4) - Duration for 0%→100% dimming on long press
- **State Helper** (optional) - Input text helper for precise brightness calculation on button release
  - Home Assistant can create this for you in-place when configuring the blueprint
  - Without this: Uses simple brightness step on release
  - With this: Calculates exact brightness based on hold duration
- **Left/Right Button Mode** - Choose what the left/right buttons control
  - **Color Temperature**: Cycle through warmth levels (2200K, 2700K, 3000K, 4000K, 5000K, 6500K)
  - **Color**: Cycle through IKEA's color palette (11 colors across the rainbow spectrum)

### Usage Example

**Scenario:** Control the Red Room training facility lights with a Rodret/Styrbar remote mounted near the combat arena

1. Ensure your Styrbar or Rodret is connected via Zigbee2MQTT and appears as a device
2. Create a new automation from the blueprint
3. Select your remote device from the dropdown
4. Select `light.red_room_arena` as the target
5. (Styrbar only) Choose "Color Temperature" for Left/Right Button Mode to adjust training intensity lighting
6. (Optional) For the State Helper field, let Home Assistant create a helper in-place (or create one named "Remote Red Room state")
7. Set transition time to 5 seconds for smooth transitions during reconnaissance training
8. Save the automation

Now you can:
- Single press Up to raise lighting for hand-to-hand combat drills
- Single press Down to reduce lighting for stealth training exercises
- Hold Up/Down to smoothly dim, release to stop precisely where needed
- (Styrbar only) Press Left/Right to cycle through different training mode temperatures

---

## Totally Trivial Tradfri Toggle Tool

**File:** `totally-trivial-tradfri-toggle-tool.yaml`

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/bnutzer/brilliant-blueprint-bureau/main/blueprints/automation/totally-trivial-tradfri-toggle-tool.yaml)

### Overview

A comprehensive controller for the IKEA Tradfri five-button remote via Zigbee2MQTT. Features toggle, discrete brightness steps, smooth dimming with state tracking, and color cycling.

### Requirements

- **IKEA Tradfri remote** (E1524/E1810) connected via Zigbee2MQTT
- **Target devices:** Smart lights (individual entities or groups)
- **Optional:** Input text helper for precise dimming (recommended for best experience)

### Button Behavior

| Button | Action | Behavior |
|--------|--------|----------|
| **Toggle** | Single press | Toggle lights on/off |
| **Brightness Up** | Click | +10% brightness |
| | Hold | Dim UP (smooth transition to 100%) |
| | Release | Stop at current brightness |
| **Brightness Down** | Click | -10% brightness |
| | Hold | Dim DOWN (smooth transition to 1%) |
| | Release | Stop at current brightness |
| **Arrow Left** | Click | Cycle to previous color/temperature |
| **Arrow Right** | Click | Cycle to next color/temperature |

### Configuration Options

- **Tradfri Remote** - Select your Tradfri device from Zigbee2MQTT
- **Light Targets** - Choose lights to control (supports groups)
- **Transition Time** (2-20 seconds, default 4) - Duration for 0%→100% dimming on long press
- **State Helper** (optional) - Input text helper for precise brightness calculation on button release
  - Home Assistant can create this for you in-place when configuring the blueprint
  - Without this: Uses ±10% brightness steps on release
  - With this: Calculates exact brightness based on hold duration
- **Left/Right Button Mode** - Choose what the arrow buttons control
  - **Color Temperature**: Cycle through warmth levels (2200K, 2700K, 3000K, 4000K, 5000K, 6500K)
  - **Color**: Cycle through IKEA's color palette (11 colors across the rainbow spectrum)

### Usage Example

**Scenario:** Control the Triskelion command center lighting with a Tradfri remote mounted on the tactical operations console

1. Ensure your Tradfri is connected via Zigbee2MQTT and appears as a device
2. Create a new automation from the blueprint
3. Select your Tradfri device from the dropdown
4. Select `light.triskelion_command_center` as the target
5. Choose "Color Temperature" for Left/Right Button Mode to adjust tactical display visibility
6. (Optional) For the State Helper field, let Home Assistant create a helper in-place (or create one named "Tradfri Triskelion state")
7. Set transition time to 4 seconds for smooth dimming
8. Save the automation

Now you can:
- Press Toggle to instantly switch command center lighting on/off for threat alerts
- Click Brightness Up/Down for quick ±10% tactical adjustments
- Hold Brightness Up/Down to smoothly dim, release to stop precisely where needed
- Press Left/Right arrows to cycle through color temperatures for different tactical scenarios

---

## Satisfyingly Simple Smart Switch Setup

**File:** `satisfyingly-simple-smart-switch-setup.yaml`

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/bnutzer/brilliant-blueprint-bureau/main/blueprints/automation/satisfyingly-simple-smart-switch-setup.yaml)

### Overview

A straightforward toggle automation for physical wall switches. Perfect for retrofitting existing wall switches with smart relays like Shelly devices, allowing physical switch operation to control smart lights.

### Requirements

- **Physical switch with smart relay** (e.g., Shelly 1, Shelly 1PM, Shelly Plus 1) or binary sensor
- **Target devices:** Smart lights and/or switches

### How It Works

Any state change of the controlling switch (on→off or off→on) toggles all target devices. This allows:
- Physical wall switch controls smart lights
- External control of targets (via app/automation) doesn't break functionality
- Simple toggle behavior without complex state tracking

### Configuration Options

- **Controlling Switch** - The physical switch entity (e.g., Shelly relay exposed as a switch or binary sensor)
- **Target Entities** - Lights and/or switches to toggle

### Usage Example

**Scenario:** Summoning sconce lighting in the Sanctum Sanctorum with a Shelly switch behind Stephen Strange's study wall switch.

1. Install Shelly Plus 1 behind the Sanctum's stone wall switch in "edge/detached switch mode"
2. The Shelly appears in Home Assistant as `switch.shelly_sanctum_study`
3. Create a new automation from this blueprint
4. Select `switch.shelly_sanctum_study` as the controlling switch
5. Select `light.sanctum_sconces` as the target
6. Save the automation

Now flipping the physical switch summons the sconce lighting, whether controlled by sorcery spells or smart home scenes.

**Common Use Cases:**
- Shelly relays behind existing wall switches
- Physical override for smart lighting systems
- Retrofit solutions for non-neutral wire installations

---

## Magnificently Minimal Motion Manager

**File:** `magnificently-minimal-motion-manager.yaml`

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/bnutzer/brilliant-blueprint-bureau/main/blueprints/automation/magnificently-minimal-motion-manager.yaml)

### Overview

A straightforward motion-activated lighting controller that respects ambient light levels. Perfect for hallways, bathrooms, and utility spaces where automatic lighting improves convenience without wasting energy.

### Requirements

- **Motion/occupancy sensor** (binary_sensor with motion or occupancy device class)
- **Luminance sensor** (sensor with illuminance device class)
- **Target devices:** Smart lights and/or switches (individual entities or groups)

### How It Works

When motion is detected AND ambient luminance is below the threshold, targets turn on. When motion stops for the configured wait time, targets turn off. Optional custom conditions allow additional constraints like time windows.

### Configuration Options

- **Motion Sensor** - Binary sensor that detects motion or occupancy
- **Luminance Sensor** - Sensor measuring ambient light level in lux
- **Maximum Luminance** (0-200 lux, default 60) - Lights won't turn on above this brightness
- **Targets** - Lights and/or switches to control when motion detected
- **Wait Time** (0-3600 seconds, default 120) - How long to keep lights on after motion stops
- **Brightness Level** (optional, 1-100%) - Set brightness percentage for lights when turned on
  - If not specified, lights use their last brightness or default level
  - Only affects lights, not switches
- **Custom Conditions** (optional) - Additional constraints like time windows or other conditions

### Usage Example

**Scenario:** Automatically illuminate the X-Mansion's main hallway at night when students pass through

1. Create a new automation from this blueprint
2. Select `binary_sensor.mansion_hallway_motion` for Motion Sensor
3. Select `sensor.mansion_hallway_luminance` for Luminance Sensor
4. Set Maximum Luminance to 50 lux (only activate in darkness)
5. Select `light.mansion_hallway` as the target
6. Set Wait Time to 180 seconds (3 minutes after motion stops)
7. (Optional) Add Custom Condition for time between 10 PM and 6 AM
8. Save the automation

Now when students walk through the mansion hallway at night, lights automatically turn on and stay on for 3 minutes after the last detected movement. During daylight hours or when ambient light is sufficient, the automation won't trigger.

**Day/Night Modes:** Create two automation instances for different behavior during day and night:
- **Day instance** (6:00-00:00): `light.mansion_hallway` at 100% brightness with time condition
- **Night instance** (00:00-6:00): `light.mansion_hallway_dim` at 20% brightness with time condition

**Common Use Cases:**
- Hallway and stairwell lighting
- Bathroom and utility room automation
- Garage and workshop lights
- Energy-efficient lighting that respects natural daylight

---

Development

These blueprints were collaboratively developed with assistance from Claude Code (claude.ai/code), because no human
should be forced to EVER manually create or handle yaml files. The blueprint logic and thematic examples are the result
of human creativity.

---

## License

This project is released under the **No Nazis License**. See [LICENSE.txt](LICENSE.txt) for full details.

In brief: Do whatever you want with this code, unless you're a Nazi.
