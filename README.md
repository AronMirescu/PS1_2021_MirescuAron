# PS1_2021_MirescuAron
#include <LiquidCrystal.h>

LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

int temp;
double realTemp;

int timp;
int sec;
int min;
int ora;

void setup() {

  lcd.begin(16, 2);
  lcd.print("Temp: ");
  
  temp = 0;
  realTemp = 0;
  
  timp = 0;
  sec = 57;
  min = 59;
  ora = 23;
}

void loop() {
  lcd.print("                ");
  temp = analogRead(0);
  realTemp = (double)temp/1024; //convertire semnal analog in temperatura conform cartii tehnice
  realTemp *= 5;
  realTemp -=0.5;
  realTemp *=100;
  lcd.setCursor(6, 0);
  
  lcd.print(realTemp);
  lcd.setCursor(12, 0);
  lcd.print(String((char)178) + "C");
  
  lcd.setCursor(0, 1);
  lcd.print("Ora: ");
  
  delay(1000);
  timp++;
  
  if(sec + timp == 60)
  {
    timp = 0;
    sec = 0;
    min++;
  }
  if(min == 60)
  {
   min = 0;
   ora++;
  }
  if(ora == 24)
  {
    timp = 0;
  	sec = 0;
    min = 0;
    ora = 0;
  }
  
  
  lcd.setCursor(0, 1);
  lcd.print("                ");
  lcd.print("Ora: ");
  
  lcd.setCursor(5, 1);
  lcd.print(ora);
  
  lcd.setCursor(7, 1);
  lcd.print(":");
  
  lcd.setCursor(8, 1);
  lcd.print(min);
  
  lcd.setCursor(10, 1);
  lcd.print(":");
  
  lcd.setCursor(11, 1);
  lcd.print(sec + timp);

}
