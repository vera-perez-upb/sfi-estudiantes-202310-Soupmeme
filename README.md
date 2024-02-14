# Introduccion

Este es el MD referente a los trabajos de la unidad 1. 

## Metodologia de trabajo

La mayoria de trabajos se realizaran en equipo. Nuestro nombre de equipo es **ESTRELLITAS 11** conformado por Esteban Cardoso Benitez, Mateo Jimenez Fama, Mariana Echavarria Cadavid.

El link al repositorio compartido, donde se necesitaria realizar la mayoria de punto de la unidad 1 es [este repositorio]. A menos que se indique lo contrario, los archivos referentes desde el punto 4 de la Unidad 1 en adelante, estaran contenidos en ese repo.

[este repositorio]: https://github.com/MateoJimenezFamaArt/RASPBERRY_PICO/tree/master

### Unidad 1: Software para sistemas embebidos

#### Ejercicio 3-5 (LED Blink)

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
Se modifica el codigo dado para aumentar el intervalo donde el LED parpadea, de cada 100ms a 500ms.

```
void setup() {
  pinMode(LED_BUILTIN, OUTPUT); // El pin correspondiente al LED se pone en modo OUTPUT, que envie señales, no que las reciba.
}

void loop() {
  static uint32_t previousTime = 0; // unsigned integer de 32 posiciones (unsigned significa que todos los bits toman valores positivos)
  static bool ledState = true; // boolean set to true, turns on the LED

  uint32_t currentTime = millis(); // creates a counter from the moment its invoked

  if( (currentTime - previousTime) > 500){ // Un condicional que controla la diferencia entre el current y el previous time para comparar si llegan hasta un threshold definido
    previousTime = currentTime; // Si se cumple el condicional, el previousTime se setea al currentTime, para no reiniciar desde el 0 absoluto el contador
    ledState = !ledState; //Se setea el LED al estado contrario al actual. !apagado, otherwise prendido
    digitalWrite(LED_BUILTIN, ledState); // digitalWrite escribe los parametros al pin declarado desde el setup. En este caso, manda el estado de la variable ledState al pin que controla el LED_BUILTIN
  }
}
```

#### Ejercicio 6 (State Machine)

```
void task1()
{
    enum class Task1States
    {
        INIT,
        WAIT_TIMEOUT
    };
    static Task1States task1State = Task1States::INIT;

    static uint32_t lastTime = 0;

    constexpr uint32_t INTERVAL = 1000;

    switch (task1State)
    {
    case Task1States::INIT:
    {
        Serial.begin(115200);

        lastTime = millis();
        task1State = Task1States::WAIT_TIMEOUT;

        Serial.print("Task1States::WAIT_TIMEOUT\n");

        break;
    }
    case Task1States::WAIT_TIMEOUT:
    {
        uint32_t currentTime = millis();

        if ((currentTime - lastTime) >= INTERVAL)
        {
            lastTime = currentTime;
            Serial.print(currentTime);
            Serial.print('\n');
        }
        break;
    }
    default:
    {
        Serial.println("Error");
    }
    }
}

void setup()
{
    task1();
}

void loop()
{
    task1();
}
```

- ¿Cómo se ejecuta este programa?
R/: El programa utiliza una maquina de estado para llevar acabo unas tareas en especifico, o en este caso, una tarea. Se crea una clase que contiene ambos estados **INIT** y **WAIT_TIMEOUT**, donde INIT inicializa el puerto del Monitor Serial de la board, inicializa la variable *lastTime* y posteriormente declara el seguiente estado *WAIT_TIMEOUT* donde realiza un print al serial monitor. Despues, el siguiente caso del switch verifica en que estado se encuentra el *Task1State*, donde deberia estar en *WAIT_TIMEOUT*, se accede a este case donde se declara e inicializa una nueva variable llamada *currentTime*. Se realiza un if statement, donde se verifica si la diferencia de *currentTime* y *lastTime* son mayores o iguales que un intervalo (que equivale a 1s), si esto se cumple, realiza un print mediante el serial monitor donde se ve el tiempo actual y regresa al principio del switch statement, donde se cumple el ciclo: Se verifica si se encuentra en *WAIT_TIMEOUT* y se empieza a contar, cuando el conteo exceda el intervalo, se printea el tiempo actual, y se repite.


- Pudiste ver este mensaje: `Serial.print("Task1States::WAIT_TIMEOUT\n");`. ¿Por qué crees que ocurre esto?
R/: Se deja saber mediante un print al monitor serial que el estado al que se acaba de pasar es al de *WAIT_TIMEOUT*


- ¿Cuántas veces se ejecuta el código en el case Task1States::INIT?
R/: Una vez, cuando se inicializa el programa por primera vez, ya que una vez iniciado, nunca se regresa a este estado, pues el switch case nunca lo permite.


### Documented Bugs

#### Archivo de Arduino IDE no ejecutando de manera correcta. Error lanzado: Syntax

Lo resolvimos de una manera inusual. Al momento de seguir el trayecto de acitividades, nos dimos cuenta que estabamos teniendo problemas para flashear el microcontrolador del Raspberry Pico que teniamos. El IDE mencionaba que teniamos errores en el syntax de la escritura del codigo, especificamente, al momento de compilar, la consola del IDE decia que no era posible compilar debido a que la IDE estaba sobreescribiendo las funciones bases del Arduino como el ***void setup()*** y el ***void loop()***, pero esta era imposible ya que habiamos copiado el codigo multiples veces y revisado la syntaxis multiples veces. Al final recurrimos a aislar el archivo en cuestion dentro de su propio folder, y al momento de abrirlo e intentar flashearlo al Pico, funciono de manera adecuada. No entendemos cual podria ser la causa, sin embargo, puede ser posible que al tener multiples archivos .ino dentro de un mismo folder puede llevar a problemas el momento de compilarlos, ya que el compiler no tiene en cuenta cual archivo es cual. Así lo buscamos en Google:

![image](https://github.com/vera-perez-upb/sfi-estudiantes-202310-Soupmeme/assets/124329074/c02e3cc3-158c-48b8-a5de-eb0e52c57b96)


