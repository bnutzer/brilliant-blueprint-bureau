# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

The Brilliant Blueprint Bureau is a collection of Home Assistant blueprints for automating smart home devices. Includes blueprints for:
- IKEA Matter-over-Thread devices (Bilresa two-button switch)
- IKEA Zigbee2MQTT devices (Rodret dimmer, Styrbar remote, Tradfri two-button switch, Tradfri five-button remote)
- Physical switch controllers (Shelly relays, wall switches)
- Motion-activated lighting (motion sensors with luminance control)

## Blueprint Structure

Home Assistant blueprints are YAML files that define reusable automation templates. Each blueprint file contains:

- **blueprint.name**: Display name shown in Home Assistant UI
- **blueprint.description**: User-facing description of functionality
- **blueprint.domain**: Always "automation" for automation blueprints
- **blueprint.input**: User-configurable parameters with selectors
- **variables**: Internal variables derived from inputs
- **trigger**: Events that start the automation
- **action**: Actions to perform when triggered

## Blueprint Development Guidelines

### Event Handling Patterns

#### Matter Devices (Bilresa)
The Bilresa blueprint uses a state-based trigger pattern for event entities:
- Triggers fire on state changes (timestamp changes on event entities)
- Event types are checked via `trigger.to_state.attributes.event_type`
- Common event types: `multi_press_1` (single press), `long_press` (hold)

#### Zigbee2MQTT Devices (Rodret, Styrbar, Tradfri)
The Rodret/Styrbar/Tradfri blueprints use device triggers for MQTT actions:
- Triggers use `trigger: device` with `domain: mqtt`
- Action types specified via `subtype` field
- Common action types:
  - Rodret/Styrbar/Tradfri two-button: `on`, `off`, `brightness_move_up`, `brightness_move_down`, `brightness_stop`
  - Tradfri five-button: `toggle`, `brightness_up_click`, `brightness_up_hold`, `brightness_up_release`, `brightness_down_click`, `brightness_down_hold`, `brightness_down_release`
- Arrow buttons: `arrow_left_click`, `arrow_right_click` (Styrbar and Tradfri five-button)
- Tradfri five-button specific: Uses separate click/hold/release events for brightness, allowing both discrete steps (±10% on click) and smooth dimming (on hold)
- Tradfri two-button (E1743): Simpler on/off switch using the same MQTT action pattern as Rodret/Styrbar

#### Motion Sensors
The Motion Manager blueprint uses state-based triggers for motion detection:
- Trigger fires on binary_sensor state change from "off" to "on"
- Luminance threshold checked in conditions (numeric_state below threshold)
- Uses `wait_for_trigger` to detect when motion stops
- Optional custom conditions support time windows and other constraints
- Empty condition arrays (`[]`) evaluate to TRUE in AND blocks

### Mode Configuration

Use `mode: restart` with `max_exceeded: silent` for button controllers to ensure:
- New button presses interrupt ongoing dimming actions
- No warning logs when automations restart

### Light Control Services

- `light.turn_on`: Turns lights on, optionally with brightness/transition
  - `brightness_pct`: 1-100 percentage
  - `transition`: Duration in seconds for smooth changes
- `light.turn_off`: Turns lights off

### Target Selectors

Use `selector.target.entity.domain: light` to allow users to select individual lights or light groups without distinction.

## Testing Blueprints

Blueprints must be tested in a Home Assistant instance:
1. Copy the YAML file to `config/blueprints/automation/` in Home Assistant
2. Reload automations or restart Home Assistant
3. Create a new automation using the blueprint from the UI
4. Test with actual hardware or device simulators
