# Introduccion

Este es el MD referente a los trabajos de la unidad 1. 

## Metodologia de trabajo

La mayoria de trabajos se realizaran en equipo. Nuestro nombre de equipo es **ESTRELLITAS 11** conformado por Esteban Cardoso Benitez, Mateo Jimenez Fama, Mariana Echavarria Cadavid.

El link al repositorio compartido, donde se necesitaria realizar la mayoria de punto de la unidad 1 es [este repositorio]. A menos que se indique lo contrario, los archivos referentes desde el punto 4 de la Unidad 1 en adelante, estaran contenidos en ese repo.

[este repositorio]: https://github.com/MateoJimenezFamaArt/RASPBERRY_PICO/tree/master

### Unidad 1: Software para sistemas embebidos

Los primeros puntos de esta unidad hacen un recorrido breve sobre actividades pasadas que se han realizado haciendo uso de microcontroladores dentro del curso. Despues de esto, aprendemos a realizar el setup inicial del Integrated Development Environment (IDE) de Arduino, haciendo uso de una libreria para los Raspberry Pi Pico. Posteriormente se realiza un ejercicio introductorio para compilar, buildear y flashear el Pico con un codigo simple que prende un LED cada cierto intervalo de tiempo.


```
void setup() {
  pinMode(LED_BUILTIN, OUTPUT);
}

void loop() {
  static uint32_t previousTime = 0;
  static bool ledState = true;

  uint32_t currentTime = millis();

  if( (currentTime - previousTime) > 100){
    previousTime = currentTime;
    ledState = !ledState;
    digitalWrite(LED_BUILTIN, ledState);
  }
}
```




### Documented Bugs

#### Archivo de Arduino IDE no ejecutando de manera correcta. Error lanzado: Syntax

Lo resolvimos de una manera inusual. Al momento de seguir el trayecto de acitividades, nos dimos cuenta que estabamos teniendo problemas para flashear el microcontrolador del Raspberry Pico que teniamos. El IDE mencionaba que teniamos errores en el syntax de la escritura del codigo, pero esta era imposible ya que habiamos copiado el codigo multiples veces y revisado la syntaxis multiples veces. Al final recurrimos a aislar el archivo en cuestion dentro de su propio folder, y al momento de abrirlo e intentar flashearlo al Pico, funciono de manera adecuada. No entendemos cual podria ser la causa, sin embargo, puede ser posible que al tener multiples archivos .ino dentro de un mismo folder puede llevar a problemas el momento de compilarlos, ya que el compiler no tiene en cuenta cual archivo es cual.

