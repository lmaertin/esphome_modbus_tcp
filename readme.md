# Universal Modbus-TCP for ESPHome (ESP-IDF changes)

Port is optional (default: 502)

This fork adapts the Modbus-TCP integration to work when building ESPHome projects using the ESP-IDF framework for ESP32. The main changes are related to using a TCP transport compatible with ESP-IDF and documenting that the esp-idf framework must be selected in your ESPHome YAML.

Important: set the esp32.framework type to esp-idf in your ESPHome configuration when using this integration.

Example configuration:

```yaml
external_components:
  - source: github://creepystefan/esphome_tcp
    refresh: 0s

esp32:
  board: esp32dev
  framework:
    type: esp-idf

modbustcp:
  - id: modbustesttcp
    host: 192.168.178.46
    port: 502
         
modbustcp_controller:
  - id: modbus_device
    modbustcp_id: modbustesttcp
    address: 1                   # Unit-ID
    update_interval: 5s   # default 60s

sensor:
  - platform: modbustcp_controller
    modbustcp_controller_id: modbus_device
    name: A-Voltage
    address: 1021
    value_type: FP32
    register_type: read
    accuracy_decimals: 2

binary_sensor:
  - platform: modbustcp_controller
    modbustcp_controller_id: modbus_device
    name: "Error status"
    register_type: read
    address: 0x3200

switch:
  - platform: modbustcp_controller
    modbustcp_controller_id: modbus_device
    id: testswitch
    register_type: coil
    address: 2
    name: "testswitch"
    bitmask: 1
```

Value types (optional): the datatype of Modbus register data. The default Modbus data type is a 16-bit integer in big-endian (network byte order, MSB first).

- U_WORD : unsigned 16-bit integer, 1 register, uint16_t
- S_WORD : signed 16-bit integer, 1 register, int16_t
- U_DWORD : unsigned 32-bit integer, 2 registers, uint32_t
- S_DWORD : signed 32-bit integer, 2 registers, int32_t
- U_DWORD_R : little-endian unsigned 32-bit integer, 2 registers, uint32_t
- S_DWORD_R : little-endian signed 32-bit integer, 2 registers, int32_t
- U_QWORD : unsigned 64-bit integer, 4 registers, uint64_t
- S_QWORD : signed 64-bit integer, 4 registers, int64_t
- U_QWORD_R : little-endian unsigned 64-bit integer, 4 registers, uint64_t
- S_QWORD_R : little-endian signed 64-bit integer, 4 registers, int64_t
- FP32 : 32-bit IEEE 754 floating point, 2 registers, float
- FP32_R : little-endian 32-bit IEEE 754 floating point, 2 registers, float

Defaults to U_WORD.

Changes and notes for ESP-IDF:

- Build with ESPHome using esp-idf by setting esp32.framework.type: esp-idf.
- This integration uses an external TCP transport (creepystefan/esphome_tcp) that supports ESP-IDF. Ensure the external component is available and compatible with your ESPHome version.
- The networking layer has been adapted to use the TCP transport compatible with ESP-IDF; function calls and initialization are different from Arduino/ESP8266 builds.
- If you encounter build errors, verify that all external components used in your YAML support the esp-idf framework and that your ESPHome/ESP-IDF toolchain is installed correctly.

Useful link:
https://ipc2u.de/artikel/wissenswertes/detaillierte-beschreibung-des-modbus-tcp-protokolls-mit-befehlsbeispielen/

(End of README)
