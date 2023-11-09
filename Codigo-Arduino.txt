#include <LiquidCrystal.h>
#include <Servo.h>
//LiquidCrystal LCD(rs, enable, d4, d5, d6, d7); 

LiquidCrystal LCD(9,8,4,5,6,7);
Servo Servomotor; // nombre
float temp1 = 0.00 ;
float temp2 = 0.00 ;
float temp3 = 0.00 ;
float temp4 = 0.00 ;

int angulo=0;
int ventilacion = 12;

int rojo = 11;
int azul = 10;
int verde = 3;


void setup()
{
  Serial.begin(9600);
  LCD.begin(16,2);
  LCD.setCursor(0,0);
  
  pinMode(verde, OUTPUT);
  pinMode(azul, OUTPUT);
  pinMode(rojo, OUTPUT);
  
  pinMode(ventilacion,OUTPUT);
  
  Servomotor.attach(2); // a que pin esta asignado
}

void loop()
{
  /*
    LCD.println("Hola");
    LCD.scrollDisplayLeft();
    delay(100);
    */

  // TMP
  int valor_leido = analogRead(A0);

  temp1 = 5*valor_leido;
  temp2 = temp1 * 100;
  temp3 = temp2 / 1024;
  temp4 = temp3 - 50;

  LCD.setCursor(0,0);
  LCD.print("TMP: ");
  LCD.print(temp4);
  LCD.print(" C");

  // SENSOR DE HUMEDAD
  int sensor_hum = analogRead(A1);
  int humedad = map(sensor_hum,0,876,0,100);
  LCD.setCursor(0,1);
  LCD.print("Hum: ");
  if(humedad <= 20){
    LCD.print("Muy Seco");
  }
  else if(humedad > 20 && humedad <= 70){
    LCD.print("Seco");
  }
  else if(humedad > 70){
    LCD.print("Humedo");
  }

  // FOTORRESISTENCIA
  float luminosidad = analogRead(A2);
  float luz = map(luminosidad,1017,344,0,100);
  if (luz >= 78){
    digitalWrite(rojo,0);
    digitalWrite(azul,254);
    digitalWrite(verde,0);
  }
  else if(luz < 78){
    digitalWrite(rojo,254);
    digitalWrite(azul,0);
    digitalWrite(verde,254);
  }
  //Serial.println("LUZ: ");
  //Serial.println(luz);


// ventilacion
  if(temp4 > 30){
    digitalWrite(ventilacion,HIGH);
  }
  else{
    digitalWrite(ventilacion,LOW);
  }
  
// SISTEMA DE RIEGO
  if(humedad < 69){
    if (angulo == 0){
      delay(400);
      Servomotor.write(180);
      delay(600);
      angulo = 180;
    }
    else if(angulo == 180){
      delay(400);
      Servomotor.write(0);
      delay(600);
      angulo = 0;
    }
  }
  	delay(1000);
	LCD.clear();
}