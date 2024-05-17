# Introduccion

Este es el MD referente a los trabajos de la unidad 1. 

## Metodologia de trabajo

La mayoria de trabajos se realizaran en equipo. Nuestro nombre de equipo es **ESTRELLITAS 11** conformado por Esteban Cardoso Benitez, Mateo Jimenez Fama, Mariana Echavarria Cadavid.

El link al repositorio compartido, donde se necesitaria realizar la mayoria de punto de la unidad 1 es [este repositorio]. A menos que se indique lo contrario, los archivos referentes desde el punto 4 de la Unidad 1 en adelante, estaran contenidos en ese repo.

[este repositorio]: https://github.com/MateoJimenezFamaArt/RASPBERRY_PICO/tree/master

## Unidad 1: Software para sistemas embebidos

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

#### Ejercicio 12

- ¿Cómo se declara un puntero?
**R/=** En la sintaxis que utilizamos en C++, un puntero se declara como otro tipo de variable de unsigned integer, es decir 'uint32_t' que es un "unsigned integer" es decir, un entero de base 32bit que no admite valores negativos. Despues, se establece la el nombre de la variable, es decir, '*pvar' donde "*" declara la variable como un *pointer* y "pvar" por concesion de nombramiento, establece el nombre de esa variable como "pointer of var" para simpleza de lectura, despues se hace un '= &var' donde el ampersand refiere a la direccion de memoria donde esta almacenado 'var'.


- ¿Cómo se define un puntero? (cómo se inicializa)
**R/=** La defincion de puntero referiria al argumento, o mas bien, a la variable a quien va a apuntar, que en este caso, nuestro puntero 'pvar' apuntaria, a la variable 'var' refiriendose a su direccion de memoria en vez de definir o llamar la variable como tal.


- ¿Cómo se obtiene la dirección de una variable?
**R/=** Mediante el uso del simbolo Ampersand '&'.


- ¿Cómo se puede leer el contenido de una variable por medio de un puntero?
**R/=** Podemos leer el contenido de una variable si llamamos a su puntero. En el caso de nuestro ejemplo, si llamamos a nuestro puntero 'pvar' este daria como output el contenido en memoria de nuestra variable 'var'. En resumidas cuentas, en vez de llamar directamente a nuestra variable y asi obtener su valor, llamamos a su puntero para que nos diga que datos se encuentran almacenados en la direccion de memoria a la que esta apuntando.


- ¿Cómo se puede escribir el contenido de una variable por medio de un puntero?
**R/=** Para escribir el contenido de una variable, podemos hacerlo mediante su puntero en vez de hacerlo directamente sobre nuestra variable. Si queremos hacer que nuestra variable 'var' tome el valor de 10, por ejemplo, podemos hacer: ***pvar = 10;*** donde pvar = 10 significa en palabras simples, que almacene 10 en pvar, pero pvar es un puntero que apunta a la direccion de memoria de 'var', entonces al hacer pvar = 10, estamos haciendo por proxy que 'var' sea igual a 10, ya que el puntero no almacena valores, si no que solo almacena referencias a otras variables.


#### Ejercicio 13

El programa crea una funcion.

```
static void changeVar(uint32_t *pdata)
{
    *pdata = 10;
}
```
La cual recibe como argumento un puntero, y a dicho punto le asigna el valor de 10.

Despues, durante los eventos del estado **WAIT_DATA** realiza lo siguiente:

```
uint32_t var = 0;
            uint32_t *pvar = &var;
            printVar(*pvar);
            changeVar(pvar);
            printVar(var);
```
Donde crea la variable var y la inicializa en 0, crea el puntero pvar y hace que referencie a la direccion de memoria de la variable var, printea el valor del puntero pvar (por consiguiente, el valor de var) y llama el metodo changeVar donde como argumento ingresa el puntero pvar. Antes de llamar al metodo, pvar es igual a 0 ya que var es igual a 0. Cuando se llama al metodo, se cambia el valor de pvar a 10 y por ende el valor de var a 10, ya que el metodo recibe un puntero como argumento y a ese puntero le asigna el valor de 10. Despues printea el nuevo valor de var.

