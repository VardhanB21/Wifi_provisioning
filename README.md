# Wi-Fi Provisioning with BLE on ESP32

## Overview
This project demonstrates Wi-Fi provisioning on an ESP32 using Bluetooth Low Energy (BLE). Provisioning allows the ESP32 to receive Wi-Fi credentials from a smartphone or other BLE-enabled device, enabling the ESP32 to connect to a Wi-Fi network. This simplified implementation is based on the ESP-IDF examples but customized to solve specific requirements.

### Key Features
- **BLE Transport**: Uses BLE for Wi-Fi provisioning.
- **Custom BLE Service**: Includes a custom data endpoint for additional communication.
- **Wi-Fi Event Handling**: Handles connection, disconnection, and IP address assignment.
- **Provisioning Status**: Tracks provisioning success or failure and logs relevant details.

### Simplifications
- **BLE Mode Only**: The code supports BLE for provisioning and excludes the SoftAP mode.
- **No QR Display**: QR code generation and display functionality are omitted.
- **Testing Limitation**: The code was not tested on a physical ESP32 board.

---

## Code Walkthrough

### Header Files
The following header files are included:
```c
#include <stdio.h>
#include <string.h>
#include <freertos/FreeRTOS.h>
#include <freertos/task.h>
#include <freertos/event_groups.h>
#include <esp_log.h>
#include <esp_wifi.h>
#include <esp_event.h>
#include <nvs_flash.h>
#include <wifi_provisioning/manager.h>
#include <wifi_provisioning/scheme_ble.h>
```

## Global Variables and Macros

- **`TAG`**: Tag for logging messages.
- **`wifi_event_group`**: FreeRTOS event group to signal Wi-Fi connection events.
- **`PROV_TRANSPORT_BLE`**: BLE as the provisioning transport.

---

## Event Handling

### `event_handler`
This function manages events from various sources:

#### Wi-Fi Provisioning Events:
- **`WIFI_PROV_START`**: Starts provisioning.
- **`WIFI_PROV_CRED_RECV`**: Handles received credentials.
- **`WIFI_PROV_CRED_SUCCESS` / `WIFI_PROV_CRED_FAIL`**: Logs provisioning success or failure.
- Deinitializes the provisioning manager after completion.

#### Wi-Fi Events:
- Connects to the network upon station start.
- Reconnects if disconnected.

#### IP Events:
- Logs the obtained IP address and signals the application to proceed.

#### BLE Transport Events:
- Logs BLE connection and disconnection events.

---

## Wi-Fi Initialization

### `wifi_init_sta`
This function configures the ESP32 as a Wi-Fi station:
- Sets the mode to `WIFI_MODE_STA`.
- Starts the Wi-Fi interface.

---

## Device Service Name

### `get_device_service_name`
This function generates a unique BLE service name using the ESP32's MAC address.

---

## Custom Endpoint Handler

### `custom_prov_data_handler`
Defines a custom endpoint (`custom-data`) for handling BLE messages:
- Logs received data.
- Sends a **"SUCCESS"** response back to the client.

## Main Application

### `app_main`
The `app_main` function implements the following steps:

1. **NVS Initialization**:
   - Initializes non-volatile storage for Wi-Fi credentials.

2. **TCP/IP and Event Loop Initialization**:
   - Sets up networking and event handling.

3. **Provisioning Setup**:
   - Initializes the provisioning manager with BLE as the transport scheme.
   - Checks if the device is already provisioned.
     - **If not provisioned**:
       - Starts BLE provisioning with a custom service UUID.
       - Registers a custom data endpoint.
     - **If provisioned**:
       - Starts Wi-Fi in station mode.

4. **Wi-Fi Connection**:
   - Waits for successful Wi-Fi connection using event groups.

5. **Main Task**:
   - Logs `"Hello!"` every second.

---

## Configuration Details

### Menuconfig Adjustments
The following changes were made in `menuconfig`:

- **BLE Transport Enabled**:
  - BLE provisioning was selected over SoftAP.
- **QR Display Disabled**:
  - QR code generation and related dependencies were excluded.

These changes reflect in the `sdkconfig` file.

---

## Testing

- Since no physical ESP32 hardware was available, the code was not tested on a device.
- **Testing is required** to verify BLE provisioning and Wi-Fi connectivity in a real-world scenario.

---

## Notes

- The code is a simplified version of the official ESP-IDF examples, customized for BLE-based Wi-Fi provisioning.
- The BLE-only approach reduces complexity and focuses on achieving provisioning functionality efficiently.
- **Future Updates**:
  - Include SoftAP mode.
  - Add QR code display.
  - Support for other provisioning schemes.

