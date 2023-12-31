# Proyecto

<img src="https://github.com/0Mateciotti/Parcial-spd-2-/blob/main/Parcial%20Spd%202/imagenes/parcial%202%20spd.png?raw=true" width="800">
<img src="https://github.com/0Mateciotti/Parcial-spd-2-/blob/main/Parcial%20Spd%202/imagenes/esquematica%20spd%202.png?raw=true" width="800">

# Funcionamiento del programa

### El termometro se encarga de medir y enviar la temperatura al LCD y el LCD calculara la estacion del año en la que estemos, en caso de llegar a mas de 60 grados celcius se entrara el modo alarma, encendiendo un led rojo, el servomotor y mostrando un mensaje en el LCD, tambien esta el control infra rojo que nos permite encender y apagar el programa y tambien seleccionar manualmente la estacion que querramos simular.

# Includes, variables y constantes
~~~ C
// C++ code
//
#include <LiquidCrystal.h>
#include <IRremote.h>
#include <Servo.h>

//DEFINES Y VARIABLES
#define CONTROL_IR 11 
#define OTONIO 4010852096
#define INVIERNO 3994140416
#define PRIMAVERA 3977428736
#define VERANO 3944005376
#define APAGAR 4278238976
#define SERVO 2
#define ROJO 9
#define VERDE 10

int boton;
int temperatura = 0;
String estacion = "";
bool flagApagado = false;
bool flagInicio = true;
bool flagIncendio = false;

Servo servo;
LiquidCrystal lcd(4,3,5,6,7,8);
~~~

# Setup y loop
### En el setup inicio tanto los componentes que se van a usar como los pines para los leds. El loop se encarga de medir las temperaturas y llamar a funcion detectarBoton y mostrarLCD validando en que paso del programa estamos.
~~~c
oid setup() {
  Serial.begin(9600);
  pinMode(9,OUTPUT);
  pinMode(10,OUTPUT); 
  
  //INICIO COMPONENTES
  lcd.begin(16, 2);
  IrReceiver.begin(CONTROL_IR, DISABLE_LED_FEEDBACK);
  servo.attach(SERVO);
}


void loop() {
  medirTemperatura(&temperatura);
  
  detectarBoton(&estacion,&flagInicio,&temperatura,&flagApagado);
  if (flagInicio)
  {
    
  	lcd.clear();
	mostrarLCD(temperatura,&estacion);
  }else{
    
  lcd.clear();
  }
  	delay(1000);
  	
}
~~~

### detectarBoton se encarga de detectar las señales IR del control remoto e interactuar con el, si se apreta el boton 1 se hace otoño, con el 2 se hace invierno, con el 3 se hace primavera, con el 4 se hace verano y con el boton apagar se apaga y prende el sistema.
~~~c
void detectarBoton(String* estacion,bool* flagInicio,int* temperatura,bool* flagApagado){
  
  Serial.println(IrReceiver.decodedIRData.decodedRawData);
  
  if(IrReceiver.decode() && flagInicio)
  {
    
    if (IrReceiver.decodedIRData.decodedRawData == OTONIO)
    {	
      *temperatura = 12;
      
      }
    if (IrReceiver.decodedIRData.decodedRawData == INVIERNO)
    {
      	*temperatura = 5;
      	     
    }
    if (IrReceiver.decodedIRData.decodedRawData == PRIMAVERA)
    {
      	*temperatura = 20;
      	
      	      	
    }
    if (IrReceiver.decodedIRData.decodedRawData == VERANO)
    {	
      	*temperatura = 25;
      	
           	
    }
    if (IrReceiver.decodedIRData.decodedRawData == APAGAR)
    {	
      
      if (*flagApagado){
      	*flagInicio = true;
        *flagApagado = false;
        
      }else
      {
      	*flagInicio = false;
      	*flagApagado = true;
        
      }
      	
    }
    IrReceiver.resume();
  } 	 
}


~~~


### MostrarLCD detecta si las temperaturas son menores a 60 grados celcius, si ese es el caso se mostrara la estacion y la temperatura, caso contrario mostrara un mensaje de alerta en el LCD
~~~c
void mostrarLCD(int temperatura,String* estacion)
{
  String mensajeLCD;
  conseguirEstacion(estacion,temperatura);
  if (temperatura >= 60)
  {
    flagIncendio = true;
    mensajeLCD = "ALERTA T: "+ String(temperatura) + "C";
    lcd.print(mensajeLCD);
    moverServo();
    digitalWrite(ROJO,HIGH);
    digitalWrite(VERDE,LOW);
    
  }
  else
  {
    mensajeLCD = *estacion + " "+ String(temperatura) + "C";
    lcd.print(mensajeLCD);
    digitalWrite(ROJO,LOW);
    digitalWrite(VERDE,HIGH);
  }
}

~~~
## Link al proyecto
<[Thinkercad](https://www.tinkercad.com/things/e4s5Br0xbEV)>
