# Projetos2




#include <Servo.h>
#include <DHT.h>
#include <ArduinoJson.h>

#define ledG 13
#define ledR 12
#define dhtpin 7
#define dhttype DHT11
#define servopin1 6
#define servopin2 5
bool mode = true;

DHT dht(dhtpin, dhttype);
Servo servo1;
Servo servo2;

void setup() {
  Serial.begin(9600);
  servo1.attach(servopin1);
  servo2.attach(servopin2);
  dht.begin();
  pinMode(ledG, OUTPUT);
  pinMode(ledR, OUTPUT);

}

void loop() {
 if(Serial.available()>0){
  char comando = Serial.read();
  if(comando == 'M'){
    mode = false;
  }else if(comando == 'A'){
    mode = true;
  }else if(!mode){
    if(comando == '1'){
      servo1.write(180);
      servo2.write(180);
    }else if(comando == '0'){
      servo1.write(0);
      servo2.write(0);
    }
  }
 }
 if(mode){
  int temp = dht.readTemperature();
  int umi = dht.readHumidity();

  StaticJsonDocument<100>json;
  json["Temperatura"] = temp;
  json["Umidade"] = umi;

  serializeJson(json, Serial);
  Serial.println();

  if(temp <= 30){
    digitalWrite(ledG, HIGH);
    digitalWrite(ledR, LOW);
    servo1.write(0);
    servo2.write(0);
  }else if(temp < 40){
    digitalWrite(ledG, HIGH);
    digitalWrite(ledR, LOW);
    servo1.write(180);
    servo2.write(0);
  }else{
    digitalWrite(ledG, LOW);
    digitalWrite(ledR, HIGH);
    servo1.write(180);
    servo2.write(180);
  }
 }
 

 

}
