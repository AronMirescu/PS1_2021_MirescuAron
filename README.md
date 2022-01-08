# PS1_2021_MirescuAron


#include <LiquidCrystal.h>
///tema3
#define BUTTON_OK 6
#define BUTTON_CANCEL 7
#define BUTTON_PREV 8
#define BUTTON_NEXT 9

enum Buttons {
  EV_OK,
  EV_CANCEL,
  EV_NEXT,
  EV_PREV,
  EV_NONE,
  EV_MAX_NUM
};

enum Menus {
  MENU_MAIN = 0,  
  MENU_Start,///
  MENU_Alege,
  MENU_TEMP,
  MENU_Incal,
  MENU_Menti,
  MENU_Racire,
  MENU_KP,
  MENU_KI,
  MENU_KD,
  MENU_MAX_NUM
};


///tema3///
LiquidCrystal lcd(12, 11, 5, 4, 3, 2);
///tema3
int program = 0;
int timpu = 10;
int timpi = 0;
int timpm = 0;
int timpr = 0;
double tempe = 36.6;
double kp = 1;
double ki = 0.1;
double kd = 0.1;
float temp_q = 0;
Menus scroll_menu = MENU_MAIN;
Menus current_menu =  MENU_MAIN;
///tema3///
///tema4
 double eroare= 0;
 double suma_erori= 0;
 double eroare_anterioara = 0;
 double derivativa = 0;
 double dt = 0.1; // timp esantionare
 double setpoint = 20;
 double output;
///team4///
int temp;
double realTemp;

int timp;
int sec;
int min;
int ora;

///tema3
void state_machine(enum Menus menu, enum Buttons button);
Buttons GetButtons(void);
void print_menu(enum Menus menu);

typedef void (state_machine_handler_t)(void);

void print_menu(enum Menus menu)
{
  lcd.begin(16, 2);
  lcd.clear();
  switch(menu)
  {
    case MENU_Start:
    	lcd.print("Start");
    	//lcd.print(kp);
    	break;
    case MENU_Alege:
    	lcd.print("Programul = ");
    	lcd.print(program);
    	break;
    case MENU_TEMP:
    	lcd.print("Temp = ");
    	lcd.print(tempe);
    	break;
    case MENU_Incal:
    	lcd.print("Timp incal = ");
    	lcd.print(timpi);
    	break;
    case MENU_Menti:
    	lcd.print("Timp menti = ");
    	lcd.print(timpm);
    	break;
    case MENU_Racire:
    	lcd.print("Timp racir = ");
    	lcd.print(timpr);
    	break;
    case MENU_KP:
    	lcd.print("KP = ");
    	lcd.print(kp);
    	break;
    case MENU_KI:
    	lcd.print("KI = ");
    	lcd.print(ki);
    	break;
    case MENU_KD:
    	lcd.print("KD = ");
    	lcd.print(kd);
    	break;
    case MENU_MAIN:
    default:
   lcd.print("MENIU T=");
   
  /*lcd.setCursor(6, 0);
  
  lcd.print(realTemp);
  lcd.setCursor(12, 0);
  lcd.print(String((char)178) + "C");//temperatura curenta  
  
  lcd.setCursor(0, 1);
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
  lcd.print(sec + timp);*/
    
    ///tema4
    lcd.setCursor(0, 1);
    lcd.print(output);
    ///tema4///
   		break;
  }
  if(current_menu != MENU_MAIN)
  {
  	lcd.setCursor(0,1);
  	lcd.print("modifica");
  }
}

void enter_menu(void)
{
  current_menu = scroll_menu;
}

void go_home(void)
{
  scroll_menu = MENU_MAIN;
  current_menu = scroll_menu;
}

void go_next(void)
{
  scroll_menu = (Menus) ((int)scroll_menu + 1);
  scroll_menu = (Menus) ((int)scroll_menu % MENU_MAX_NUM);
}

void go_prev(void)
{
  scroll_menu = (Menus) ((int)scroll_menu - 1);
  scroll_menu = (Menus) ((int)scroll_menu % MENU_MAX_NUM);
}

void save_program(void)
{  
}

void inc_program(void) ///////////////program-alegere
{
  if(program < 4)
    program++;
}

void dec_program(void)
{
  if(program > 0)
    program--;
}

void save_kp(void)
{
}

void inc_kp(void) ////////////KP
{
  kp++;
}

void dec_kp(void)
{
  kp--;
}

void inc_ki(void) ////////////KI
{
  ki++;
}

void dec_ki(void)
{
  ki--;
}

