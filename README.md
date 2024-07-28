# EMBDSYS_FINAL_PROJECT
## Schematic Diagram
![image](https://github.com/user-attachments/assets/2e8c4cbb-4da5-4ed9-bf49-a6cfd323f088)
## Current Schematic 
![image](https://github.com/user-attachments/assets/440ead0e-ee3d-4a85-8ff5-d572332700f7)
## Notes
upload the hex file for the ultrasonic sensor and arduino code
## Arduino Code
```
#include <Wire.h> 
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27,16,2);  // set the LCD address to 0x27 for a 16 chars and 2 line display

#define Sensor A0
const int trigger = 3;
const int echo = 2;

void setup(){
  lcd.init();                      // initialize the lcd 
  // Print a message to the LCD.
  lcd.begin(20,4,LCD_5x8DOTS);
  lcd.backlight();
  for (int i=3; i<=7; i++){
  pinMode(i, OUTPUT); // set the dc motors as output
  }
  pinMode(echo,INPUT); 
  Serial.begin(9600);
  
}

void loop() {
  // establish variables for duration of the ping, 
  // and the distance result in inches and centimeters:
  long duration, inches, cm;

  // The PING))) is triggered by a HIGH pulse of 2 or more microseconds.
  // Give a short LOW pulse beforehand to ensure a clean HIGH pulse:
  digitalWrite(trigger, LOW);
  //delayMicroseconds(2);
  delay(2);
  digitalWrite(trigger, HIGH);
  //delayMicroseconds(5);
  delay(5);
  digitalWrite(trigger, LOW);

  // The same pin is used to read the signal from the PING))): a HIGH
  // pulse whose duration is the time (in microseconds) from the sending
  // of the ping to the reception of its echo off of an object.
  duration = pulseIn(echo, HIGH);

  // convert the time into a distance
  inches = microsecondsToInches(duration);
  cm = microsecondsToCentimeters(duration);

  Serial.print(inches);
  Serial.print("in, ");
  Serial.print(cm);
  Serial.print("cm");
  Serial.println();

  lcd.setCursor(0,0);
  lcd.print("Inches: ");
  lcd.setCursor(10,0);
  lcd.print(inches);

  lcd.setCursor(0,1);
  lcd.print("Cm: ");
  lcd.setCursor(5,1);
  lcd.print(cm);

  lcd.setCursor(0,2);
  lcd.print("Row 3: ");

  lcd.setCursor(0,3);
  lcd.print("Row 4: ");

  for (int i=4; i<=7; i++){
    digitalWrite(i,LOW);
  }
  delay(500);
  for (int i=4; i<=7; i++){
    digitalWrite(i,HIGH);
  }
  delay(500);
  lcd.clear();
  //delay(20);
}
long microsecondsToInches(long microseconds)
{
  // According to Parallax's datasheet for the PING))), there are
  // 73.746 microseconds per inch (i.e. sound travels at 1130 feet per
  // second).  This gives the distance travelled by the ping, outbound
  // and return, so we divide by 2 to get the distance of the obstacle.
  // See: http://www.parallax.com/dl/docs/prod/acc/28015-PING-v1.3.pdf
  return microseconds / 74 / 2;
}

long microsecondsToCentimeters(long microseconds)
{
  // The speed of sound is 340 m/s or 29 microseconds per centimeter.
  // The ping travels out and back, so to find the distance of the
  // object we take half of the distance travelled.
  return microseconds / 29 / 2;
}

```
