# Universal Modbus-TCP for ESPHome

This component provides Modbus TCP client functionality for ESPHome and supports both Arduino and ESP-IDF frameworks.

Port is optional (default: 502)

## Framework Support

This component now supports **both** ESP32 frameworks:

### Arduino Framework (with AsyncTCP)
- Uses the AsyncTCP library for non-blocking, asynchronous TCP operations
- Best for projects already using Arduino framework
- To use AsyncTCP on Arduino, add it to your `platformio_options`:

```yaml
esp32:
  board: esp32dev
  framework:
    type: arduino
  platformio_options:
    lib_deps:
      - esphome/AsyncTCP-esphome@^2.0.0
```

### ESP-IDF Framework (with lwip sockets)
- Uses native lwip sockets for synchronous TCP operations
- No external dependencies required
- Recommended for ESP-IDF projects

```yaml
esp32:
  board: esp32dev
  framework:
    type: esp-idf
```

The component automatically detects which framework is being used at compile time and uses the appropriate TCP implementation.

## Example Configuration

```yaml
external_components:
  - source: github://lmaertin/esphome_modbus_tcp
    refresh: 0s

esp32:
  board: esp32dev
  framework:
    type: esp-idf    # or 'arduino' - both are supported

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

## Framework Implementation Details

### Arduino Framework
- **Transport**: AsyncTCP library (non-blocking, event-driven)
- **Benefits**: 
  - Asynchronous I/O doesn't block the main loop
  - Better for complex projects with multiple components
  - Event-driven callbacks for connection management
- **Requirements**: AsyncTCP library (automatically managed if added to lib_deps)

### ESP-IDF Framework
- **Transport**: lwip sockets (synchronous, non-blocking sockets)
- **Benefits**:
  - No external dependencies
  - Native ESP-IDF support
  - Simpler implementation for straightforward use cases
- **Requirements**: None (lwip is part of ESP-IDF)

The component will log which transport is being used during startup. Look for lines like:
- `Transport: AsyncTCP (Arduino framework)` or
- `Transport: lwip sockets (ESP-IDF framework)`

## Migration Notes

### From ESP-IDF only version
This component now supports both frameworks. Existing ESP-IDF configurations will continue to work without changes.

### For Arduino Framework users
To enable AsyncTCP support when using Arduino framework:
1. Make sure you're using Arduino framework (not ESP-IDF)
2. Add AsyncTCP to your platformio lib_deps (see example above)
3. The component will automatically use AsyncTCP when the `ARDUINO` macro is defined

## Troubleshooting

### Build errors about missing AsyncTCP
- If building with Arduino framework and you see AsyncTCP errors, add the library to lib_deps
- If building with ESP-IDF, AsyncTCP is not used and not required

### Connection issues
- Ensure your Modbus TCP server is reachable
- Check network configuration and firewall settings
- Review ESPHome logs for connection status messages

## Legacy Notes

**Note**: This component was previously adapted specifically for ESP-IDF. It now supports both Arduino and ESP-IDF frameworks through conditional compilation.

## Useful Links

- [Modbus TCP Protocol Description](https://ipc2u.de/artikel/wissenswertes/detaillierte-beschreibung-des-modbus-tcp-protokolls-mit-befehlsbeispielen/)
- [ESPHome Documentation](https://esphome.io/)
- [AsyncTCP Library](https://github.com/me-no-dev/AsyncTCP)
