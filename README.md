# Development of Cow Milking Control System with Milk Tank Level Indicator
use the version 2 proteus file
## TODO LIST
- ~LCD display the level of the milk through the Ultrasonic sensor~
- ~Integrate functions with the buttons to the LCD (start and stopping of all DC motors)~
- ~Make the DC motors only work when all the IR sensors are HIGH~
- Add different DC motor patterns using the third button
## Current Schematic 
![image](https://github.com/user-attachments/assets/2b006f4c-dbee-456c-b9c6-38ce355a1de7)



## Notes
upload the hex file for the ultrasonic sensor and arduino code
## Arduino Code
```
#include <Wire.h> 
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27, 20, 4);  // set the LCD address to 0x27 for a 20 chars and 4 line display

const int trigger = 3;
const int echo = 2;
const int startButton = 13;
const int stopButton = 12;
const int tempSensorPin = A0;

bool motorsRunning = false; // State of the motors
bool temp = true;
bool initialRun = true;

// Variables to store previous states
int prevPercentage = -1;
bool prevMotorsRunning = false;
bool prevIrError = false;
int prevTemperature = -1;

void setup() {
  lcd.init();                      // initialize the lcd 
  lcd.backlight();
  for (int i = 3; i <= 7; i++) {
    pinMode(i, OUTPUT); // set the dc motors and trig as output
  }
  pinMode(echo, INPUT); // set the echo as input
  pinMode(tempSensorPin, INPUT);
  pinMode(startButton, INPUT_PULLUP); // set the start button as input with pull-up resistor
  pinMode(stopButton, INPUT_PULLUP);  // set the stop button as input with pull-up resistor
  Serial.begin(9600);
}

void loop() {
  // Read the state of the start and stop buttons
  int startButtonState = digitalRead(startButton);
  int stopButtonState = digitalRead(stopButton);

  int temperatureSensorValue = analogRead(tempSensorPin);
  int temperature = (temperatureSensorValue * 4.88)/10;	/* Convert adc value to equivalent voltage */ 

  long duration, cm;
  int ir1 = digitalRead(8);
  int ir2 = digitalRead(9);
  int ir3 = digitalRead(10);
  int ir4 = digitalRead(11);

  digitalWrite(trigger, LOW);
  delay(2);
  digitalWrite(trigger, HIGH);
  delay(5);
  digitalWrite(trigger, LOW);
  duration = pulseIn(echo, HIGH);
  cm = microsecondsToCentimeters(duration);

  // Calculate the percentage of the tank filled
  int percentage = map(cm, 1000, 100, 0, 100);

  // Update the tank percentage on the LCD if it has changed
  if (percentage != prevPercentage) {
    lcd.setCursor(0, 0);
    lcd.print("Tank: ");
    lcd.print(percentage);
    lcd.print("% FULL ");
    prevPercentage = percentage; // Update the previous percentage
  }

  if (initialRun) {
    lcd.setCursor(0, 2);
    lcd.print("Not Milking Cow  ");
    initialRun = false;
  }

  // Check if the start button is pressed (active low)
  if (startButtonState == LOW) {
    if (percentage >= 100) {
      lcd.setCursor(0, 1);
      lcd.print("Empty Tank to milk");
    } else {
      if (ir1 == HIGH && ir2 == HIGH && ir3 == HIGH && ir4 == HIGH) {
        motorsRunning = true;
        prevIrError = false;
      } else {
        lcd.setCursor(0, 1);
        lcd.print("IR error");
        prevIrError = true;
      }
    }
    delay(100); // Debounce delay
  }

  // Check if the stop button is pressed (active low)
  if (stopButtonState == LOW) {
    motorsRunning = false;
    delay(100); // Debounce delay
  }

  // If any IR sensor detects low, stop the motors
  if (motorsRunning) {
    if ((ir1 == LOW) || (ir2 == LOW) || (ir3 == LOW) || (ir4 == LOW)) {
      motorsRunning = false;
      lcd.setCursor(0, 1);
      lcd.print("IR disconnected");
      prevIrError = true;
    }
  }

  // Update the milking status on the LCD if it has changed
  if (motorsRunning != prevMotorsRunning) {
    lcd.setCursor(0, 2);
    if (motorsRunning) {
      lcd.print("Milking Cow      ");
    } else {
      lcd.print("Not Milking Cow  ");
    }
    prevMotorsRunning = motorsRunning; // Update the previous motors running state
  }

  // Update the temperature on the LCD if it has changed
  if (temperature != prevTemperature) {
    lcd.setCursor(0, 3);
    lcd.print("Temp: ");
    lcd.print(temperature);
    lcd.print("C    ");
    prevTemperature = temperature; // Update the previous temperature
  }

  // Control the motors based on the state
  for (int i = 4; i <= 7; i++) {
    digitalWrite(i, motorsRunning ? HIGH : LOW);
  }

  delay(100);
}

long microsecondsToCentimeters(long microseconds) {
  // The speed of sound is 340 m/s or 29 microseconds per centimeter.
  // The ping travels out and back, so to find the distance of the
  // object we take half of the distance travelled.
  return microseconds / 29 / 2;
}


```
