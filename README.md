TASK 2:- (Program)
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x26, 16, 2);

// LED Pins
int redLED = 1;      // D1
int yellowLED = 2;   // D2
int greenLED = 3;    // D3

// Motor Pin
int motorPin = 4;    // D4

// PING))) Sensor Pin
int pingPin = 7;

int count = 0;

// Function to read distance from PING))) sensor
long readUltrasonicDistance(int pin)
{
  pinMode(pin, OUTPUT);

  digitalWrite(pin, LOW);
  delayMicroseconds(2);

  digitalWrite(pin, HIGH);
  delayMicroseconds(10);

  digitalWrite(pin, LOW);

  pinMode(pin, INPUT);

  return pulseIn(pin, HIGH);
}

void setup()
{
  lcd.init();
  lcd.backlight();

  pinMode(redLED, OUTPUT);
  pinMode(yellowLED, OUTPUT);
  pinMode(greenLED, OUTPUT);
  pinMode(motorPin, OUTPUT);

  // Initial State
  digitalWrite(greenLED, HIGH);
  digitalWrite(yellowLED, LOW);
  digitalWrite(redLED, LOW);
  digitalWrite(motorPin, LOW);

  lcd.setCursor(0, 0);
  lcd.print("Pedestrians:");
  lcd.setCursor(0, 1);
  lcd.print("0");
}

void loop()
{
  long duration = readUltrasonicDistance(pingPin);
  int cm = duration * 0.01723;

  // Detect object within 20 cm
  if (cm > 0 && cm < 20)
  {
    // Increase Counter
    count++;

    lcd.setCursor(0, 1);
    lcd.print("                ");
    lcd.setCursor(0, 1);
    lcd.print(count);

    // Motor ON
    digitalWrite(motorPin, HIGH);

    // GREEN -> YELLOW
    digitalWrite(greenLED, LOW);
    digitalWrite(yellowLED, HIGH);
    delay(2000);

    // YELLOW -> RED
    digitalWrite(yellowLED, LOW);
    digitalWrite(redLED, HIGH);
    delay(6000);

    // Motor OFF
    digitalWrite(motorPin, LOW);

    // RED -> OFF
    digitalWrite(redLED, LOW);

    // GREEN ON AGAIN
    digitalWrite(greenLED, HIGH);

    // Wait until object moves away
    while (cm < 20)
    {
      duration = readUltrasonicDistance(pingPin);
      cm = duration * 0.01723;
      delay(100);
    }
  }

  delay(100);
}
