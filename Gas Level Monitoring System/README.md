⛽📊🔍 GAS LEVEL MONIROTING SYSTEM ⛽📊🔍


![Imagem WhatsApp 2025-01-21 às 13 51 28_e9fdb454](https://github.com/user-attachments/assets/ab544dd3-7f02-4eaa-8b5e-724d7910a12b)


🧑🏻‍💻🧑🏻‍💻🧑🏻‍💻 CODE: 🧑🏻‍💻🧑🏻‍💻🧑🏻‍💻


# Gas Level Monitoring Systemr - LUMAX LAB

VIDEO 🎥📹🎬: [https://www.instagram.com/p/DEqZ6R6N2ph/](https://www.instagram.com/p/DFGLEUztOCW/)

## Description
/**
 * Project: Gas Level Monitoring System
 * Developed by: Lumax Lab
 * This program runs on an ESP32 microcontroller and is designed to monitor gas levels using an MQ2 sensor. 
 * It uses a web interface to display real-time gas concentration in ppm (parts per million) and triggers
 * visual (LED) and audio (buzzer) alerts when the gas level exceeds a predefined threshold.
 * 
 * Features:
 * - Wi-Fi Access Point for web-based monitoring.
 * - Real-time gas data visualization with a dynamic gauge.
 * - Alerts via LEDs and a buzzer when gas levels are high.
 * - JSON endpoint to provide gas data for web applications.
 */

## Code

```cpp


/**
 * Project: Gas Level Monitoring System
 * Developed by: Lumax Lab
 * This program runs on an ESP32 microcontroller and is designed to monitor gas levels using an MQ2 sensor. 
 * It uses a web interface to display real-time gas concentration in ppm (parts per million) and triggers
 * visual (LED) and audio (buzzer) alerts when the gas level exceeds a predefined threshold.
 * 
 * Features:
 * - Wi-Fi Access Point for web-based monitoring.
 * - Real-time gas data visualization with a dynamic gauge.
 * - Alerts via LEDs and a buzzer when gas levels are high.
 * - JSON endpoint to provide gas data for web applications.
 */

#include <WiFi.h>
#include <WebServer.h>

#define AO_PIN A0        // ESP32's pin GPIO36 connected to AO pin of the MQ2 sensor
#define BUZZER_PIN D2    // GPIO 4 connected to the passive buzzer
#define LED_RED D9       // GPIO 9 connected to red LED
#define LED_GREEN D3     // GPIO 3 connected to green LED

WebServer server(80);   // Create a web server instance

// Wi-Fi credentials
const char *ssid = "LumaxLab";
const char *password = "lumaxlab";

void setup() {
  // Initialize serial communication for debugging
  Serial.begin(9600);

  // Set the ADC attenuation to 11 dB (up to ~3.3V input)
  analogSetAttenuation(ADC_11db);

  // Configure GPIO pins
  pinMode(BUZZER_PIN, OUTPUT);
  pinMode(LED_RED, OUTPUT);
  pinMode(LED_GREEN, OUTPUT);

  // Initialize Wi-Fi in Access Point mode
  WiFi.softAP(ssid, password);
  Serial.println("Wi-Fi AP started.");
  Serial.print("Access the web page at: ");
  Serial.println(WiFi.softAPIP());

  // Set up the web server routes
  server.on("/", handleRoot);      // Root route for the homepage
  server.on("/gas", HTTP_GET, handleGasData);  // Route to provide gas data in JSON format
  server.begin();

  // Sensor warm-up period
  Serial.println("Warming up the MQ2 sensor...");
  delay(1000);  // Wait for the MQ2 sensor to stabilize
}

void loop() {
  int gasValue = analogRead(AO_PIN); // Read gas sensor value
  Serial.println("Gas Value: " + String(gasValue) + " ppm");

  // Check if the gas value exceeds the safety threshold
  if (gasValue > 1700) {
    tone(BUZZER_PIN, 1000);          // Activate buzzer
    digitalWrite(LED_RED, HIGH);    // Turn on red LED
    digitalWrite(LED_GREEN, LOW);   // Turn off green LED
  } else {
    noTone(BUZZER_PIN);             // Deactivate buzzer
    digitalWrite(LED_RED, LOW);     // Turn off red LED
    digitalWrite(LED_GREEN, HIGH);  // Turn on green LED
  }

  // Handle web server requests
  server.handleClient();

  delay(100);  // Small delay for stability
}

// Function to handle the root web page
void handleRoot() {
  String html = "<!DOCTYPE html>"
                "<html lang='en'>"
                "<head>"
                "<meta charset='UTF-8'>"
                "<meta name='viewport' content='width=device-width, initial-scale=1.0'>"
                "<title>LumaxLab Gas Monitor</title>"
                "<style>"
                "body { font-family: Arial, sans-serif; text-align: center; background-color: #f0f0f0; color: #333; margin: 0; padding: 0; }"
                "h1 { color: #2258E0; margin-top: 20px; }"
                ".gauge-container { margin: 40px auto; width: 300px; height: 300px; position: relative; }"
                ".gauge { width: 300px; height: 300px; border-radius: 50%; background: conic-gradient(green 0% 50%, yellow 50% 80%, red 80% 100%); transform: rotate(-90deg); position: relative; }"
                ".gauge-overlay { width: 280px; height: 280px; border-radius: 50%; background: #f0f0f0; position: absolute; top: 10px; left: 10px; }"
                ".gauge-text { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); font-size: 24px; font-weight: bold; color: #333; }"
                "footer { margin-top: 20px; font-size: 14px; color: #777; }"
                "</style>"
                "<script>"
                "function updateGauge() {"
                "  fetch('/gas')"
                "    .then(response => response.json())"
                "    .then(data => {"
                "      const gauge = document.querySelector('.gauge');"
                "      const gasValue = data.gas;"
                "      const percent = Math.min(Math.max(gasValue / 1700 * 100, 0), 100);"
                "      gauge.style.background = `conic-gradient(green 0% ${percent}%, yellow ${percent}% 80%, red 80% 100%)`;"
                "      document.querySelector('.gauge-text').innerText = gasValue + ' ppm';"
                "    })"
                "    .catch(err => console.error('Error fetching gas data:', err));"
                "} "
                "setInterval(updateGauge, 1000);"
                "</script>"
                "</head>"
                "<body>"
                "<h1>LumaxLab Gas Sensor</h1>"
                "<div class='gauge-container'>"
                "  <div class='gauge'></div>"
                "  <div class='gauge-overlay'></div>"
                "  <div class='gauge-text'>0 ppm</div>"
                "</div>"
                "<footer>Powered by LumaxLab</footer>"
                "</body>"
                "</html>";

  server.send(200, "text/html", html);
}

// Function to provide gas data in JSON format
void handleGasData() {
  int gasValue = analogRead(AO_PIN);  // Read the gas sensor value
  Serial.println("Gas Value Sent to Web: " + String(gasValue)); // Debugging output

  // Create and send a JSON response with the gas value
  String json = "{\"gas\": \"" + String(gasValue) + "\"}";
  server.send(200, "application/json", json);
}
