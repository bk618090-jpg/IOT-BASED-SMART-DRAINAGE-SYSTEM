# IOT-BASED-SMART-DRAINAGE-SYSTEM
The Arduino code is used to control the Smart Drainage Monitoring System based on IoT. The water level sensor continuously monitors the drainage water level and sends data to the Arduino UNO. The LCD display shows the current system status and water level condition. 


#include <LiquidCrystal.h>
#include <SoftwareSerial.h>

// LCD Pins
LiquidCrystal lcd(7, 8, 9, 10, 11, 12);

// GSM Module Pins
SoftwareSerial gsm(2, 3); // RX, TX

// Components
const int waterSensor = A0;
const int relayPin = 4;
const int buzzer = 5;

int waterValue = 0;
int threshold = 300;

void setup() 
{
  pinMode(relayPin, OUTPUT);
  pinMode(buzzer, OUTPUT);

  digitalWrite(relayPin, LOW);
  digitalWrite(buzzer, LOW);

  lcd.begin(16, 2);
  lcd.print("Smart Drainage");
  lcd.setCursor(0,1);
  lcd.print("Monitoring");

  gsm.begin(9600);

  delay(3000);
  lcd.clear();
}

void loop() 
{
  waterValue = analogRead(waterSensor);

  lcd.setCursor(0,0);
  lcd.print("Level=");
  lcd.print(waterValue);
  lcd.print("   ");

  // Blockage / High Water Detection
  if(waterValue > threshold)
  {
    lcd.setCursor(0,1);
    lcd.print("BLOCKAGE DET");

    digitalWrite(relayPin, HIGH); // Motor ON
    digitalWrite(buzzer, HIGH);   // Buzzer ON

    sendSMS();

    delay(5000);
  }
  else
  {
    lcd.setCursor(0,1);
    lcd.print("NORMAL FLOW ");

    digitalWrite(relayPin, LOW);  // Motor OFF
    digitalWrite(buzzer, LOW);    // Buzzer OFF
  }

  delay(1000);
}

// GSM SMS Function
void sendSMS()
{
  gsm.println("AT+CMGF=1");
  delay(1000);

  gsm.println("AT+CMGS=\"+91XXXXXXXXXX\""); // Replace with phone number
  delay(1000);

  gsm.println("Alert! Drainage blockage detected.");
  delay(100);

  gsm.write(26);
  delay(3000);
}
