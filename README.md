# EMBDSYS_FINAL_PROJECT
## Arduino Code
```
#include <Wire.h> 
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27,16,2);  // set the LCD address to 0x27 for a 16 chars and 2 line display

#define Sensor A0

void setup(){
  lcd.init();                      // initialize the lcd 
  // Print a message to the LCD.
  lcd.begin(20,4,LCD_5x8DOTS);
  lcd.backlight();
  
  Serial.begin(9600);
  
}

void loop() {
  lcd.setCursor(0,0);
  lcd.print("Row 1: ");
  lcd.setCursor(0,1);
  lcd.print("Row 2: ");
  lcd.setCursor(0,2);
  lcd.print("Row 3: ");
  lcd.setCursor(0,3);
  lcd.print("Row 4: ");
  delay(500);
  lcd.clear();
  //delay(20);
}
```
