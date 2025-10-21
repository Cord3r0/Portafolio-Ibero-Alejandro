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

```
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

```
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
```
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

En esta practica 
