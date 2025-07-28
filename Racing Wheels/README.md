🎮🛞⚙️ RACING WHEELS 🎮🛞⚙️

![Terrific Gaaris-Juttuli](https://github.com/user-attachments/assets/fa6106a8-a8ea-4118-b7c7-cf7f8df17631)

🧑🏻‍💻🧑🏻‍💻🧑🏻‍💻 CODE: 🧑🏻‍💻🧑🏻‍💻🧑🏻‍💻


# Racing Wheels - LUMAX LAB

VIDEO 🎥📹🎬: 

## Description
This code implements a DIY steering wheel simulator using a potentiometer and an SG90 servo motor. The system reads analog input from a potentiometer attached to a homemade cardboard wheel, and maps this input to control the angle of the servo motor. As the user turns the wheel left or right, the servo moves correspondingly, demonstrating basic proportional control. This project is ideal for beginners learning about analog-to-digital conversion, servo control, and physical interaction with electronics. The angle is also printed to the Serial Monitor for real-time debugging and monitoring.

## Code

```cpp

/* 
   Project: Servo Position Control with Potentiometer (BK10)
   Developed by: ALMA Creating
   Description: This project reads analog values from a potentiometer and maps them 
   to control the rotation angle of an SG90 servo motor from 0° to 180°.
   The current angle is also printed to the Serial Monitor for debugging purposes.
*/

#include <Servo.h> // Library for controlling servo motors

// ==========================
// Pin Configuration
// ==========================
const int potPin   = A0; // Potentiometer signal connected to analog pin A0
const int servoPin = 9;  // Servo control signal connected to digital pin 9

// ==========================
// Servo Object
// ==========================
Servo servo; // Create a Servo object to control the SG90

void setup() {
  // Serial Monitor Initialization
  Serial.begin(9600); // Start Serial communication at 9600 baud for debugging

  // Servo Initialization
  servo.attach(servoPin); // Attach the servo signal wire to pin 9
}

void loop() {
  // Read analog input from the potentiometer (range: 0 to 1023)
  int potValue = analogRead(potPin);

  // Map the potentiometer value to servo angle (0° to 180°)
  int angle = map(potValue, 0, 1023, 0, 180);

  // Move the servo to the mapped angle
  servo.write(angle);

  // Display potentiometer reading and servo angle on Serial Monitor
  Serial.print("Potentiometer: ");
  Serial.print(potValue);
  Serial.print(" => Angle: ");
  Serial.println(angle);

  // Small delay to smooth the servo movement
  delay(15);
}

