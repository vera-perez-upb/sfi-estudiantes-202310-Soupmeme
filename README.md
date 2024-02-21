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

#### Ejercicio 7

![image](https://github.com/vera-perez-upb/sfi-estudiantes-202310-Soupmeme/assets/124329074/4f08e4ac-cf1c-411c-afac-4c81aa5bae6c)

#### Ejercicio 8 

```
void task1()
{
    enum class Task1States
    {
        INIT,
        MENSAJE_1,
        MENSAJE_2,
        MENSAJE_3
    };

    static Task1States task1State = Task1States::INIT;
    static uint32_t lastTime = 0;
    static uint32_t interval = 1000;

    switch (task1State)
    {
    case Task1States::INIT:
    {
        Serial1.begin(115200);
        lastTime = millis();
        task1State = Task1States::MENSAJE_1;
        Serial1.println("Task1States::Ha Iniciado la cuenta");
        break;
    }
    case Task1States::MENSAJE_1:
    case Task1States::MENSAJE_2:
    case Task1States::MENSAJE_3:
    {
        uint32_t currentTime = millis();
        if ((currentTime - lastTime) >= interval)
        {
            lastTime = currentTime;
            Serial1.print("Ha pasado el tiempo, SEGUNDOS PASADOS SINCE LAST: ");
            Serial1.println(interval / 1000);
            interval += 1000; 
            if (interval > 3000)
            {
                interval = 1000;
            }

            switch (task1State)
            {
            case Task1States::MENSAJE_1:
                task1State = Task1States::MENSAJE_2;
                break;
            case Task1States::MENSAJE_2:
                task1State = Task1States::MENSAJE_3;
                break;
            case Task1States::MENSAJE_3:
                task1State = Task1States::MENSAJE_1;
                break;
            }
        }
        break;
    }
    default:
    {
        Serial1.println("Error");
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

- ¿Cuáles son los estados del programa?
R/: Los estados son ***INIT***, ***MENSAJE_1***, ***MENSAJE _2***, ***MENSAJE_3***

- ¿Cuáles son los eventos?
R/: Los eventos son: *If statement que verifica el paso del tiempo comparandolo con un intervalo dado*, y  *Cada que pasan 3 segundos, pues se loopea el programa*

- ¿Cuáles son las acciones?
R/: Printear mediante el monitor serial el tiempo transcurrido en cada intervalo de tiempo; Cada 1, 2 y 3 segundos, antes de esperar para el paso al siguiente estado.

#### Ejercicio 9

```
void task1()
{
    enum class Task1States
    {
        INIT,
        WAIT_FOR_TIMEOUT
    };

    static Task1States task1State = Task1States::INIT;
    static uint32_t lastTime;
    static constexpr uint32_t INTERVALA = 1000;

    switch(task1State)
    {
        case Task1States::INIT:
        {
            Serial1.begin(115200);
            lastTime = millis();
            task1State = Task1States::WAIT_FOR_TIMEOUT;
            break;
        }

        case Task1States::WAIT_FOR_TIMEOUT:
        {
          uint32_t currentTime = millis();
            if( (currentTime - lastTime) >= INTERVALA)
             {
                lastTime = currentTime;
                Serial1.print("mensaje a 1Hz from A interval\n");
                delay(4000);
             }
            break;
        }

        default:
        {
            break;
        }
    }
}

void task2()
{
    enum class Task2States
    {
        INIT,
        WAIT_FOR_TIMEOUT
    };

    static Task2States task2State = Task2States::INIT;
    static uint32_t lastTime;
    static constexpr uint32_t INTERVALB = 2000;

    switch(task2State)
    {
        case Task2States::INIT:
        {
            Serial1.begin(115200);
            lastTime = millis();
            task2State = Task2States::WAIT_FOR_TIMEOUT;
            break;
        }

        case Task2States::WAIT_FOR_TIMEOUT:
        {
           uint32_t currentTime = millis();
            if( (currentTime - lastTime) >= INTERVALB)
            {
                lastTime = currentTime;
                Serial1.print("mensaje a 0.5Hz from B interval\n");
            }
            break;
        }

        default:
        {
            break;
        }
    }
}

void task3()
{
    enum class Task3States
    {
        INIT,
        WAIT_FOR_TIMEOUT
    };

    static Task3States task3State = Task3States::INIT;
    static uint32_t lastTime;
    static constexpr uint32_t INTERVALC = 4000;

    switch(task3State)
    {
        case Task3States::INIT:
        {
            Serial1.begin(115200);
            lastTime = millis();
            task3State = Task3States::WAIT_FOR_TIMEOUT;
            break;
        }

        case Task3States::WAIT_FOR_TIMEOUT:
        {
           uint32_t currentTime = millis();
            if( (currentTime - lastTime) >= INTERVALC)
            {
                lastTime = currentTime;
                Serial1.print("mensaje a 0.25Hz from C interval\n");
            }
            break;
        }

        default:
        {
            break;
        }
    }
}
 
void setup() 
{
  task1();
  task2(); 
  task3();
}

void loop() 
{
  task1();
  task2();
  task3();
}
```
#### Ejercicio 11

![image](https://github.com/vera-perez-upb/sfi-estudiantes-202310-Soupmeme/assets/124329074/aebceb7d-1e5f-45ad-8f77-bbc28852c034)

- ¿Por qué enviaste la letra con el botón send? ¿Qué evento verifica si ha llegado algo por el puerto serial?
  
**R/=** Dentro del codigo, existe un case switch ***case Task1States::WAIT_DATA:*** que verifica si el puerto serial esta desponible (existe comunicacion send/receive) y si lo esta, hace un read de los datos que llegan y devuelve al sender el mensaje "Hola computador"
- Analiza los números que se ven debajo de las letras. Nota que luego de la r, abajo, hay un número. ¿Qué es ese número?
  
**R/=** Debido a la disposicion de configuracion del Scriptcommunicator, nosotros pusimos que el output de la consola sea 'Mixto', es decir, que se muestren valores codificados en Hexadecimal y en UTF-8 Debajo de la ultima 'r' esta el valor '72' que corresponde al codigo hexadecimal de la letra R en miniscula.
- ¿Qué relación encuentras entre las letras y los números?
  
**R/=** Las letras o el mensaje que se espera mostrar en pantalla o recibir mediante transmision por el monitor serial se codifica de alguna manera para facilitar su transmision o ahorrar cantidad de datos que se utilizan en dicha transmision. El hecho de que no solo veamos las letras como tal si no tambien su manera de ser codificadas se usa para hechos interpretativos.
  
- ¿Qué es el 0a al final del mensaje y para qué crees que sirva?
  
**R/=** 0a es hexedecimal para un comando de "line feed", cambiando de "reglon" donde se hace display de las letras. En el codigo se evidencia con el caracter de escape '\n'.


### Documented Bugs

#### Archivo de Arduino IDE no ejecutando de manera correcta. Error lanzado: Syntax

Lo resolvimos de una manera inusual. Al momento de seguir el trayecto de acitividades, nos dimos cuenta que estabamos teniendo problemas para flashear el microcontrolador del Raspberry Pico que teniamos. El IDE mencionaba que teniamos errores en el syntax de la escritura del codigo, especificamente, al momento de compilar, la consola del IDE decia que no era posible compilar debido a que la IDE estaba sobreescribiendo las funciones bases del Arduino como el ***void setup()*** y el ***void loop()***, pero esta era imposible ya que habiamos copiado el codigo multiples veces y revisado la syntaxis multiples veces. Al final recurrimos a aislar el archivo en cuestion dentro de su propio folder, y al momento de abrirlo e intentar flashearlo al Pico, funciono de manera adecuada. No entendemos cual podria ser la causa, sin embargo, puede ser posible que al tener multiples archivos .ino dentro de un mismo folder puede llevar a problemas el momento de compilarlos, ya que el compiler no tiene en cuenta cual archivo es cual. Así lo buscamos en Google:

![image](https://github.com/vera-perez-upb/sfi-estudiantes-202310-Soupmeme/assets/124329074/c02e3cc3-158c-48b8-a5de-eb0e52c57b96)


