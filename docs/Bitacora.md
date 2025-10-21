# Practicas realizadas para proyecto

## 1) Primera practica
Para la primera practica, se nos dio una pequeña clase de logica en la electronica, luego se nos paso una pagina para calcularlo que seria el capacitor y la resistencia para realizar que un led prenda 4 segundos y apague 4 segundos de manera consecutiva.

Los **materiales** que utilizamos fueron:
- 1 compuerta logica 555, que es un contador
- 1 resistencia de 300 kΩ
- 1 resistencia de 1000 kΩ
- 1 Resistencia de 220 Ω
- 1 capacitor de 4.7 µF

La pagina que se utilizo para calcular las resistencias y el capacitos fue en [Digikey](https://www.digikey.com.mx/es/resources/conversion-calculators/conversion-calculator-555-timer?srsltid=AfmBOorIMn9rovHiLriNQc45qD3LhIHQ_Ve1l8VCfuCqa09MgpDren3H) la cual nos ayudo a determinar el valor de las resistencias y el valor del capacitor, despues para saber como conectar buscamos la [Datasheet](https://www.alldatasheet.com/datasheet-pdf/view/208043/TI/NE555P.html) del contador 555

![Diagrama del sistema](recursos/imgs/imagen.png)

Despues de que se calculara todo, se tuvo que realizar las conexiones en base a todo lo sacado anteriormente, con esto se tienen se tuvo que observa que señales mandaba con el osciloscopio para ver como reaccionaba y se mandaban las señales de encendido y apagado en el circuito que se realizo. 

![Diagrama del sistema](recursos/imgs/Multimedia.jpeg)
---

## 2) Segunda practica

Antes de empezar esta practica, aprendimos que existen diversos tipos de microcontroladores, uno de ellos es el [ESP32](https://www.waveshare.com/img/devkit/accBoard/NodeMCU-32S/NodeMCU-32S-details-3.jpg), el cual es mucho mejor que un Arduino UNO, ya que este tiene conectividad a wifi y a bluetooth incluida.

Despues de eso, aprendimos algunas funciones basicas del arduino, para empezar con la primera practica. Tambien aprendimos logica para conectar circuitos lo cual seria de gran ayuda para la practica.

Terminando todo eso, procedimos a empezar la practica, procedimos a conectar el ESP32 a una protoboard y despues de eso procedimos a realizar varias conexiones con sus referentes resistencias y tambien cargamos lo que seria el codigo de Arduino para el ESP32, el cual es el siguiente:

```codigo
#define LED 23
void setup() {
    pinMode(LED, OUTPUT);
}
void loop() {
    digitalWrite(LED, HIGH); 
    delay(1000);
    digitalWrite(LED, LOW); 
    delay(1000);
}
```
El cual el resultado salo algo asi:

![Diagrama del sistema](recursos/imgs/Led%20sin%20boton.jpeg)

Despues de eso procedimos a la siguiente parte que era agregarle un boton para que enciera y apagara, para esto en la parte del codigo no cambiaria mucho solo se le agregaria definir la señal de entrada del boton y agregarle una condicional el codigo quedaria algo asi:

```codigo
#define LED 23
#define BUTTON 33

void setup() {
    pinMode(LED, OUTPUT);
    pinMode(BUTTON, INPUT);
}
void loop() {
    if (digitalRead(BUTTON) == HIGH) {
        digitalWrite(LED, HIGH);
    } else {
        digitalWrite(LED, LOW);
    }
}
```

Y el circuito se veria algo asi:

![Diagrama del sistema](recursos/imgs/led%20con%20boton.jpeg)

Por siguiente, se nos explico como conectar el ESP32 por Bluetooth y como controlarlo, esto se basa por medio de una aplicacion, la cual se llama [Serial Bluetooth Terminal](https://play.google.com/store/apps/details?id=de.kai_morich.serial_bluetooth_terminal&hl=es) en el cual manda señales las cuales las recibe el ESP32 y con estas señales se puede realizar lo anteriormente realizado pero con la aplicacio, así que el codigo cambiaria para recibir señales Bluetooth:
```codigo
#include "BluetoothSerial.h"
BluetoothSerial SerialBT;

#define LED 23

void setup() {
    Serial.begin(115200);
    SerialBT.begin("ESP32");
    pinMode(LED, OUTPUT);
}

void loop() {
    if (SerialBT.available()) {
        String mensaje = SerialBT.readString();
        Serial.println("Recibido: " + mensaje);
        if (mensaje == "prendido") {
            digitalWrite(LED, HIGH);
        } else if (mensaje == "apagado") {
            digitalWrite(LED, LOW);
        }
    }
    delay(1000);
}
```
El circuito se veria algo asi:

![Diagrama del sistema](recursos/imgs/Led%20bluetooth.jpeg
)
---
## 3) Practica 3

En esta practica se recupero lo visto con anterioridad y se veran más temas sobre como el [Puente H](https://europe1.discourse-cdn.com/arduino/original/4X/2/5/3/253f56d944d1e7b663778a00035cd1620a1a2b0f.jpeg) el cual ayuda a aumentar y regular el voltaje, junto con el amperaje, esto sera necesario para poder mover los motores a una velocidad considerable.

En el codigo se van a agregar salidas que seran la in1,in2,in3,in4 y las señales que se mandaran para las salidas de cada una. El codigo se veria algo asi:

```codigo
#define in1 32
#define in2 33

void setup() {
  pinMode(in1, OUTPUT);
  pinMode(in2, OUTPUT);
}

void loop() {

  digitalWrite(in1, 0);
  digitalWrite(in2, 1);
  delay(1000);
 
  digitalWrite(in1, 0);
  digitalWrite(in2, 0);
  delay(1000);
 
  digitalWrite(in1, 1);
  digitalWrite(in2, 0);
  delay(1000);

  digitalWrite(in1, 0);
  digitalWrite(in2, 0);
  delay(1000);
}
```
Despues de hacer que avanzara los motores, se solicito hacer que se movieran a una velocidad se tendria que agregar otra nueva señal para que sea la de velocidad, esto tambien lo puede realizar con el puente H, asi que el codigo se veria asi:

```codigo
#define in1 32
#define in2 33
#define pwm 25

void setup() {

  pinMode(in1, OUTPUT);
  pinMode(in2, OUTPUT);
  ledcAttachChannel(pwm, 1000, 8, 0);
}

void loop() {
  digitalWrite(in1, 0);
  digitalWrite(in2, 1);
  ledcWrite(pwm, 0);
  delay(500);
  ledcWrite(pwm, 51);
  delay(500);
  ledcWrite(pwm, 102);
  delay(500);
  ledcWrite(pwm, 153);
  delay(500);
  ledcWrite(pwm, 204);
  delay(500);
  ledcWrite(pwm, 255);
  delay(500);
}
```
Y las conexiones del Puente H se veran algo parecido como lo muestra la imagen:

![Diagrama del sistema](recursos/imgs/Conexiones%20puente%20H.jpeg
)

Como no importa la polaridad en los motores, se puede conectar de cualquier lado, solo cambiaria en que direccion se dirige el motor.

---

## 4) Practica 4
En la cuarta practtica se vieron el tema de servos, aunque nuestra clase fue la unica que se atraso con ese tema, logramos comprender el funcionamiento del servo, de una manera un poco mas y menos practica. Con todo esto, vimos como se conecta y como se programa en el ESP32 para que asi se mande un angulo y se mueva en ese angulo, pero con una ligera restriccion el sevo motor solo puede girar 180°. El codigo se veria algo asi:

```codigo
#define pwm 12
int duty = 0;
int grados = 0;

void setup() {
  pinMode(in1, OUTPUT);
  pinMode(in2, OUTPUT);
  ledcAttachChannel(pwm, 50, 12, 0);
  Serial.begin(115200);
}

void loop() { 
  grados=0;
  duty= map(grados, 0, 180, 205, 410);
  Serial.print("Pos: ");
  Serial.println(duty);
  ledcWrite(pwm, duty);
  delay(1000);
  grados=90;
  duty= map(grados, 0, 180, 205, 410);
  Serial.print("Pos: ");
  Serial.println(duty);
  ledcWrite(pwm, duty);
  delay(1000);
  grados=180;
  duty= map(grados, 0, 180, 205, 410);
  Serial.print("Pos: ");
  Serial.println(duty);
  ledcWrite(pwm, duty);
  delay(1000);
}
```
el map, nos ayuda a evitar hacer calculos y decidir el angulo que queremos y la funcion lo hace directamente, para mandar la señal al servo y este se movera el angulo que necesite.

 (imagen servo)

---
## 5) Practica 5 

En esta practica se nos dijo que se tiene que hacer un carrito para que compita con otros en futbol, así que el grupo se decidio dividir en diferentes partes, unos que hagan la parte de construccion. 
En mi caso, me dedique a hacer parte de programación y apoyar en el armado. En la parte de programación me dedique ah realizar el movimiento de los servo y una aplicacion de movil, junto a otros de mis compañeros.

Lo primero que se realizo para el servo, fue hacer que se moviera con la aplicacion que nos se habia mencionado anteriomente, esto hacia que se moviera hasta ciertos angulos en especifico, pero no dejaba mover libremente el servo. El codigo quedo de la siguiente manera

```codigo

```