#### Ejercicio 16
Serial.available() actua como una especie de trigger, donde "lee" si hay datos recibidos en el buffer serial. Solo "lee" el estado del buffer, es decir, solo te dice si existen datos recibidos o no, mas no los almacena.
Serial.read() es el metodo que permite leer y almacenar los datos que se recibieron en el buffer serial, los permite parsear y almacenar en una varible. Primero se deben detectar los datos (haciendo un Serial.available y que este sea TRUE, es decir, que hayan datos) y despues se parsean mediante Serial.read() para almacenarlos.
Si no existen datos en el buffer serial, devuelve un -1:

![image](https://github.com/vera-perez-upb/sfi-estudiantes-202310-Soupmeme/assets/124329074/bf2be8c6-eaeb-46d5-8d67-66bc3b1641dc)

El Serial.read() lee byte por byte.
Si no se realiza un Serial.read() apesar de haber recibido datos, entonces los datos quedaran almacenados en el buffer serial hasta que sufra un "overflow" donde empezara a reemplazar datos antiguos con datos nuevos constantemente hasta que finalize el rpgrama o se haga un Serial.read()

### Entrega final unidad 1

El siguiente apartado corresponde a la entrega del ejercicio final de la unidad 1 con sus elementos requeridos:

Video de documentacion: <https://drive.google.com/file/d/11NOBZgPiRUmASY-IAY7UUUiAnkivs7zF/view?usp=sharing>

Link a repositorio donde esta el .ino del ejercicio: <https://github.com/MateoJimenezFamaArt/PROYECTOSPICO/tree/master/BombaTermonuclearAsesina>

Link a diagrama de maquina de estados: <https://app.creately.com/d/qCcTeYTp3eb/view>

## Unidad 2: Protocolos ASCII

#### Ejercicio 1

```
void setup() {
  Serial.begin(115200);
}

void loop() {
if(Serial.available()){
if(Serial.read() == '1'){
      Serial.print("Hello from Raspberry Pi Pico");
    }
  }
}
```
Este script de arduino inicializa el monitor serial a un rate de 115200 y espera recibir un dato desde el pc, el cual debe ser el numero 1, si recibe dicho dato printea el mensaje "Hello from Raspberry Pi Pico", de lo contrario no hace nada.

#### Ejercicio 2

```
using UnityEngine;
using System.IO.Ports;
using TMPro;

public class Serial : MonoBehaviour
{
	private SerialPort _serialPort =new SerialPort();
	private byte[] buffer =new byte[32];

	public TextMeshProUGUI myText;

	private static int counter = 0;

	void Start()
    {
        _serialPort.PortName = "COM3";
        _serialPort.BaudRate = 115200;
        _serialPort.DtrEnable =true;
        _serialPort.Open();
        Debug.Log("Open Serial Port");
    }

void Update()
    {
        myText.text = counter.ToString();
        counter++;

				if (Input.GetKeyDown(KeyCode.A))
        {
            byte[] data = {0x31};// or byte[] data = {'1'};            
						_serialPort.Write(data,0,1);
            int numData = _serialPort.Read(buffer, 0, 20);
            Debug.Log(System.Text.Encoding.ASCII.GetString(buffer));
            Debug.Log("Bytes received: " + numData.ToString());
        }
    }
}
```
Este script de C# para Unity, se utiliza para la comunicacion entre Unity y un Arduino, mediante un puerto serial. Utilizamos las librerias necesarias para invocar los metodos y funciones que necesitamos. Declaramos una variable "_serialPort" de la clase SerialPort para realizar las configuraciones necesarias del puerto serial mas adelante, tambien, declaramos un vector de datos para guardar los bytes que llegen por el puerto serial.

En el metodo Start invocamos el puerto y el baudrate que vamos utilizar para la comunicacion, abrimos el puerto y realizamos un print en la consola para significar la inicializacion del mismo.

En el metodo Update declaramos un contador sencillo que utiliza el gameobject de TxtMeshPro que tenemos dentro del proyecto de Unity. Despues, realizamos un if statement donde la prueba logica es verificar si la tecla 'A' esta siendo presionada, de ser asi, envia el byte 0x31 (que corresponde al caracter 1) mediante el puerto serial, despues, usando el metodo de Write de la instancia de la clase de serialPort que declaramos, ponemos los argumentos necesarios: utilizando el vector (array) 'data', en la posicion de indice 0 (primera posicion), escribimos un byte de datos que recibimos.

Despues de escribir el dato que enviamos y posteriormente recibimos, declaramos una nueva variable de enteros llamada "numData" cuya declaracion es igual al metodo Read de la instancia de serialPort donde utilizamos el vector 'buffer', en su posicion de indice 0, para leer hasta 20 bytes desde el puerto serial.

Realiza un print a la consola de los datos recibidos mediante codificacion de ASCII, y despues printea la cantidad de bytes recibidos.

#### Ejercicio 3

```
void task()
{
		enum class TaskStates
		{
				INIT,
				WAIT_INIT,
				SEND_EVENT
		};
		static TaskStates taskState = TaskStates::INIT;
		static uint32_t previous = 0;
		static u_int32_t counter = 0;

		switch (taskState)
		{
				case TaskStates::INIT:
				{
						Serial.begin(115200);
						taskState = TaskStates::WAIT_INIT;
						break;
				}
				case TaskStates::WAIT_INIT:
				{
						if (Serial.available() > 0)
						{
								if (Serial.read() == '1')
								{
										previous = 0; // Force to send the first value immediately
										taskState = TaskStates::SEND_EVENT;
								}
						}
						break;
				}
				case TaskStates::SEND_EVENT:
				{
						uint32_t current = millis();
						if ((current - previous) > 2000)
						{
								previous = current;
								Serial.print(counter);
								counter++;
						}
						if (Serial.available() > 0)
						{
							  if (Serial.read() == '2')
							  {
								    taskState = TaskStates::WAIT_INIT;
							  }
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
		task();
}

void loop()
{
		task();
}
```
En este script de Arduino, estamos creando un enumerador para declarar los estados que vamos a utilizar para una maquina de estados finita, los estados siendo INIT, WAIT_INIT y SEND_EVENT. Despues, declaramos una variables globales para controlar la transicion de estados, un contador, y una variable para conocer el tiempo transcurrido desde la ultima instruccion enviada/recibida.

Realizamos el switch case para controlar los cambios entre estados, donde en el estado INIT, abrimos el puerto serial a un baudrate definido y pasamos al estado WAIT_INIT

En este estado, verificamos si el puerto serial esta abierto, y de ser asi, esperamos a recibir el byte correspondiente al caracter "1", y cuando lo recibimos, pasamos al evento SEND_EVENT.

En SEND_EVENT, declaramos una variable local para hacer tracking del tiempo transcurrido, hacemos la variable "previous" igual al tiempo transcurrido hasta el momento, imprimimos el contador y lo aumentamos. Despues, verificamos si la conexion sigue abierta, y si es asi, esperamos a recibir el byte correspondiente al caracter "2", y cuando lo recibimos, pasamos de vuelta al estado WAIT_ INIT.

En resumen, este codigo inicializa la comunicacion serial, espera a recibir "1" para imprimir y aumentar un contador, y cuando reciba el "2" para de contar y se devulve a esperar a recibir otro "1".

```
using UnityEngine;
using System.IO.Ports;
using TMPro;

enum TaskState
{
    INIT,
    WAIT_START,
    WAIT_EVENTS
}

public class Serial : MonoBehaviour
{
		private static TaskState taskState = TaskState.INIT;
		private SerialPort _serialPort;
		private byte[] buffer;
		public TextMeshProUGUI myText;
		private int counter = 0;

		void Start()
    {
        _serialPort =new SerialPort();
        _serialPort.PortName = "COM3";
        _serialPort.BaudRate = 115200;
        _serialPort.DtrEnable =true;
        _serialPort.Open();
        Debug.Log("Open Serial Port");
        buffer =new byte[128];
    }

void Update()
    {
        myText.text = counter.ToString();
        counter++;

				switch (taskState)
        {
						case TaskState.INIT:
		            taskState = TaskState.WAIT_START;
                Debug.Log("WAIT START");
								break;
						case TaskState.WAIT_START:
								if (Input.GetKeyDown(KeyCode.A))
                {
                    byte[] data = {0x31};// start
                    _serialPort.Write(data,0,1);
                    Debug.Log("WAIT EVENTS");
                    taskState = TaskState.WAIT_EVENTS;
                }
								break;
						case TaskState.WAIT_EVENTS:
								if (Input.GetKeyDown(KeyCode.B))
                {
                    byte[] data = {0x32};// stop
                    _serialPort.Write(data,0,1);
                    Debug.Log("WAIT START");
                    taskState = TaskState.WAIT_START;
                }
								if (_serialPort.BytesToRead > 0)
                {
                    int numData = _serialPort.Read(buffer, 0, 128);
                    Debug.Log(System.Text.Encoding.ASCII.GetString(buffer));
                }
								break;
						default:
                Debug.Log("State Error");
								break;
        }
    }
}
```

En el lado de Unity tenemos las librerias que nos permiten usar funciones especificas y despues declaramos, como en el Arduino, un enumerador para los estados de nuestra maquina de estados finita, con los estados INIT, WAIT_START y WAIT_EVENTS.

Declaramos unas variables globales, las cuales controlan los estados  sus transiciones, una para utilizar las funciones necesarias del puerto serial, un vector llamado buffer, la declaracion de TMP y un contador que inicializamos en 0.

En el metodo de Start inicializamos una nueva comunicacion con el puerto serial, declaramos que puerto de conexion utilizara, el baudrate, lo "prendemos" y lo abrimos para la comunicacion, como tambien abrir 128 espacios en el vector "buffer"

En el metodo de Update, actualizamos cada frame el texto que tenemos en el GameObject dentro de Unity con TMP, y lo aumentamos por 1 cada ciclo. Despues, hacemos un switch case para controlar los cambios entre estados de nuestra maquina.

El estado INIT pasa inmediatamente al estado WAIT_START. Este estado espera que se presione la tecla "A", y al momento de hacerlo, envia, mediante el puerto serial con la funcion "_serialPort.Write" el byte correspondiente al caracter "1", printea que se pasara al estado WAIT_EVENTS y pasa de estado.

En este estado, espera que se presione la tecla "B", de ser asi, envia por el puerto serial dicho caracter, printea el paso al estado anterior, y pasa a ese estado.

Tenemos tambien una funcion que lee el buffer de datos para detectar los datos que se encuentran en el, si hay datos, codifica los datos (que estan en ASCII) a una string y los printea.

## Unidad 3 Protocolos Binarios

#### Ejercicio 1

En este ejercicio se propone un caso de estudio, especificamente se habla de un lector de RFID y la manera en como se comunica este lector con otros dispositivos llamados hosts.

Se establece primero lo que es la **INTERFAZ DE COMUNICACION SERIAL** donde el lector se comunica con un host (ya sea un microcontrolador o un microprocesador) utilizando protocolos de interfaz como el RS232 o el RS485 que son estandares de comunicacion.

Despues, se establecen los **PARAMETROS DE COMUNICACION** donde se decide el baudrate a utilizar durante el proceso (siendo en este caso 57600 bps) y el formato de la data a enviar/recibir, siendo esta conformada por:

- 1 bit de inicio
- 8 bits de data
- 1 bit de finalizacion

Estos bits componen la estructura del mensaje y tiene que ser utilizado por ambos dispositivos para determinar en que momento inicia un segmento de comunicacion y en que momento termina dicho segmento.

Por ultimo, se habla del **ORDEN DE LOS BITS**, es decir, si durante la transmision de los datos, se transmitira el bit mas significativo primero o el bit menos significativo primero. Esto es importante ya que esto determina el orden con que se manipula y se lee los datos recibidos en un protocol binario. Ambos dispositivos deben "acordar" si envian/leen el menos significativos primero o de lo contrario el mas significativo primero, de lo contrario la data que se envia de manera bidireccional seria distinta para ambos dispositivos.

#### Ejercicio 2

Durante este ejercicio se plantea un recodatorio de los metodos que se utilizan en el IDE de Arduino para trabajar con las comunicaciones seriales que vienen integradas en dichos dispositivos. 

Vemos metodos como:

- Serial.available() determina si el buffer del puerto serial tiene algun tipo de dato dentro de el. Es decir, solo "lee" si hay datos, mas no sabe que datos hay.
- Serial.read() lee los datos como tal que estan dentro del buffer serial, despues de haberse determinado que existen datos en el.
- Serial.readBytes(buffer, length) el cual permite leer una seccion determina del buffer, indicando la "longitud" de bytes a leer
- Serial.write() que es mediante el cual enviamos datos a traves del puerto serial en una comunicacion bidireccional con otro dispositivo o con otra aplicacion.

#### Ejercicio 3

Durente este ejercicio se menciona el concepto de ***endian***

Se trata basicamente de lo que se discutio en el ejercicio 1, lo cual es orden de transmision de la data. Cuando hablamos de un ***BIG ENDIAN*** estamos hablando de que se transmite primero en la cadena de datos el byte de mayor peso, mientras que si hablamos de un ***LITTLE ENDIAN*** se transmite primero en la cadena de datos el byte de menos peso.

Cabe destacar que saber que endian se va a utilizar al momento de establecer el protocolo de comunicacion es de suma importancia, ya que determinar que endian se transmite determina que orden la data se transmite y en que orden la data se lee. Una diferencia entre los dispositivos en comunicacion con respecto al endian que envian y el endian que reciben significaria data que no se esta enviando/recibiendo como se debe hacer.

#### Ejercicio 4

Durente este ejercicio se plantea la transmision de numeros de punto flotante, es decir, numeros decimales.
```
void setup() {
    Serial.begin(115200);
}

void loop() {
    // 45 60 55 d5
    // https://www.h-schmidt.net/FloatConverter/IEEE754.html
    static float num = 3589.3645;

    if(Serial.available()){
        if(Serial.read() == 's'){
            Serial.write ( (uint8_t *) &num,4);
        }
    }
}
```
Este primer codigo ejemplifica la manera en como se transmite un flotante de manera directa haciendo uso de la funcion serial.write. Transmite 4 bytes los cuales son la representacion de los flotantes. De la misma manera que lo hace el siguiente:
```
void setup() {
    Serial.begin(115200);
}

void loop() {
// 45 60 55 d5// https://www.h-schmidt.net/FloatConverter/IEEE754.htmlstatic float num = 3589.3645;
static uint8_t arr[4] = {0};

if(Serial.available()){
if(Serial.read() == 's'){
            memcpy(arr,(uint8_t *)&num,4);
            Serial.write(arr,4);
        }
    }
}

```

La diferencia entre ambos codigos es la siguiente: En el primer snippet, se castea la direccion de memoria de un uint8_t sobre un puntero y se envian 4 bytes de manera directa, mientras que en el segundo primero declaramos e inicializamos un arreglo de 4 indices. Al momento de mandar el numero mediante la comunicacion serial, casteamos 4 bytes, provenientes de la direccion de memoria de nuestra variable num, sobre un puntero de tipo uint8_t y los copiamos a nuestro array, despues, enviamos desde el array las primeras 4 posiciones de su indice.

#### Ejercicio 5

Para este ejercicio, se requiere utilizar el concepto de endianness para enviar 2 numeros de punto flotante utilizando ambos endian (big and little). Para esto, se puede reutilizar el codigo del ejercicio anterior para enviar un numero en big endian y simplemente añadirle la capacidad de enviar un numero distinto utilizando little endian, de la siguiente manera:
```
void setup() {
    Serial.begin(115200);
}

void loop() {
    // Original floating-point number (big endian)
    static float num1 = 3589.3645;
    // New floating-point number (little endian)
    static float num2 = 1234.5678;
    static uint8_t arr[4] = {0};

    if (Serial.available()) {
        char trigger = Serial.read();
        if (trigger == 's') {
            // Send num1 in big-endian format
            memcpy(arr, (uint8_t *)&num1, 4);
            for (int8_t i = 3; i >= 0; i--) {
                Serial.write(arr[i]);
            }
        } else if (trigger == 'r') {
            // Send num2 in little-endian format
            memcpy(arr, (uint8_t *)&num2, 4);
            for (int8_t i = 0; i < 4; i++) {
                Serial.write(arr[i]);
            }
        }
    }
}

```
Cabe destacar que se puede utilizar el mismo array para el proposito del ejercicio. Si enviamos un numero despues del otro, los contenidos del array se sobrescriben ya que mediante memcpy estamos transfiriendo el mismo numero de bytes, por lo que no habria data residual.

## Unidad 3 Protocolos Binarios

#### Ejercicio 3

```
uint32_t last_time = 0;

void setup()
{
    Serial.begin(9600);
}

void loop()
{
	// Print a heartbeat
	if ( (millis() - last_time) >  2000)
    {
        Serial.print("Arduino is alive!!");
        Serial.print('\n');
        last_time = millis();
    }

// Send some message when I receive an 'A' or a 'Z'.
switch (Serial.read())
    {
			case 'A':
            Serial.print("That's the first letter of the abecedarium.");
            Serial.print('\n');
						break;
			case 'Z':
            Serial.print("That's the last letter of the abecedarium.");
            Serial.print('\n');
						break;
    }
}
```
Este codigo para el Raspberry Pi Pico es similar a otros que hemos realizado en el pasado para establecer comunicacion entre el microcontrollador y otro dispositivo mediante el puerto serial, aunque cuenta con algunas diferencias:

Establecemos el baudrate en 9600 bits por segundo.
En nuestra funcion loop creamos un 'latido' para saber que la comunicacion establecida todavia se mantiene. Este latido se printea en cada ciclo del update mediante un Serial.print para dejarle saber al otro dispositivo que todavia tenemos una conexicon establecida.
Creamos un switch case en el printeamos un mensaje dependiendo del caracter que recibamos, sea la letra A o la letra Z y lo combinamos con un caracter de escape.

De acuerdo a las caracteristicas que observamos en el codigo, podemos afirmar que estamos trabajando mediante comunicacion serial haciendo uso de un protocolo ASCII, ya que no declaramos ningun array ni hacemos transformacion de strings en bytes individuales.

Despues de realizar una serie de pruebas, ya que desde Unity no estabamos detectando ningun mensaje entrante, pudimos presenciar el funcionamiento del codigo:

![image](https://github.com/vera-perez-upb/sfi-estudiantes-202310-Soupmeme/assets/124329074/f286a3f7-d931-4eaf-a905-54e812e08e41)

Siguiendo en el desarrollo del ejercicio, procedemos a hacer algunas pruebas relacionadas al funcionamiento del codigo presentando.

Primero, al analizar el script principal con el que estamos trabajando, nos damos cuenta de que estamos trabajando la comunicacion serial de una manera muy inteligente, ya que en vez de tener toda nuestra funcionalidad en un solo script que le ponemos a un GameObject, en esta ocasion el script que le asignamos tiene distintos puntos de fallo, diseminados en muchos otros scripts que controlan aspectos distintos de las funcionalidades de la comunicacion.
Una de las mas importantes es la manera segmentada en la cual tratamos el puerto serial, ya que utilizamos la programacion orientada a objetos para referenciar nuestro puerto, nuestro baudrate, nuestro puerto COM entre otros. Si borramos las referencia en el apartado publico de nuestro script, el script deja de funcionar, ya que esta ligado a un GameObject en especifico.

Tambien nos damos cuenta de que los mensajes que queremos enviar no solo estan puestos en el metodo loop en nuestro raspberry, si no que tambien estan puestos en el metodo update dentro de Unity. Esto significa en terminos practicos que la lectura de inputs, el envio de outputs y la lectura de los mismos se realizar cada que vez que se hace un accion de 'polling', es decir, cada vez que Unity invoca el metodo Update(). Asi, nos aseguramos de estar leyendo constantemente los caracteres que debemos recibir y los mensajes que debemos enviar.



## Documented Bugs

#### Archivo de Arduino IDE no ejecutando de manera correcta. Error lanzado: Syntax

Lo resolvimos de una manera inusual. Al momento de seguir el trayecto de acitividades, nos dimos cuenta que estabamos teniendo problemas para flashear el microcontrolador del Raspberry Pico que teniamos. El IDE mencionaba que teniamos errores en el syntax de la escritura del codigo, especificamente, al momento de compilar, la consola del IDE decia que no era posible compilar debido a que la IDE estaba sobreescribiendo las funciones bases del Arduino como el ***void setup()*** y el ***void loop()***, pero esta era imposible ya que habiamos copiado el codigo multiples veces y revisado la syntaxis multiples veces. Al final recurrimos a aislar el archivo en cuestion dentro de su propio folder, y al momento de abrirlo e intentar flashearlo al Pico, funciono de manera adecuada. No entendemos cual podria ser la causa, sin embargo, puede ser posible que al tener multiples archivos .ino dentro de un mismo folder puede llevar a problemas el momento de compilarlos, ya que el compiler no tiene en cuenta cual archivo es cual. Así lo buscamos en Google:

![image](https://github.com/vera-perez-upb/sfi-estudiantes-202310-Soupmeme/assets/124329074/c02e3cc3-158c-48b8-a5de-eb0e52c57b96)


