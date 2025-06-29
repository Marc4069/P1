# P1

# PRACTICA 1  :  BLINK

El objeticvo de la practica es producir el parpadeo periodico de un led. 
Se utilizara la salida serie  para depurar  el programa 

El microcontrolador que utilizaremos es el ya comentado en la introducción  **ESP32**

![](https://ae04.alicdn.com/kf/S8dee2f4cafc344e1b57ebc21ad5c11a4P.jpg?fit=600%2C600&ssl=1)



La distribucion de pines de este procesador  es el siguiente 

![](https://ae04.alicdn.com/kf/S61a9f7eb6ad3487ca95acc2f410157a35.jpg?resize=966%2C574&ssl=1)


## FUNCIONALIDAD DE LA PRACTICA

1. Iniciar pin de led como salida 
2. bucle infinito 
    * encender led  
    * espera de 500 milisegundos
    * apagar led 
    * espera de 500 milisegundos

## Código básico

```c
#define LED_BUILTIN 2
#define DELAY 500

void setup() {
  pinMode(LED_BUILTIN, OUTPUT);
}

void loop() {
  digitalWrite(LED_BUILTIN, HIGH);
  delay(DELAY);
  digitalWrite(LED_BUILTIN, LOW);
  delay(DELAY);
}

```

### Referencia

* https://github.com/schacon/blink

## TRABAJOS Y PREGUNTAS 

1. Generar el programa  y subir el codigo  al github de cada uno
2. Modificar el programa para que incluya el envio de datos (ON y OFF) al puerto serie.
    Añadir la iunicialización del puerto serie y el envio cada vez que cambia el estado del led
   - Iniciar pin de led como salida 
   - Iniciar el terminal serie                      
   - bucle infinito  
       * encender led  
       * sacar por puerto serie mensaje *ON*          
       * espera de 1000 milisegundos  
       * apagar led  
       * sacar por puesto serie mensaje *OFF*        
       * espera de 1000 milisegundos  
  
  Sugerencias. Utilizr las funciones:

    > Serial.begin(115200); 

    > Serial.println("ON"); 

    > Serial.println("OFF"); 


https://electropeak.com/learn/getting-started-with-platformio-ide-to-program-esp32/

3. Modificar el programa para que actue directamente sobre los registros de los puertos de entrada y salida
   
  Sugerencias. Utilizr lineas del tipo:

    > uint32_t *gpio_out = (uint32_t *)GPIO_OUT_REG;   // Establecer un puntero al registro de I/O
    
    > *gpio_out |= (1 << 2); // Activar el bit correspondiente al pin 2

    > *gpio_out ^= (1 << 2); // Alternar el estado del bit correspondiente al pin 2

4. Eliminar los delay modificar el pin de salida a uno cualquiera de los que estan disponibles i medir con el osciloscopio cual es la màxima frecuencia de apagado encendido que permite el microcontrolador. Medir la frecuencia en estos cuatro casos: 
   - Con el envio por el puerto série del mensaje i utilizando las funciones de Arduino
   - Con el envio por el puerto série y accedirendo directamente a los registros
   - Sin el envio por el puerto série del mensaje i utilizando las funciones de Arduino
   - Sin el envio por el puerto série y accedirendo directamente a los registros
5. Generar un informe fichero  informe.MD ( markdown ) donde se muestre el codigo, un diagrama de flujo y un diagrama de tiempos 
6. Responder a la siguiente pregunta en el programa que se ha realizado cual es el tiempo libre que tiene el procesador ?

#include <Arduino.h>

#define LED_BUILTIN 2  // Pin del LED (cambiar según la placa)
#define DELAY 1000  // Tiempo de espera en milisegundos

void setup() {
    pinMode(LED_BUILTIN, OUTPUT);  // Configurar el pin como salida
    Serial.begin(115200);  // Iniciar comunicación serie a 115200 baudios
    delay(1000);  // Pequeña pausa para que el monitor serie se estabilice
}

void loop() {
    digitalWrite(LED_BUILTIN, HIGH);  // Encender LED
    Serial.println("ON");  // Enviar mensaje al puerto serie
    delay(DELAY);  // Esperar 1 segundo

    digitalWrite(LED_BUILTIN, LOW);  // Apagar LED
    Serial.println("OFF");  // Enviar mensaje al puerto serie
    delay(DELAY);  // Esperar 1 segundo
}

# Ejercicio 2

#include <Arduino.h>

struct Button {
  const uint8_t PIN;
  uint32_t numberKeyPresses;
  bool pressed;
  unsigned long lastPressTime;  // Variable para almacenar el tiempo de la última pulsación
  unsigned long debounceDelay;  // Variable para definir el tiempo de rebote (debounce)
};

Button button1 = {18, 0, false, 0, 200};  // Tiempo de debounce a 200 ms

void IRAM_ATTR isr() {
  // Si el tiempo desde la última pulsación es mayor que el tiempo de rebote
  if (millis() - button1.lastPressTime > button1.debounceDelay) {
    button1.numberKeyPresses += 1;
    button1.pressed = true;
    button1.lastPressTime = millis();  // Actualiza el tiempo de la última pulsación
  }
}

void setup() {
  Serial.begin(9600);
  pinMode(button1.PIN, INPUT_PULLUP);
  attachInterrupt(button1.PIN, isr, FALLING);  // Interrupción en flanco de bajada
}

void loop() {
  if (button1.pressed) {
    Serial.printf("Button 1 has been pressed %u times\n", button1.numberKeyPresses);
    button1.pressed = false;  // Resetear estado de presionado
  }

  // Desconectar la interrupción después de 1 minuto
  static uint32_t lastMillis = 0;
  if (millis() - lastMillis > 60000) {
    lastMillis = millis();
    detachInterrupt(button1.PIN);  // Desconectar la interrupción
    Serial.println("Interrupt Detached!");
  }
}

# EJERCICIOS voluntarios  DE MEJORA DE NOTA

Elejir entre cualquiera de los siguentes:

* leer el valor de un convertidor  A/D  de entrada ; sacarlo por el puerto serie  y sacar el mismo valor  por otro pin  D/A

  - https://randomnerdtutorials.com/esp32-adc-analog-read-arduino-ide/

* Leer el valor del sensor de temperatura interno y sacarlo por el puerto serie 

  - https://gist.github.com/xxlukas42/7e7e18604f61529b8398f7fcc5785251

El resultado se ha de subir al github de cada uno y realizar un informe .MD 