void inc_kd(void) ////////////KD
{
  kd++;
}

void dec_kd(void)
{
  kd--;
}

void save_temp(void)
{
}

void inc_temp(void) ////////temperatura
{
  tempe++;
}

void dec_temp(void)
{
  tempe--;
}

void inc_timpi(void)///////////timp incalzire
{
  timpi++;
}

void dec_timpi(void)
{
  timpi--;
}

void inc_timpm(void)///////////timp mentinere
{
  timpm++;
}

void dec_timpm(void)
{
  timpm--;
}

void inc_timpr(void)///////////timp racire
{
  timpr++;
}

void dec_timpr(void)
{
  timpr--;
}


state_machine_handler_t* sm[MENU_MAX_NUM][EV_MAX_NUM] = 
{ //events: OK , CANCEL , NEXT, PREV
  {enter_menu, go_home, go_next, go_prev},  //MENU_MAIN
  {go_home, go_home, go_next, go_prev},	//MENU_Start
  {go_home, go_home, inc_program, dec_program},	//MENU_Alege//
  {go_home, go_home, inc_temp, dec_temp,},	//MENU_TEMP//
  {go_home, go_home, inc_timpi, dec_timpi},	//MENU_Incal
  {go_home, go_home, inc_timpm, dec_timpm},	//MENU_Menti
  {go_home, go_home, inc_timpr, dec_timpr},	//MENU_Racire
  {go_home, go_home, inc_kp, dec_kp},	//MENU_KP//
  {go_home, go_home, inc_ki, dec_ki},	//MENU_KI//
  {go_home, go_home, inc_kd, dec_kd},	//MENU_KD//
};

void state_machine(enum Menus menu, enum Buttons button)
{
  sm[menu][button]();
}

Buttons GetButtons(void)
{
  enum Buttons ret_val = EV_NONE;
  if (digitalRead(BUTTON_OK))
  {
    ret_val = EV_OK;
  }
  else if (digitalRead(BUTTON_CANCEL))
  {
    ret_val = EV_CANCEL;
  }
  else if (digitalRead(BUTTON_NEXT))
  {
    ret_val = EV_NEXT;
  }
  else if (digitalRead(BUTTON_PREV))
  {
    ret_val = EV_PREV;
  }
  Serial.print(ret_val);
  return ret_val;
}
///tema3///

void setup() 
{

  lcd.begin(16, 2);
  Serial.begin(9600);
  lcd.begin(16,2);
  pinMode(6, INPUT);
  digitalWrite(6, LOW); // pull-down
    pinMode(7, INPUT);
  digitalWrite(7, LOW); // pull-down
    pinMode(8, INPUT);
  digitalWrite(8, LOW); // pull-down
    pinMode(9, INPUT);
  digitalWrite(9, LOW); // pull-down
  
  ///tema4
  pinMode(10, OUTPUT);
  digitalWrite(10, LOW);
  ///tema4///
  
  /*lcd.begin(16, 2);
  lcd.print("Temp: ");
  
  temp = 0;
  realTemp = 0;
  
  timp = 0;
  sec = 57;
  min = 59;
  ora = 23;*/
}

void loop() 
{
  /*lcd.print("                ");
  temp = analogRead(0);
  realTemp = (double)temp/1024; //convertire semanl analog in temperatura conform cartii tehnice
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
  lcd.print(sec + timp);*/
  
  ///tema2
  temp = analogRead(0);
  realTemp = (double)temp/1024; //convertire semanl analog in temperatura conform cartii tehnice
  realTemp *= 5;
  realTemp -=0.5;
  realTemp *=100;
  
  /*//delay(1000);
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
  }*/
  ///tema2///
  
  ///tema3
  volatile Buttons event = GetButtons();
  if (event != EV_NONE)
  {
    state_machine(current_menu, event);
  }
  
    print_menu(scroll_menu);
    delay(1000);
  ///tema3///
  ///tema4
    eroare = setpoint - realTemp;
    suma_erori= suma_erori + eroare * dt;
    derivativa = (eroare - eroare_anterioara) / dt;
    output = (kp * eroare) + (ki * suma_erori ) + (kd * derivativa);
    eroare_anterioara = eroare;
    
  
  //digitalWrite(10,5);
  if(output > 40)////modific dupa ce inteleg formula!!!!!!!
  {
  digitalWrite(10,HIGH);
  }
  else if(output < 40)
  {
  digitalWrite(10,LOW);
  }
  else if(output > 40 && output <40)
  {
  digitalWrite(10, (output * 255)/5);  
  }
  ///tema4///
}
 
 
