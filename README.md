# AquaSafe Node-RED Flow

## 📖 Overview
This Node-RED flow enables the reception, validation, and broadcasting of various data types (GPS/SOS, Chat, Weather, and Hotspot data) from the AquaSafe system. It processes incoming HTTP POST requests, validates the payload, formats AT commands, and sends them to a connected LoRa module via a serial port.

---

## 💡 Key Features
- Receive and handle JSON data via HTTP POST endpoints.
- Validate incoming payload formats.
- Format and encode AT commands in Base64.
- Send formatted AT commands over serial to a connected LoRa device.
- Respond with appropriate HTTP success or error codes.
- Log debug outputs for real-time monitoring.

---

## 📡 HTTP Endpoints
| Endpoint                         | Description                                    |
|----------------------------------|------------------------------------------------|
| `/api/broadcast-gps`             | Receives GPS/SOS data and broadcasts via LoRa |
| `/api/broadcast-chat_data`       | Receives chat messages and transmits over LoRa|
| `/api/broadcast-weather_data`    | Receives weather data and sends LoRa requests |
| `/api/broadcast-hotspot_data`    | Receives hotspot data array and sends sequentially over LoRa |

---

## 🔎 Validation Logic
### GPS/SOS Data:
- Validates presence of `id`, `l` (latitude|longitude), and `s` (status).
- Ensures correct GPS format.
- Responds with `400` on errors and `200` on success.

### Chat Data:
- Validates presence of `id` and `m` (message).
- Enforces length and value constraints.
- Responds with `400` on errors and `200` on success.

### Weather Data:
- Ensures the presence of `id` and weather value `w`.
- Transforms into AT command and broadcasts over serial.

### Hotspot Data:
- Accepts an array of hotspot objects.
- Formats and sends each entry as a Base64-encoded AT command with a delay.

---

## 🚀 AT Command Formats
| Data Type   | AT Command Format Example                                            |
|-------------|----------------------------------------------------------------------|
| GPS/SOS     | `AT+SEND=1,"<base64_encoded_id|lat|long|status>",0,3\r\n`          |
| Chat        | `AT+SEND=1,<base64_encoded_c|id|l|m>,0,3\r\n`                       |
| Weather     | `AT+SEND=1,<base64_encoded_w|id|w>,0,3\r\n`                         |
| Hotspot     | `AT+SEND=1,<base64_encoded_h|id|lat|long>,0,3\r\n`                 |

---

## ⚙️ Serial Port Configuration
- **Port:** `/dev/ttyUSB0`
- **Baud Rate:** `9600`
- **Data Bits:** `8`
- **Parity:** `None`
- **Stop Bits:** `1`
- **Add Characters:** `\r\n`
- **Response Timeout:** `10000ms`

---

## 🛠 Preconfigured AT Commands (via Inject nodes)
- `ATZ` (Reset)
- `AT+FRE=433.000,433.000` (Set frequency)
- `AT+BW=0,0` (Set bandwidth)
- `AT+SF=12,12` (Set spreading factor)
- `AT+RXMOD=65535,0` (Receive mode without ACK)
- `AT+SYNCWORD=0` (Set sync word)
- `AT+RXDAFORM=1` (Data format setup)

---

## 🔄 Response Handling
- On successful processing, returns HTTP `200 OK` with JSON confirmation.
- On payload validation errors, returns HTTP `400 Bad Request` with descriptive messages.

---

## ✅ Debugging
Multiple debug nodes are connected throughout the flow to log:
- Incoming payloads.
- Errors in formatting and validation.
- Generated AT commands.
- Serial port transmissions.
- HTTP API responses.

---

## 📥 External API Calls
- The system makes HTTP POST requests to:
  - `http://172.31.255.253:3000/api/reRouteGps`
  - `http://172.31.255.253:3000/api/getWeatherDataFromServer`
  - `http://172.31.255.253:3000/api/getHotpostDataFromServer`

---

## 📂 Usage
1. Deploy this flow in Node-RED.
2. Connect a LoRa module to `/dev/ttyUSB0`.
3. Use HTTP clients (Postman, cURL) to send payloads to the provided endpoints.
4. Monitor transmission in the debug sidebar.

---

## 🤝 Contributions
Feel free to fork, extend, or modify this flow for other applications.

---

## 🛠 License
This project is released under the MIT License.