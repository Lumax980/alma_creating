🔐📲💡 RFID ACCESS CONTROL SYSTEM 🔐📲💡

![alt text](<Imagem WhatsApp 2025-05-12 às 16.54.09_c9fd0955-1.jpg>)

![alt text](circuit_rfid_acess-1.png)

![alt text](serial-1.png)

🧑🏻‍💻🧑🏻‍💻🧑🏻‍💻 CODE: 🧑🏻‍💻🧑🏻‍💻🧑🏻‍💻

# RFID Access Control System - LUMAX LAB

VIDEO 🎥📹🎬: [https://www.instagram.com/p/DEqZ6R6N2ph/](https://www.instagram.com/p/DFGLEUztOCW/) ????????????????

## Description

   Project: RFID Access Control with LCD and Buzzer
   Developed by: Lumax Lab
   Description: This project uses an RC522 RFID reader to detect authorized cards,
   displaying the result on an I2C LCD and indicating status using LEDs and a passive buzzer.
   Green LED and short beep indicate access granted.
   Red LED and three low-pitch beeps indicate access denied.

## Code

```cpp


/* 
   Project: RFID Access Control with LCD and Buzzer
   Developed by: Lumax Lab
   Description: This project uses an RC522 RFID reader to detect authorized cards,
   displaying the result on an I2C LCD and indicating status using LEDs and a passive buzzer.
   Green LED and short beep indicate access granted.
   Red LED and three low-pitch beeps indicate access denied.
*/

#include <Wire.h>               // Library for I2C communication
#include <LiquidCrystal_I2C.h>  // Library for controlling LCD with I2C
#include <SPI.h>                // Library for SPI communication
#include <MFRC522.h>            // Library for RFID RC522 reader

// ==========================
// RFID Pin Configuration
// ==========================
const uint8_t RFID_SDA = 2;     // SDA (SS) pin connected to D9
const uint8_t RFID_RST = 25;    // RST pin connected to D2

// ==========================
// I2C LCD Configuration
// ==========================
LiquidCrystal_I2C lcd(0x27, 16, 2); // LCD at I2C address 0x27, 16 columns x 2 rows

// ==========================
// LED Pin Configuration
// ==========================
const uint8_t LED_BLUE      = 12;   // Blue LED: waiting for card
const uint8_t LED_GREEN     = 4;    // Green LED: access granted
const uint8_t LED_RED       = 13;   // Red LED: access denied

// ==========================
// Buzzer Configuration
// ==========================
const uint8_t BUZZER_PIN    = 17;   // Passive buzzer pin (D17)

// ==========================
// RFID and UID Variables
// ==========================
MFRC522 mfrc522(RFID_SDA, RFID_RST); // Create MFRC522 instance
byte uid_lido[4];                    // Stores UID of the scanned card

void setup() {
  // Serial Monitor Initialization
  Serial.begin(115200);
  while (!Serial); // Wait for serial to be ready

  // LCD Initialization
  lcd.init();
  lcd.backlight();
  lcd.setCursor(0, 0);
  lcd.print("ESP32 READY");
  lcd.setCursor(0, 1);
  lcd.print("Waiting for card");

  // LED and Buzzer Pin Modes
  pinMode(LED_RED, OUTPUT);
  pinMode(LED_GREEN, OUTPUT);
  pinMode(LED_BLUE, OUTPUT);
  pinMode(BUZZER_PIN, OUTPUT);

  // Default State: Blue LED ON
  digitalWrite(LED_BLUE, HIGH);
  digitalWrite(LED_GREEN, LOW);
  digitalWrite(LED_RED, LOW);

  // RFID Initialization
  SPI.begin();
  mfrc522.PCD_Init();

  delay(1000); // Give time for components to stabilize
}

void loop() {
  // Check for card presence
  if (!mfrc522.PICC_IsNewCardPresent() || !mfrc522.PICC_ReadCardSerial()) {
    return;
  }

  // Read UID from card
  for (byte i = 0; i < 4; i++) {
    uid_lido[i] = mfrc522.uid.uidByte[i];
  }

  // Display UID in Serial Monitor
  Serial.print("Card detected: UID:");
  for (byte i = 0; i < 4; i++) {
    Serial.print(uid_lido[i] < 0x10 ? " 0" : " ");
    Serial.print(uid_lido[i], HEX);
  }
  Serial.println();

  // >>>>>> Replace this with your real UID <<<<<<
  const byte uid_authorized[4] = {0x04, 0xAA, 0x92, 0x0A}; // Authorized UID

  // UID Comparison
  bool authorized = true;
  for (byte i = 0; i < 4; i++) {
    if (uid_lido[i] != uid_authorized[i]) {
      authorized = false;
      break;
    }
  }

  // Access Granted Actions
  if (authorized) {
    Serial.println("Access GRANTED");

    lcd.clear();
    lcd.setCursor(0, 0);
    lcd.print("Card detected:");
    lcd.setCursor(0, 1);
    lcd.print("Welcome!");

    digitalWrite(LED_GREEN, HIGH);
    digitalWrite(LED_RED, LOW);

    tone(BUZZER_PIN, 1000, 300); // Short high beep
  }
  // Access Denied Actions
  else {
    Serial.println("Access DENIED");

    lcd.clear();
    lcd.setCursor(0, 0);
    lcd.print("Card detected:");
    lcd.setCursor(0, 1);
    lcd.print("Access denied");

    digitalWrite(LED_GREEN, LOW);
    digitalWrite(LED_RED, HIGH);

    // Three low-pitched beeps
    for (int i = 0; i < 3; i++) {
      tone(BUZZER_PIN, 400, 100);
      delay(150);
    }
  }

  // Turn off blue LED and wait for 3 seconds
  digitalWrite(LED_BLUE, LOW);
  delay(3000);

  // Reset to idle state
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Waiting for card");
  Serial.println("Waiting for new card");

  digitalWrite(LED_BLUE, HIGH);
  digitalWrite(LED_GREEN, LOW);
  digitalWrite(LED_RED, LOW);

  mfrc522.PICC_HaltA(); // Stop communication with current card
}
