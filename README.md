
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
LiquidCrystal lcd(12, 10, 5, 4, 3, 2);
///tema3
int program = 0;
int timpu = 10;
int timpi = 10;
int timpm = 10;
int timpr = 10;
double tempe = 36.6; //setpoint
double kp = 1;
double ki = 0.1;
double kd = 0.1;
float temp_q = 20;
Menus scroll_menu = MENU_MAIN;
Menus current_menu =  MENU_MAIN;
///tema3///
///tema4
 double eroare= 0;
 double suma_erori= 0;
 double eroare_anterioara = 0;
 double derivativa = 0;
 double dt = 0.1; // timp esantionare
 double output = 0;
///team4///
int temp;
double realTemp;
int secunde = 0;
int timp;
int sec;
int min;
int ora;

double process_temp = 0;

///tema3

///tema5
bool gen = false;
float timpa = 0;
float init_temp = 0;
///tema5///

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
    	if(gen)///testeaza daca a inceput procedura
        {
         lcd.setCursor(0, 0);
         lcd.print("                ");
         lcd.setCursor(0, 1);
         lcd.print("                ");
         
         lcd.setCursor(0, 0);
         if(secunde <= timpi)
         {
         lcd.print("Ti=");
         lcd.print(timpi);
         lcd.print("s");
         }
         else if(secunde <= timpi + timpm)
         {
         lcd.print("Tm=");
         lcd.print(timpm);
         lcd.print("s");
         }
         else if(secunde <= timpi+timpm+timpr+1)
         {
         lcd.print("Tr=");
         lcd.print(timpr);
         lcd.print("s");
         }
         lcd.print(" ");
         lcd.print("Tset=");
         lcd.print(tempe);
          
         lcd.setCursor(0, 1);
         lcd.print("P");
         lcd.print(program);
         lcd.print("    ");
         lcd.print("Temp=");
         lcd.print(realTemp);
         
   ///tema4
    eroare = tempe - realTemp;
    suma_erori= suma_erori + eroare * dt;
    derivativa = (eroare - eroare_anterioara) / dt;
    output = (kp * eroare) + (ki * suma_erori ) + (kd * derivativa);
    eroare_anterioara = eroare;
  
  if(output > 5)
  {
  OCR2A = 255;
  }
  else if(output < 0)
  {
  OCR2A = 0;
  }
  else
  {
  OCR2A =(int)((output * 255)/5);  
  }
          
  heat_process1();
  ///tema4///
        }
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
   
  //lcd.setCursor(6, 0);
  
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
  lcd.print(sec + timp);
   		break;
  }
  if(current_menu != MENU_MAIN && current_menu != MENU_Start)
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

///tema5
void start_proces(void)
{
  gen = true;
}
///tema5///

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
  {start_proces, go_home, go_next, go_prev},	//MENU_Start
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

///tema5
void set_pwm()
{
  TCCR2A |= (1 << WGM20) | (1 << WGM21); 
  TCCR2B |= (1 << CS20) | (1 << CS21);
  TCCR2A |= 1 << COM0A1;
}


///tema5///

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
  pinMode(11, OUTPUT);
  digitalWrite(11, LOW);
  ///tema4///
  
  ///tema5
  set_pwm();  
  ///tema5///
}

void loop() 
{
  
  ///tema2
  temp = analogRead(0);
  realTemp = (double)temp/1024; //convertire semanl analog in temperatura conform cartii tehnice
  realTemp *= 5;
  realTemp -=0.5;
  realTemp *=100;
  realTemp = realTemp + process_temp;
  
  timp++;
  
  if(gen)
  {
  secunde++;
  }
  else
    OCR2A = 0;
  
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
  
}

void heat_process1()
{
  if(secunde==1)
    init_temp=realTemp;

  if(secunde <= timpi)
  {
	process_temp=(tempe-init_temp)*secunde/timpi;

  }
  else if(secunde <= timpi + timpm)
  { 
    if(realTemp < tempe)
  {
  process_temp=(tempe-init_temp)*secunde/timpi;
  OCR2A = 255;
  }
  else
  {
  OCR2A = 0;
  }

  }
  else if(secunde <= timpi+timpm+timpr+1)
  {
    
    timpa =(float)(timpi+timpm+timpr - secunde)/(timpr);
    process_temp=process_temp*timpa;
    
    if(realTemp < (tempe-init_temp)*secunde/timpr)
    {
      OCR2A = 255;
    }
    else
    {
       OCR2A = 0;
    }

  }
  else
    gen = false;
}
