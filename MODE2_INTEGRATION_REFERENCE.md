# Mode2 Integration Reference Documentation

## Overview

**YES**, this Home Assistant integration is based on the **Mode 2** API of Bosch alarm panels. The integration uses the `bosch-alarm-mode2` library as its core dependency to communicate with Bosch alarm panels.

## What is Mode 2?

Mode 2 (also known as "Automation Mode" or "Remote Programming Software Mode") is a communication protocol provided by Bosch Security Systems for their alarm panels. It allows external systems to:
- Monitor panel status and events in real-time
- Control areas (arm/disarm)
- Manage outputs and doors
- Access panel history
- Receive push updates via subscriptions

This integration wraps the `bosch-alarm-mode2` Python library to provide native Home Assistant entities and services.

## Key Library Dependency

The integration depends on the external library hosted at:
- **GitHub**: https://github.com/dimxyp/bosch-alarm-mode2
- **Package**: `bosch-alarm-mode2`

This is declared in:
- `custom_components/bosch_alarm/manifest.json` (line 15)

## Complete Code References to Mode2

Below is a comprehensive list of all files that import or reference the `bosch_alarm_mode2` library:

### 1. **manifest.json** - Dependency Declaration
**File**: `custom_components/bosch_alarm/manifest.json`
**Line**: 15
```json
"requirements": ["bosch-alarm-mode2 @ git+https://github.com/dimxyp/bosch-alarm-mode2.git"]
```
- Declares the Python package dependency
- Specifies it should be installed from the GitHub repository

### 2. **__init__.py** - Main Integration Setup
**File**: `custom_components/bosch_alarm/__init__.py`
**Line**: 8
```python
from bosch_alarm_mode2 import Panel
```
- Imports the main `Panel` class
- Used in `async_setup_entry()` to create a Panel instance
- Initializes connection to the Bosch alarm panel (lines 41-48)

### 3. **types.py** - Type Definitions
**File**: `custom_components/bosch_alarm/types.py`
**Line**: 3
```python
from bosch_alarm_mode2 import Panel
```
- Defines type alias `BoschAlarmConfigEntry = ConfigEntry[Panel]`
- Provides type hints for config entries throughout the integration

### 4. **config_flow.py** - Configuration Flow
**File**: `custom_components/bosch_alarm/config_flow.py`
**Line**: 11
```python
from bosch_alarm_mode2 import Panel
```
- Used in the setup/configuration flow
- Creates Panel instances to test connectivity
- Uses `Panel.LOAD_EXTENDED_INFO` constant (line 236)
- Validates authentication credentials (lines 74-86, 234-237)

### 5. **alarm_control_panel.py** - Alarm Control Panel Entities
**File**: `custom_components/bosch_alarm/alarm_control_panel.py`
**Line**: 5
```python
from bosch_alarm_mode2 import Panel
```
- Creates alarm control panel entities for each area
- Uses Panel object to send arm/disarm commands:
  - `panel.area_disarm()` (line 75)
  - `panel.area_arm_part()` (line 79)
  - `panel.area_arm_all()` (line 83)

### 6. **binary_sensor.py** - Binary Sensor Entities
**File**: `custom_components/bosch_alarm/binary_sensor.py`
**Lines**: 7-8
```python
from bosch_alarm_mode2 import Panel
from bosch_alarm_mode2.const import ALARM_PANEL_FAULTS
```
- Imports Panel class and fault constants
- Creates binary sensors for:
  - Alarm points (zones)
  - Panel fault conditions (battery, AC, communication, etc.)
  - Area ready-to-arm status
- Uses `ALARM_PANEL_FAULTS` constants to identify specific fault types

### 7. **sensor.py** - Sensor Entities
**File**: `custom_components/bosch_alarm/sensor.py`
**Lines**: 8-10
```python
from bosch_alarm_mode2 import Panel
from bosch_alarm_mode2.const import ALARM_MEMORY_PRIORITIES
from bosch_alarm_mode2.panel import Area
```
- Imports Panel, Area class, and alarm priority constants
- Creates sensors for:
  - Burglary alarms (supervisory, trouble, alarm)
  - Gas alarms
  - Fire alarms
  - Faulting points counter
- Uses `ALARM_MEMORY_PRIORITIES` to categorize alarm types

