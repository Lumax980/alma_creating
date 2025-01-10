🤖🤖🤖 MEMORY CHALLANGE 🤖🤖🤖

![Terrific Gaaris-Juttuli](https://github.com/user-attachments/assets/fa6106a8-a8ea-4118-b7c7-cf7f8df17631)



🧑🏻‍💻🧑🏻‍💻🧑🏻‍💻 CODE: 🧑🏻‍💻🧑🏻‍💻🧑🏻‍💻


/* 
   Project: Memory Challenge
   Developed by: Lumax Lab
   Description: This code implements a Simon Says game with LEDs, buttons, and a buzzer. 
   The game shows a sequence of lights that the player must repeat by pressing the corresponding buttons. 
   If the player repeats the sequence correctly, the game progresses to the next level. 
   If the player makes a mistake, the game resets to the first level.
*

#include <Arduino.h>

// Define LED, Button, and Buzzer pins
const int ledPins[] = {2, 3, 4, 5, 6};     // LED pins
const int buttonPins[] = {7, 8, 9, 10, 11}; // Button pins
const int buzzerPin = 12;                  // Buzzer pin

int gameSequence[10];  // Array to store the LED sequence for the game
int sequenceLength = 1; // Length of the sequence (starts with 1)
int playerIndex = 0;    // Index for player to compare against

void setup() {
  // Set LED pins as outputs
  for (int i = 0; i < 5; i++) {
    pinMode(ledPins[i], OUTPUT);
    digitalWrite(ledPins[i], LOW); // Initialize LEDs to be off
  }

  // Set button pins as inputs with internal pull-up resistors
  for (int i = 0; i < 5; i++) {
    pinMode(buttonPins[i], INPUT_PULLUP);
  }

  pinMode(buzzerPin, OUTPUT);  // Set the buzzer pin as output
  randomSeed(analogRead(0));   // Initialize random number generator
}

void loop() {
  // Start the game with the first sequence
  if (playerIndex == 0) {
    generateSequence();        // Generate the LED sequence
    playSequence();            // Display the sequence to the player
  }

  // Wait for the player to press the correct button
  bool correctButtonPressed = false;
  while (!correctButtonPressed) {
    for (int i = 0; i < 5; i++) {
      if (digitalRead(buttonPins[i]) == LOW) { // Button pressed
        digitalWrite(ledPins[i], HIGH);        // Turn on the corresponding LED
        delay(500);                            // Wait for a while
        digitalWrite(ledPins[i], LOW);         // Turn off the LED

        // Check if the pressed button corresponds to the player's sequence
        if (gameSequence[playerIndex] == i) {
          playerIndex++;  // Player got it right, move to the next LED

          if (playerIndex == sequenceLength) {  // If the entire sequence is completed correctly
            playSuccessTone();   // Play success tone
            showSuccessEffect(); // Show success effect with LEDs
            sequenceLength++;    // Increase the sequence length for the next level
            playerIndex = 0;     // Reset the player's index to restart the sequence
            delay(2000);          // Wait for 2 seconds before starting the next level
          }
          correctButtonPressed = true;  // Correct button was pressed, move to the next LED
        } else {
          playErrorTone();     // Play error tone
          showErrorEffect();   // Show error effect with LEDs flashing
          sequenceLength = 1;  // Reset sequence length to level 1
          playerIndex = 0;     // Reset player's index
          delay(2000);         // Wait for 2 seconds before restarting the game
          correctButtonPressed = true;  // Stop waiting and restart the game
        }
      }
    }
  }
}

// Function to generate a random sequence of LEDs
void generateSequence() {
  for (int i = 0; i < sequenceLength; i++) {
    gameSequence[i] = random(0, 5);  // Generate a random number between 0 and 4 (for 5 LEDs)
  }
}

// Function to play the LED sequence for the player
void playSequence() {
  for (int i = 0; i < sequenceLength; i++) {
    digitalWrite(ledPins[gameSequence[i]], HIGH);
    delay(500); // Keep the LED on for 500ms
    digitalWrite(ledPins[gameSequence[i]], LOW);
    delay(200); // Wait 200ms between LEDs
  }
}

// Function to play the success tone on the buzzer (Access Granted)
void playSuccessTone() {
  tone(buzzerPin, 1000, 300); // 1000Hz tone for 300ms (success tone)
}

// Function to play the error tone on the buzzer (Access Denied)
void playErrorTone() {
  // 300Hz tone for 500ms
  tone(buzzerPin, 300, 500);  
  delay(500); // Wait for the tone to finish before playing it again
  
  // Play the tone again
  tone(buzzerPin, 300, 500);  
  delay(500); // Wait for the tone to finish before continuing
}

// Function to show success effect with LEDs
void showSuccessEffect() {
  // Turn on all LEDs
  for (int i = 0; i < 5; i++) {
    digitalWrite(ledPins[i], HIGH);
  }
  delay(500); // Keep LEDs on for 500ms
  resetLEDs(); // Turn off all LEDs
  delay(500); // Wait 500ms before starting the next sequence
}

// Function to show error effect with LEDs flashing
void showErrorEffect() {
  // LEDs flashing from 1 to 5
  for (int i = 0; i < 5; i++) {
    digitalWrite(ledPins[i], HIGH);
    delay(100); // LED on for 100ms
    digitalWrite(ledPins[i], LOW);
    delay(100); // LED off for 100ms
  }

  // LEDs flashing from 5 to 1
  for (int i = 4; i >= 0; i--) {
    digitalWrite(ledPins[i], HIGH);
    delay(100); // LED on for 100ms
    digitalWrite(ledPins[i], LOW);
    delay(100); // LED off for 100ms
  }
  delay(500); // Wait 500ms after the error effect
}

// Function to turn off all LEDs
void resetLEDs() {
  for (int i = 0; i < 5; i++) {
    digitalWrite(ledPins[i], LOW); // Turn off all LEDs
  }
}

