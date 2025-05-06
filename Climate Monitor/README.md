🤖🤖🤖 CLIMATE MONITOR 🤖🤖🤖


![Smashing Blorr](https://github.com/user-attachments/assets/a77a469b-6714-43b9-8dc0-ce956c3c80fe)


🧑🏻‍💻🧑🏻‍💻🧑🏻‍💻 CODE: 🧑🏻‍💻🧑🏻‍💻🧑🏻‍💻


# Climate Monitor - LUMAX LAB

VIDEO 🎥📹🎬: https://www.instagram.com/p/DEqZ6R6N2ph/

## Description
This code implements a system to measure and display temperature and humidity values using the DHT11 sensor and an I2C-controlled LCD. The measurements are continuously taken and displayed on both the LCD and the Serial Monitor. If there's an error reading the sensor, an error message will appear on the LCD and the Serial Monitor.

## Code

```cpp

/* 
   Project: Temperature and Humidity Display with DHT11 and I2C LCD
   Developed by: Lumax Lab
   Description: This code reads temperature and humidity data from the DHT11 sensor and displays them on an I2C LCD.
   The data is also sent to the Serial Monitor for debugging purposes.
*/

#include <Wire.h>               // Library for I2C communication
#include <LiquidCrystal_I2C.h>  // Library for controlling LCD with I2C
#include <DHT.h>                // Library for the DHT sensor

// I2C LCD configuration
#define LCD_ADDRESS 0x27        // I2C address of the LCD (0x27 is the most common; adjust if necessary)
#define LCD_COLUMNS 16          // Number of columns on the LCD
#define LCD_ROWS 2              // Number of rows on the LCD
LiquidCrystal_I2C lcd(LCD_ADDRESS, LCD_COLUMNS, LCD_ROWS); // Initialize the LCD

// DHT11 sensor configuration
#define DHTPIN 3                // Pin where the DHT11 is connected
#define DHTTYPE DHT11           // Type of DHT sensor
DHT dht(DHTPIN, DHTTYPE);       // Initialize the DHT object

void setup() {
  // Serial Monitor initialization
  Serial.begin(9600);
  Serial.println("Initiating DHT11 and LCD I2C...");

  // LCD initialization
  lcd.init();             // Initialize the LCD
  lcd.backlight();        // Turn on the LCD backlight
  lcd.print("Initiating"); // Initial message on the LCD
  delay(2000);            // Wait for 2 seconds

  // DHT sensor initialization
  dht.begin();
}

void loop() {
  // Read humidity and temperature data from the DHT sensor
  float humidity = dht.readHumidity();
  float temperature = dht.readTemperature();

  // Check if the sensor reading is valid
  if (isnan(humidity) || isnan(temperature)) {
    lcd.clear();
    lcd.print("Sensor error");  // Display error message on the LCD
    Serial.println("Error: Failed to read from DHT11 sensor."); // Log to Serial Monitor
    delay(2000);
    return;
  }

  // Display data on the LCD
  lcd.clear();  // Clear the LCD screen
  lcd.setCursor(0, 0);  // Set the cursor to the first line
  lcd.print("Temp: ");  // Display "Temp: "
  lcd.print(temperature);  // Display the temperature value
  lcd.print(" C");  // Display "C"

  lcd.setCursor(0, 1);  // Set the cursor to the second line
  lcd.print("Humi: ");  // Display "Humi: "
  lcd.print(humidity);  // Display the humidity value
  lcd.print(" %");  // Display "%"

  // Send data to the Serial Monitor
  Serial.print("Temperature: ");
  Serial.print(temperature);
  Serial.println(" C");

  Serial.print("Humidity: ");
  Serial.print(humidity);
  Serial.println(" %");

  delay(2000);  // Wait for 2 seconds before updating
}
