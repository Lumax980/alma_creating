🤖🤖🤖 PROXIMITY LEVEL 🤖🤖🤖

![Exquisite Esboo-Fulffy](https://github.com/user-attachments/assets/486b35b1-5e66-471e-b861-9b7d5cc82a16)

🧑🏻‍💻🧑🏻‍💻🧑🏻‍💻 CODE: 🧑🏻‍💻🧑🏻‍💻🧑🏻‍💻



# Proximity Level - LUMAX LAB

VIDEO 🎥📹🎬: https://www.instagram.com/p/DEfqT6ztJGD/

## Description
This code uses an HC-SR04 ultrasonic sensor to measure distances and provides visual feedback via LEDs and audible alerts using a buzzer. The LEDs illuminate progressively based on distance thresholds, and the buzzer sounds when the object is too close.

## Code

```cpp

/* 
   Project: Distance Measurement with LED and Buzzer Indicators
   Developed by: Alma Creating
   Description: This code uses an HC-SR04 ultrasonic sensor to measure distance and provides visual and audible feedback. 
   LEDs are activated based on predefined distance thresholds, with each LED corresponding to a different range of distances. 
   A buzzer sounds when the measured distance is below a certain threshold, alerting the user when the object is too close. 
*/

#define TRIG_PIN A0
#define ECHO_PIN A1
#define BUZZER_PIN 8

int leds[] = {2, 3, 4, 5, 6, 7, 11, 12, 13}; // LEDs in order
int numLeds = sizeof(leds) / sizeof(leds[0]); // Total number of LEDs
int ledThresholds[] = {30, 26, 22, 19, 16, 13, 10, 8, 6}; // Distance thresholds for each LED

void setup() {
  Serial.begin(9600);

  // Set up LEDs as output
  for (int i = 0; i < numLeds; i++) {
    pinMode(leds[i], OUTPUT);
    digitalWrite(leds[i], LOW); // Ensure LEDs start off
  }

  // Set up sensor and buzzer
  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);
  pinMode(BUZZER_PIN, OUTPUT);

  noTone(BUZZER_PIN); // Ensure buzzer is initially off
}

// Function to measure distance using the HC-SR04
float measureDistance() {
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  long duration = pulseIn(ECHO_PIN, HIGH, 30000); // Timeout of 30 ms
  if (duration == 0) return -1; // No response from sensor
  return (duration * 0.034) / 2; // Convert to centimeters
}

// Update LEDs based on the distance
void updateLeds(float distance) {
  for (int i = 0; i < numLeds; i++) {
    if (distance <= ledThresholds[i]) {
      digitalWrite(leds[i], HIGH); // Turn on LED if distance is less than or equal to the threshold
    } else {
      digitalWrite(leds[i], LOW); // Turn off LED if distance is greater than the threshold
    }
  }
}

// Update buzzer based on the distance
void updateBuzzer(float distance) {
  if (distance <= 6) {
    tone(BUZZER_PIN, 1000); // Activate buzzer with 1000 Hz
  } else {
    noTone(BUZZER_PIN); // Turn off the buzzer
  }
}

void loop() {
  float distance = measureDistance();
  if (distance < 0 || distance > 400) {
    Serial.println("Invalid measurement.");
    return;
  }

  Serial.print("Distance: ");
  Serial.print(distance);
  Serial.println(" cm");

  updateLeds(distance); // Update LEDs based on the distance
  updateBuzzer(distance); // Update buzzer based on the distance

  delay(100); // Small delay for stability
}