### 8. **switch.py** - Switch Entities
**File**: `custom_components/bosch_alarm/switch.py`
**Lines**: 9-10
```python
from bosch_alarm_mode2 import Panel
from bosch_alarm_mode2.panel import Door
```
- Imports Panel and Door classes
- Creates switch entities for:
  - Panel outputs (remote outputs)
  - Door controls (lock, secure, cycle)
- Uses Panel methods:
  - `panel.set_output_active()` / `panel.set_output_inactive()`
  - `panel.door_relock()` / `panel.door_unlock()`
  - `panel.door_secure()` / `panel.door_unsecure()`
  - `panel.door_cycle()`

### 9. **entity.py** - Base Entity Classes
**File**: `custom_components/bosch_alarm/entity.py`
**Line**: 5
```python
from bosch_alarm_mode2 import Panel
```
- Defines base entity classes used throughout the integration
- All entities use Panel's observer pattern for state updates:
  - `panel.connection_status_observer`
  - `panel.faults_observer`
  - `area.alarm_observer`
  - `area.ready_observer`
  - `area.status_observer`
  - `point.status_observer`
  - `door.status_observer`
  - `output.status_observer`

## Test Files

The test suite also imports from `bosch_alarm_mode2`:

### 10. **conftest.py** - Test Fixtures
**File**: `tests/custom_components/bosch_alarm/conftest.py`
**Lines**: 7-8
```python
from bosch_alarm_mode2.panel import Area, Door, Output, Point
from bosch_alarm_mode2.utils import Observable
```
- Imports panel component classes for mocking
- Used to create test fixtures

### 11. **test_sensor.py** - Sensor Tests
**File**: `tests/custom_components/bosch_alarm/test_sensor.py`
**Line**: 6
```python
from bosch_alarm_mode2.const import ALARM_MEMORY_PRIORITIES
```
- Tests alarm priority sensors
- Uses constants to validate sensor behavior

### 12. **test_binary_sensor.py** - Binary Sensor Tests
**File**: `tests/custom_components/bosch_alarm/test_binary_sensor.py`
**Line**: 6
```python
from bosch_alarm_mode2.const import ALARM_PANEL_FAULTS
```
- Tests panel fault binary sensors
- Uses fault constants to validate sensor creation

## Documentation References

### 13. **README.md**
**Lines**: 6, 26
- References the `bosch-alarm-mode2` library
- Explains that the integration is based on it
- Links to the library's GitHub repository

## Key Mode2 Features Used

Based on the code analysis, this integration leverages these Mode 2 API features:

1. **Connection Management**
   - SSL/TLS encrypted connections
   - Authentication via automation/installer/user codes
   - Connection status monitoring

2. **Real-time Updates**
   - Observer pattern for push notifications
   - No polling required (should_poll = False)
   - Subscription-based updates for supported panels

3. **Panel Information**
   - Model and firmware version
   - Serial number
   - Configuration (areas, points, doors, outputs)

4. **Area Control**
   - Arm away (all)
   - Arm home (part)
   - Disarm
   - Status monitoring (armed, arming, pending, triggered)
   - Ready-to-arm status

5. **Point/Zone Monitoring**
   - Open/closed state
   - Fault status
   - Real-time state changes

6. **Output Control**
   - Activate/deactivate remote outputs
   - Status monitoring

7. **Door Control** (B/G Series)
   - Lock/unlock
   - Secure/unsecure
   - Cycle operations

8. **System Monitoring**
   - Panel faults (battery, AC, communication)
   - Alarm memory (burglary, fire, gas)
   - History/event log

## Panel Compatibility

The README states this integration supports Mode 2 compatible panels:
- Solution 2000/3000/4000
- B Series: B3512/B4512/B5512/B6512
- G Series: B8512G/B9512G
- AMAX 2100/3000/4000
- D7412GV4/D9412GV4 (Firmware 2.0+)

## Authentication

The integration uses Mode 2 specific authentication:
- **Automation Code**: 10+ character passcode with superuser privileges
- **Installer Code**: Alternative for AMAX panels
- **User Code**: Required for Solution panels or as alternative

This is different from the standard user PIN code used at physical keypads.

## Summary

This integration is **entirely based on the Mode 2 API** through the `bosch-alarm-mode2` library. Every major component (`__init__.py`, `config_flow.py`, `alarm_control_panel.py`, `binary_sensor.py`, `sensor.py`, `switch.py`, `entity.py`) imports and uses classes, methods, and constants from this library.

The integration acts as a wrapper that translates Mode 2 API capabilities into Home Assistant entities and services, following Home Assistant's architecture patterns and conventions.
