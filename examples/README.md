# Examples

This directory contains example configurations demonstrating how to use the Modbus TCP component with different ESP32 frameworks.

## Files

### esp-idf-example.yaml
Example configuration using the **ESP-IDF framework** with native lwip sockets.
- No external dependencies required
- Uses synchronous TCP operations
- Recommended for ESP-IDF projects

### arduino-example.yaml
Example configuration using the **Arduino framework** with AsyncTCP library.
- Requires AsyncTCP library (specified in platformio_options)
- Uses asynchronous, non-blocking TCP operations
- Recommended for Arduino framework projects

## Usage

1. Copy one of the example files as a starting point for your project
2. Create a `secrets.yaml` file with your credentials:

```yaml
wifi_ssid: "YourWiFiSSID"
wifi_password: "YourWiFiPassword"
ap_password: "FallbackAPPassword"
api_encryption_key: "your-32-character-api-key-here"
ota_password: "YourOTAPassword"
```

3. Modify the Modbus TCP server IP address and register addresses to match your setup
4. Compile and upload to your ESP32 device

## Notes

- Both examples provide the same functionality, just using different underlying TCP implementations
- The component automatically selects the appropriate TCP implementation based on the framework
- Choose the framework that best fits your project requirements
- ESP-IDF typically has a smaller memory footprint
- Arduino framework is easier to use with other Arduino libraries

## Framework Selection

The key difference between the two examples is in the `esp32.framework` section:

**ESP-IDF:**
```yaml
esp32:
  framework:
    type: esp-idf
```

**Arduino:**
```yaml
esp32:
  framework:
    type: arduino
  platformio_options:
    lib_deps:
      - esphome/AsyncTCP-esphome@^2.0.0
```
