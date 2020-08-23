
#include "SD.h"
#define SD_ChipSelectPin 10
#include "TMRpcm.h"
#include "SPI.h"
#include "LiquidCrystal.h"

TMRpcm tmrpcm;
LiquidCrystal lcd(7, 6, 5, 4, 3, 2);

#define Light A0
#define TV A1
#define heater A2
int temp=0,i=0;
int led=A3;
int blink=0;
char str[40];
void setup()
{
  lcd.begin(16,2);
  Serial.begin(9600);
       pinMode(led, OUTPUT);
       pinMode(heater, OUTPUT);
       pinMode(Light, OUTPUT);
       pinMode(TV, OUTPUT);
       digitalWrite(heater,HIGH);
       
  lcd.setCursor(0,0);
  lcd.print("Voice Controlled");
  lcd.setCursor(0,1);
  lcd.print("   Automaton    ");
  delay(2000);
  lcd.clear();
  lcd.setCursor(0,1);
  lcd.print("System Ready");
  delay(500);
  delay(1000);
  lcd.clear();
  lcd.setCursor(0,0);
lcd.print("Light TV  Heater");
  lcd.setCursor(0,1);
  lcd.print("OFF   OFF  OFF ");
  tmrpcm.speakerPin=9;
  if(!SD.begin(SD_ChipSelectPin))
{
  Serial.println("SD fail");
  return;
}
tmrpcm.setVolume(6);
tmrpcm.play("ready.wav"); 
}
void loop()
{
  lcd.setCursor(0,0);
  lcd.print("Light TV  Heater");
   
  if(temp==1)
  {
    check();
    Serial.println(str);
    temp=0;
    i=0;
    delay(1000);
  }
}
 void serialEvent() 
 {
  while(Serial.available()) 
  {
    if(Serial.find("*"))
    {
      delay(5000);
      while (Serial.available()) 
      {
      char inChar=Serial.read();
      str[i++]=inChar;
      if(inChar=='#')
      {
        temp=1;
        return;
      } 
      } 
    }
   }
 }
void check()
{
   if(!(strncmp(str,"TV on",5)))
    {
        digitalWrite(TV, HIGH);
       lcd.setCursor(6,1); 
      lcd.print("ON   ");
      tmrpcm.play("tv_on.wav");
      delay(200);
    }  
   
   else if(!(strncmp(str,"TV off",6)))
    {
       digitalWrite(TV, LOW);
      lcd.setCursor(6,1);
       lcd.print("OFF  ");
       tmrpcm.play("tv_off.wav");
      delay(200);
    }
  
    else if(!(strncmp(str,"heater on",9)))
    {
      digitalWrite(heater, LOW);
      lcd.setCursor(11,1); 
      lcd.print("ON   ");
      tmrpcm.play("htr_on.wav");
      delay(200);
    }
 
    else if(!(strncmp(str,"heater off",10)))
    {
      digitalWrite(heater, HIGH);
      lcd.setCursor(11,1); 
      lcd.print("OFF    ");
      tmrpcm.play("htr_off.wav");
      delay(200);
    }
 
    else if(!(strncmp(str,"light on",8)))
    {
      digitalWrite(Light, HIGH);
      lcd.setCursor(0,1); 
      lcd.print("ON    ");
      tmrpcm.play("light_on.wav");
      delay(200);
    }
 
    else if(!(strncmp(str,"light off",9)))
    {
      digitalWrite(Light, LOW);
      lcd.setCursor(0,1); 
      lcd.print("OFF   ");
      tmrpcm.play("light_of.wav");
      delay(200);
    } 
    
    else if(!(strncmp(str,"all on",6)))
    {
      digitalWrite(Light, HIGH);
      digitalWrite(heater, LOW);
      digitalWrite(TV, HIGH);
      lcd.setCursor(0,1); 
      lcd.print("ON    ON   ON  ");
      tmrpcm.play("all_on.wav");
      delay(200);
    }
 
    else if(!(strncmp(str,"all of",6)))
    {
      digitalWrite(Light, LOW);
      digitalWrite(heater, HIGH);
      digitalWrite(TV, LOW);
      lcd.setCursor(0,1);
      lcd.print("OFF  OFF   OFF  ");
      tmrpcm.play("all_off.wav");
      delay(200);
    }     
    
     else if(!(strncmp(str,"all off",7)))
    {
      digitalWrite(Light, LOW);
      digitalWrite(heater, HIGH);
      digitalWrite(TV, LOW);
      lcd.setCursor(0,1);
      lcd.print("OFF  OFF   OFF  ");
      tmrpcm.play("all_off.wav");
      delay(200);
    }     
}


