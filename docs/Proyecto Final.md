# Documentación del Proyecto Final
 
> Ball and Plate System
---
 
## 1) Introducción
 
- **Nombre del proyecto:** _Ball and Plate System_  
- **Equipo / Autor(es):** _Alejandro Cordero Gónzalez, José Manuel Góngora Compeán, Diego Barriga Gómez, Pedro Emmanuel García Elvira, Santiago Herrera Conde_  
- **Curso / Asignatura:** _Introducción a la Mecatrónica_  
- **Fecha:** _05/12/2025_  
- **Descripción breve:** _Construir una plataforma que logre mantener en equilibrio una pelota_
 
---
 
## 2) Materiales
 
**Materiales**
- PLA para impresión 3D 
- ESP32
- Jumpers
- Pilas de 3.7V
- Portapilas
- Servomotores
---
 
## 3) Procedimiento
 
**Diseño**
- _El prototipo fue diseñado basándose en imágenes de proyectos similares en internet para entender el funcionamiento de la mecánica.
- _Después se comenzó el diseño para imprimirse algunas piezas en 3D mientras que algunas bases y soportes se hicieron en corte laser con mdf_
- _Se imprimió lo que se debía imprimir en 3D y también se cortó en laser y aunque hubo problemas con las medidas se volvió a imprimir y cortar para lograr el soporte que se buscaba_
---
 
## 4) Programación

**Codigo de Python**
```Python
-_import cv2
import numpy as np
import bluetooth
import time
# FUNCTION TO CONNECT TO ESP32
port = 1
sock = bluetooth.BluetoothSocket()  # Use the default constructor (no arguments)
sock.settimeout(20)
video = cv2.VideoCapture(0)
W= 640
H= 480
print("Attempting to connect to ESP32...")
while True:
    try:
        sock.connect(("68:25:DD:2E:E0:82", port))
        print("Connected to ESP32!")
        break
    except Exception as e:
        print("Error in connection... retrying:", e)
while True:
ok,frame = video.read()
  if not ok:
        break
   imagen = frame.copy()
     hsv=cv2.cvtColor(imagen,cv2.COLOR_BGR2HSV)
    bajo= np.array([0,50,50], dtype=np.uint8)
    alto= np.array([10,255,255], dtype=np.uint8)
    mask = cv2.inRange(hsv, bajo, alto)
  result=cv2.bitwise_and(imagen,imagen, mask=mask)
    lista_contornos, jerarquia = cv2.findContours(mask , cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
  area_grande = 0
     for contorno_n in lista_contornos:
        area = cv2.contourArea(contorno_n)
        if area > area_grande: 
           area_grande=area
  contorno_final = contorno_n
        else:
   continue
(x,y), radio= cv2.minEnclosingCircle(contorno_final)
cv2.circle(frame,(int(x),int(y)), int(radio),(255,0,0),3)
cv2.circle(frame,(int(x),int(y)), 3,(255,0,0),3)
   w=frame.shape[1]
    h=frame.shape[0]
  Errorx=int(x-(w/2))
    Errory=int(y-(h/2))
    mensaje=str(Errorx)+','+str(Errory)+'\n'
    print("Sent:",mensaje)
    mensaje=str(Errorx)+','+str(Errory)+'\n'
    try:
        sock.send(mensaje.encode())
        print("Sent:",mensaje)
    except Exception as e:
        print("Error sending data:", e)
     cv2.imshow("camara",frame)
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break
video.release()_
```
 
 
Este código trabaja para el funcionamiento de la cámara para que detecte en un punto la pelota y detecte el error en el área de visión de la cámara.
 
 
**Código Arduino para ESP32**
```Arduino 
float kp=0.8;
float ki=0.01;
float kd=0.8;
#include "BluetoothSerial.h"

// Check if Bluetooth is available
#if !defined(CONFIG_BT_ENABLED) || !defined(CONFIG_BLUEDROID_ENABLED)
#error Bluetooth is not enabled! Please run `make menuconfig` to and enable it
#endif
// Check Serial Port Profile
#if !defined(CONFIG_BT_SPP_ENABLED)
#error Serial Port Profile for Bluetooth is not available or not enabled. It is only available for the ESP32 chip.
#endif
#define SERVO_PIN1 32
#define SERVO_PIN2 33
float dtx=0;
float t_nowx=0;
float t_befx=0;
int error_antx=0;
float dty=0;
float t_nowy=0;
float t_befy=0;
int error_anty=0;
BluetoothSerial SerialBT;
void setup() {
  Serial.begin(115200);
  SerialBT.begin("tommy11");  //Bluetooth device name
  //SerialBT.deleteAllBondedDevices(); // Uncomment this to delete paired devices; Must be called after begin
  ledcAttach(SERVO_PIN1, 50, 8);
  ledcAttach(SERVO_PIN2, 50, 8);
}
void loop() {
  if (SerialBT.available()) {
    String msj = SerialBT.readStringUntil('\n');
    Serial.println(msj);
    //157,180
    String EX_str = msj.substring(0,msj.indexOf(','));//Corta el mensaje de pos 0 y hasta la ,
    int Ex_int = EX_str.toInt();
    String EY_str = msj.substring(msj.indexOf(',')+1);//Corta el mensaje de pos 0 y hasta la ,
    int Ey_int = EY_str.toInt();
    t_nowx = millis();
    dtx=t_nowx-t_befx;
   Ex_int= map(Ex_int, 0,90,-320,320);
    float Px = kp *Ex_int;
    double integralx = integralx + Ex_int * dtx;
    float Ix = ki * integralx;
    int derivadax= (Ex_int-error_antx)/dtx;
    float Dx = kd * derivadax;
    int posx = Px + Ix + Dx;
    int error_antx=Ex_int;
    t_befx=t_nowx;
   Serial.print(Ex_int);
    int moverx= map(Ex_int, 0, 180, 205, 410);
    ledcWrite(SERVO_PIN1, moverx);

   t_nowy = millis();
    dty=t_nowy-t_befy;
    Ey_int=map(Ey_int,0,90,-240,240);
    //Convierte erro en pixel a erro en grados
    float Py=kp*Ey_int;
    double integraly= integraly + Ey_int *dty;
    float Iy =ki * integraly;
    int derivaday=(Ey_int-error_anty)/dty;
    float Dy = kd* derivaday;
    int posy= Py+ Iy + Dy;
    error_anty= Ey_int;
    t_befy=t_nowy;
  int movery= map(Ey_int, 0, 180, 205, 410);
    Serial.println(Ey_int);
    Serial.println(movery);
    ledcWrite(SERVO_PIN2,movery);

  }
  //EX =  -W/2 -> w/2 = -320 a 320
  //EY =  -H/2 -> H/2 = -240 a 240
  delay(20);
}
```
Este código decodifica las señales mandadas por la cámara para transformarlas en movimientos de los Servomotores y lograr el movimiento que se desea
 
 
## 5) Resultados
 
**Resultado**
- _Funcionó parcialmente ya que a pesar de que el código y el funcionamiento de los servomotores fue correcto, el motor no aguantó completamente el peso de la base por lo que no se movia de manera satisfactoria por lo que la pelota al momento de moverla con cierta fuerza, la plataforma dejaba de lograr el movimiento completo y no la mantenía en equilibrio._
- _A pesar de que tuvo esos errores, si el diseño y distribución de peso hubiera sido mejor controlado hubiera funcionado perfectamente._
---