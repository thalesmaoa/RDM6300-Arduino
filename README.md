# RDM6300-Arduino + two antennas
Credits to https://github.com/ifsc-arliones/RDM6300-Arduino.

I did a small modification so I can connect two RDM6300 to the same arduino.

Exemple:

```
#include <SoftwareSerial.h>
#include "RDM6300.h"

SoftwareSerial in_rdm_serial(8, 9);
SoftwareSerial ex_rdm_serial(2, 3);



RDM6300<SoftwareSerial> in_rdm(&in_rdm_serial);
RDM6300<SoftwareSerial> ex_rdm(&ex_rdm_serial);

int led_pin = 13;
int doorpin = 7;

void blink(int n = 1) 
{
  for(int i = 0; i < n; i++) {
    digitalWrite(led_pin, HIGH);
    delay(200);
    digitalWrite(led_pin, LOW);
    delay(200);
  }
}

void opendoor(){
  digitalWrite(doorpin, HIGH);
  delay(1000);
  digitalWrite(doorpin, LOW);
  delay(4000);
}
  

void setup()
{
  pinMode(led_pin, OUTPUT);
  pinMode(doorpin, OUTPUT);
  
  
  digitalWrite(led_pin, LOW);
  digitalWrite(doorpin, LOW);
  
  Serial.begin(115200);
  //Serial.println("SETUP");
  //blink(5);
}

void loop()
{
  uint8_t NTAG = 2; 
  unsigned long long my_id[NTAG] = {
    0xA002A71CF,
    0x15105B9D0A
  };
  unsigned long long last_id_ex = 0;
  unsigned long long last_id_in = 0;



  // Check if external reader has tag
  ex_rdm.listen();
  delay(200);
  if ( ex_rdm.available() ){
    last_id_ex = ex_rdm.read();
    Serial.print("RFID Externo: 0x");
    ex_rdm.print_int64(last_id_ex);
    Serial.println();
  }
  
  // Check if reader internal has tag
  in_rdm.listen();
  delay(200);
  if ( in_rdm.available() ){
    last_id_in = in_rdm.read();
    Serial.print("RFID Interno: 0x");
    in_rdm.print_int64(last_id_in);
    Serial.println();
  }
 
  for (uint8_t i_aux = 0; i_aux < NTAG; i_aux++){
    if(last_id_ex == my_id[i_aux]){
      blink(2);
      opendoor;
    }
    if(last_id_in == my_id[i_aux]){
      blink(2);
      opendoor;
    }
  }


}
```
