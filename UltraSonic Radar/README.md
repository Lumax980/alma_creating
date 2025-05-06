🚀🔊📡 ULTRASONIC RADAR 🚀🔊📡

![Ingenious Bojo-Borwo](https://github.com/user-attachments/assets/01b96ca6-ad5b-40cd-8c3a-5b0dea3fc6eb)


# UltraSonic Radar - LUMAX LAB

VIDEO 🎥📹🎬: https://www.youtube.com/watch?v=reKcz96L2XI

## Bill of Materials (BoM)

| **Component**          | **Quantity** | **Description**                          |
|-------------------------|--------------|------------------------------------------|
| Arduino Uno            | 1            | Microcontroller board for controlling the system. |
| Ultrasonic Sensor (HC-SR04) | 1        | Measures distances using ultrasonic waves. |
| Servo Motor (SG90)     | 1            | Rotates to sweep the radar area.         |
| Breadboard             | 1            | For easy prototyping and wiring.         |
| Jumper Wires           | ~10          | Connect components on the breadboard.    |
| USB Cable              | 1            | For powering the Arduino Uno and uploading code. |

### Notes
- Ensure proper power supply to the Arduino Uno via USB or external power.
- Double-check connections for the ultrasonic sensor and servo motor to avoid damage.

🧑🏻‍💻🧑🏻‍💻🧑🏻‍💻 CODE: 🧑🏻‍💻🧑🏻‍💻🧑🏻‍💻

## Description

This code implements an ultrasonic radar using a servo motor and an ultrasonic sensor.
   The servo motor rotates to sweep an area, while the ultrasonic sensor measures distances. 
   The data is transmitted via the Serial Monitor, showing the angle and distance for each sweep.
   Ideal for detecting obstacles and visualizing the environment using Processing or other software.

## Code Arduino


```cpp
/* 
   Project: Ultrasonic Radar
   Developed by: Lumax Lab
   Description: This code implements an ultrasonic radar using a servo motor and an ultrasonic sensor.
   The servo motor rotates to sweep an area, while the ultrasonic sensor measures distances. 
   The data is transmitted via the Serial Monitor, showing the angle and distance for each sweep.
   Ideal for detecting obstacles and visualizing the environment using Processing or other software.
*/

#include <Servo.h> // Includes the Servo library

// Define pins for the Ultrasonic Sensor
const int trigPin = 10; // Pin for triggering the ultrasonic pulse YEWLLOW
const int echoPin = 11; // Pin for receiving the echo pulse

// Variables for the duration and the calculated distance
long duration;
int distance;

// Create a Servo object to control the servo motor
Servo myServo;

void setup() {
  // Initialize pins for ultrasonic sensor
  pinMode(trigPin, OUTPUT); // Set the trigPin as an output
  pinMode(echoPin, INPUT);  // Set the echoPin as an input

  // Initialize Serial communication for debugging and data transmission
  Serial.begin(9600);

  // Attach the servo motor to pin 12
  myServo.attach(13);

  // Initial message in Serial Monitor
  Serial.println("Ultrasonic Radar Initialized");
}

void loop() {
  // Sweep the servo motor from 15 to 165 degrees
  for (int angle = 15; angle <= 165; angle++) {
    myServo.write(angle);          // Move the servo to the current angle
    delay(30);                     // Wait for the servo to reach the position
    distance = calculateDistance();// Measure the distance at the current angle

    // Send the data to the Serial Monitor
    Serial.print(angle);  // Send the angle
    Serial.print(",");    // Separator for data indexing
    Serial.print(distance); // Send the distance
    Serial.print(".");    // Separator for data indexing
  }

  // Sweep the servo motor back from 165 to 15 degrees
  for (int angle = 165; angle >= 15; angle--) {
    myServo.write(angle);          // Move the servo to the current angle
    delay(30);                     // Wait for the servo to reach the position
    distance = calculateDistance(); // Measure the distance at the current angle

    // Send the data to the Serial Monitor
    Serial.print(angle);  // Send the angle
    Serial.print(",");    // Separator for data indexing
    Serial.print(distance); // Send the distance
    Serial.print(".");    // Separator for data indexing
  }
}

// Function to calculate the distance using the ultrasonic sensor
int calculateDistance() {
  // Send a 10-microsecond pulse to trigger the ultrasonic sensor
  digitalWrite(trigPin, LOW); 
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(15);
  digitalWrite(trigPin, LOW);

  // Measure the time of the echo pulse in microseconds
  duration = pulseIn(echoPin, HIGH);

  // Calculate the distance in centimeters (speed of sound = 343 m/s)
  distance = duration * 0.034 / 2;

  return distance; // Return the calculated distance
}
```
![Captura de ecrã 2025-01-13 143827](https://github.com/user-attachments/assets/37920169-c8aa-4f28-a787-4c30aef0882b)

## Description 
This Processing code receives angle and distance data from an Arduino-based radar system
   via the serial port. It visualizes the radar scanning process and detected objects on a simulated screen.
   The radar rotates and displays the object's position based on the angle and distance.

## Code Processing 4
```java
/* 
   Project: Radar Visualization using Ultrasonic Sensor
   Developed by: Lumax Lab
   Description: This Processing code receives angle and distance data from an Arduino-based radar system
   via the serial port. It visualizes the radar scanning process and detected objects on a simulated screen.
   The radar rotates and displays the object's position based on the angle and distance.
*/

import processing.serial.*; // Library for serial communication
import java.awt.event.KeyEvent; // Library for reading key events
import java.io.IOException; // Library for handling input/output exceptions

// Serial communication setup
Serial myPort; // Defines the Serial object for communication

// Variables for radar data
String angle = "";       // Stores the angle data as a string
String distance = "";    // Stores the distance data as a string
String data = "";        // Stores the full received data string
String noObject;          // Message for indicating object detection status
float pixsDistance;       // Converts distance from cm to pixels
int iAngle, iDistance;    // Stores angle and distance as integers
int index1 = 0, index2 = 0; // Indexes for parsing received data

// Font variable for displaying text (optional, not used in this code)
PFont orcFont;

void setup() {
  /*
     Initializes the Processing window and serial communication
  */
  size(1200, 700); // Sets the window size (adjust based on screen resolution)
  smooth();        // Enables anti-aliasing for better visuals

  // Sets up the serial communication with the Arduino
  myPort = new Serial(this, "COM9", 9600); // Replace "COM9" with your serial port
  myPort.bufferUntil('.'); // Reads data until the "." character
}

void draw() {
  /*
     Main loop for rendering the radar visualization
  */
  fill(98, 245, 31); // Green color for radar visuals

  // Creates a motion blur effect
  noStroke();
  fill(0, 4); 
  rect(0, 0, width, height - height * 0.065);

  fill(98, 245, 31); // Green color for radar visuals 

  // Calls functions to render radar components
  drawRadar();   // Draws the radar structure
  drawLine();    // Draws the scanning line
  drawObject();  // Draws the detected object
  drawText();    // Displays information on the screen
}

void serialEvent(Serial myPort) {
  /*
     Event handler for incoming serial data
     Parses angle and distance data from the serial port
  */

  // Reads the data from the serial port until the "." character
  data = myPort.readStringUntil('.');
  data = data.substring(0, data.length() - 1); // Removes the trailing "."

  // Extracts angle and distance values from the received data
  index1 = data.indexOf(","); // Finds the position of the "," separator
  angle = data.substring(0, index1); // Extracts the angle value
  distance = data.substring(index1 + 1, data.length()); // Extracts the distance value

  // Converts the string values to integers
  iAngle = int(angle);
  iDistance = int(distance);
}

void drawRadar() {
  /*
     Draws the radar structure, including the arcs and angle lines
  */
  pushMatrix();
  translate(width / 2, height - height * 0.074); // Sets the radar's position

  noFill();
  strokeWeight(2);
  stroke(98, 245, 31); // Green color for the radar lines

  // Draws concentric arcs representing distance ranges
  arc(0, 0, (width - width * 0.0625), (width - width * 0.0625), PI, TWO_PI);
  arc(0, 0, (width - width * 0.27), (width - width * 0.27), PI, TWO_PI);
  arc(0, 0, (width - width * 0.479), (width - width * 0.479), PI, TWO_PI);
  arc(0, 0, (width - width * 0.687), (width - width * 0.687), PI, TWO_PI);

  // Draws radial lines for angle markers
  line(-width / 2, 0, width / 2, 0);
  for (int angle = 30; angle <= 150; angle += 30) {
    line(0, 0, (-width / 2) * cos(radians(angle)), (-width / 2) * sin(radians(angle)));
  }

  popMatrix();
}

void drawObject() {
  /*
     Draws the detected object based on the angle and distance data
  */
  pushMatrix();
  translate(width / 2, height - height * 0.074); // Sets the radar's position
  strokeWeight(9);
  stroke(255, 10, 10); // Red color for detected objects

  // Converts the distance from cm to pixels
  pixsDistance = iDistance * ((height - height * 0.1666) * 0.025);

  // Limits the detection range to 40 cm
  if (iDistance < 40) {
    // Draws the object based on its angle and distance
    line(pixsDistance * cos(radians(iAngle)), -pixsDistance * sin(radians(iAngle)), 
         (width - width * 0.505) * cos(radians(iAngle)), -(width - width * 0.505) * sin(radians(iAngle)));
  }

  popMatrix();
}

void drawLine() {
  /*
     Draws the radar's scanning line based on the current angle
  */
  pushMatrix();
  strokeWeight(9);
  stroke(30, 250, 60); // Green scanning line

  translate(width / 2, height - height * 0.074); // Sets the radar's position
  line(0, 0, (height - height * 0.12) * cos(radians(iAngle)), -(height - height * 0.12) * sin(radians(iAngle)));

  popMatrix();
}

void drawText() {
  /*
     Displays angle, distance, and range information on the screen
  */
  pushMatrix();

  // Determines if the object is within range
  noObject = (iDistance > 40) ? "Out of Range" : "In Range";

  // Draws a black rectangle as a background for the text
  fill(0, 0, 0);
  noStroke();
  rect(0, height - height * 0.0648, width, height);

  // Displays the radar data
  fill(98, 245, 31); // Green text
  textSize(25);
  text("10cm", width - width * 0.3854, height - height * 0.0833);
  text("20cm", width - width * 0.281, height - height * 0.0833);
  text("30cm", width - width * 0.177, height - height * 0.0833);
  text("40cm", width - width * 0.0729, height - height * 0.0833);
  textSize(40);
  text("Lumax Lab", width - width * 0.875, height - height * 0.0277);
  text("Angle: " + iAngle + " °", width - width * 0.48, height - height * 0.0277);
  text("Distance: ", width - width * 0.26, height - height * 0.0277);
  if (iDistance < 40) {
    text("        " + iDistance + " cm", width - width * 0.225, height - height * 0.0277);
  }

  // Displays the angle markers
  textSize(25);
  fill(98, 245, 60); // Light green text
  for (int angle = 30; angle <= 150; angle += 30) {
    float x = (width - width * 0.5) + width / 2 * cos(radians(angle));
    float y = (height - height * 0.09) - width / 2 * sin(radians(angle));
    pushMatrix();
    translate(x, y);
    rotate(-radians(90 - angle));
    text(angle + "°", 0, 0);
    popMatrix();
  }

  popMatrix();
}

