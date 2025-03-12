## 1. Introducción a la Programación de Microcontroladores en C++

### ¿Por qué usar C++ en microcontroladores?
C++ ofrece abstracción, modularidad y reutilización de código sin comprometer el 
rendimiento si se usa correctamente. Permite programación orientada a objetos y 
metaprogramación sin penalizar el uso de memoria si se evita la asignación 
dinámica. Su uso es útil en sistemas embebidos donde se necesita escalabilidad, 
encapsulación y código más mantenible.

### Diferencias clave entre C y C++ en sistemas embebidos  
- **Encapsulación:** C++ permite organizar el código en clases, facilitando la 
  reutilización y el mantenimiento.  
- **Templates y constexpr:** Permiten generar código más eficiente en tiempo de 
  compilación.  
- **Gestión de memoria:** C++ permite `new` y `delete`, pero en sistemas embebidos 
  generalmente se evitan debido a la falta de un heap dinámico confiable.  
- **Uso de funciones inline y constexpr:** Ayudan a optimizar el rendimiento sin 
  comprometer la seguridad del código.  
- **Sobrecarga de funciones y operadores:** Permite código más expresivo sin afectar 
  el tamaño del binario.  

### Cuándo es útil usar C++ en microcontroladores
- Cuando se requiere modularidad y escalabilidad en firmware embebido.  
- En proyectos donde la reutilización de código es clave, como controladores de 
  hardware genéricos.  
- Para reducir errores con el uso de abstracciones seguras sin afectar el 
  rendimiento.  
- En sistemas con un RTOS, donde se pueden aprovechar clases para manejar tareas 
  y sincronización.  

---  

## 2. Microcontroladores Compatibles con C++

### Microcontroladores que soportan C++ de forma nativa  
La mayoría de los microcontroladores modernos pueden compilar código en C++ si 
el compilador lo permite. Sin embargo, algunos tienen mejor soporte que otros. 
Ejemplos de familias con buen soporte para C++ incluyen:
- **ARM Cortex-M (M0, M3, M4, M7, M23, M33)** – Soportados por compiladores como 
  GCC (arm-none-eabi), Keil y IAR.  
- **ESP32 y ESP8266** – Compatible con C++ gracias al entorno ESP-IDF y Arduino Core.  
- **STM32 (STMicroelectronics)** – Puede usar C++ con STM32CubeIDE, GCC y Keil.  
- **Renesas RX y RL78** – Soportados por compiladores específicos como CC-RX.  
- **NXP i.MX RT y Kinetis** – Usan C++ con toolchains basados en GCC y MCUXpresso.  

### Comparación con otros lenguajes usados en sistemas embebidos
- **C:** Más común en microcontroladores debido a su control directo del hardware 
  y menor overhead.  
- **Assembly:** Se usa en partes críticas de rendimiento, pero es poco escalable.  
- **Python/Micropython:** Útil para prototipos en sistemas con memoria y CPU 
  suficientes, pero no eficiente en bajo nivel.  
- **Rust:** Ofrece seguridad de memoria, pero tiene menor adopción en embebidos que 
  C/C++.

C++ permite aprovechar la abstracción sin perder control del hardware, lo que lo 
hace una opción equilibrada en comparación con otros lenguajes.

### Entornos de desarrollo y toolchains compatibles (GCC, Keil, IAR, etc.)  
- **GCC (arm-none-eabi-g++)** – Open-source, usado en ARM Cortex-M y otros MCU.  
- **Keil MDK-ARM** – Propietario, con optimizaciones avanzadas para ARM.  
- **IAR Embedded Workbench** – Muy optimizado para bajo consumo en sistemas embebidos.  
- **Clang/LLVM** – Menos común en embebidos, pero en crecimiento.  
- **PlatformIO** – IDE multiplataforma compatible con múltiples toolchains.  
- **Arduino IDE / ESP-IDF** – Usados para desarrollo rápido con C++.

A nivel profesional, los **IDEs más utilizados para desarrollo en C++ con microcontroladores** 
dependen de la plataforma y el fabricante del MCU. Los más destacados son:

#### 1. Keil MDK-ARM
- **Fabricante:** Arm (anteriormente Keil).  
- **Uso:** Predominante en la industria para microcontroladores ARM Cortex-M.  
- **Características:** Optimización avanzada, depuración eficiente, y RTOS integrado 
  (Keil RTX).  
- **Lenguaje:** Soporta C y C++.  
- **Desventaja:** Licencia propietaria y costosa.  

#### 2. IAR Embedded Workbench
- **Fabricante:** IAR Systems.  
- **Uso:** Empresas enfocadas en dispositivos de bajo consumo y certificaciones de 
  seguridad.  
- **Características:** Excelente optimización de código, análisis estático y 
  herramientas de seguridad.  
- **Lenguaje:** Soporta C y C++.  
- **Desventaja:** Licencia propietaria costosa.  

#### 3. STM32CubeIDE
- **Fabricante:** STMicroelectronics.  
- **Uso:** Desarrollos con STM32, ampliamente usado en la industria.  
- **Características:** Basado en Eclipse con integración nativa de configuradores 
- de hardware.  
- **Lenguaje:** Soporta C y C++.  
- **Desventaja:** Puede ser más pesado que otros IDEs.  

#### 4. MPLAB X IDE
- **Fabricante:** Microchip.  
- **Uso:** Desarrollo con microcontroladores PIC y AVR.  
- **Características:** Integración con compiladores XC y configuradores de hardware.  
- **Lenguaje:** Soporta C y C++.  
- **Desventaja:** No es ideal para microcontroladores ARM.  

#### 5. Visual Studio Code + PlatformIO
- **Fabricante:** Microsoft (VS Code) + PlatformIO.  
- **Uso:** Desarrollo en diversas plataformas, incluyendo ESP32, STM32, y ARM.  
- **Características:** Flexibilidad, compatibilidad con múltiples toolchains y 
  depuración avanzada.  
- **Lenguaje:** Soporta C y C++.  
- **Desventaja:** Mayor configuración inicial requerida.  

---

#### ¿Cuál es el más usado en la industria?  
- **Para ARM Cortex-M:** **Keil MDK-ARM e IAR Embedded Workbench** son los más comunes 
  en entornos industriales y productos comerciales.  
- **Para STM32:** **STM32CubeIDE** es el estándar cuando se trabaja con STM.  
- **Para PIC y AVR:** **MPLAB X IDE** es la mejor opción.  
- **Para desarrollos flexibles y open-source:** **VS Code + PlatformIO** se está 
  volviendo popular, especialmente en startups y makers avanzados.  

Si buscas una opción profesional con soporte y optimización avanzada, **Keil o IAR** 
son las elecciones estándar en la industria. Para opciones gratuitas pero profesionales, 
**STM32CubeIDE o VS Code con PlatformIO** son excelentes alternativas.

---

## 3. Revisión de C++ para Microcontroladores

### Características de C++ útiles en sistemas embebidos
C++ ofrece varias características que pueden ser ventajosas en sistemas 
embebidos, siempre que se usen correctamente:
- **`constexpr` y `inline`**: Permiten ejecutar cálculos en tiempo de compilación, 
  reduciendo uso de RAM y FLASH.  
- **Clases y estructuras (`struct`)**: Facilitan la organización del código sin 
  penalizar el rendimiento si se usan adecuadamente.  
- **Templates (`template`)**: Permiten reutilizar código sin impacto en tiempo de 
  ejecución.  
- **Espacios de nombres (`namespace`)**: Evitan colisiones de nombres en proyectos 
  grandes.  
- **Sobrecarga de operadores**: Puede hacer que el código sea más legible, aunque 
  debe usarse con precaución.

### Uso de clases y estructuras eficientes  
- **Preferir `struct` para datos simples**: En C++, las `struct` y `class` son 
  equivalentes excepto por la visibilidad (`struct` tiene miembros públicos por 
  defecto). Para representar periféricos o registros, `struct` es más eficiente.
- **Evitar herencia y virtualización excesiva**: En sistemas embebidos, el uso de 
  clases virtuales (`virtual`) puede generar *vtable* y aumentar el consumo de 
  memoria.  
- **Uso de `static` en métodos y atributos**: Reduce el uso de RAM y evita accesos 
  innecesarios en tiempo de ejecución.  

### Gestión de memoria y restricciones en microcontroladores  
- **Evitar uso de heap (`new` y `delete`)**: Los microcontroladores tienen memoria 
  limitada y no siempre cuentan con un administrador de heap eficiente.
- **Usar variables `static` en lugar de variables globales**: Ayuda a encapsular 
  datos sin exponerlos innecesariamente.  
- **Optimizar estructuras de datos**: Reducir el tamaño de las estructuras con 
  alineación (`__attribute__((packed))` en GCC) cuando sea necesario.

### Evitar funciones dinámicas (`new`, `delete`)  
- En la mayoría de los sistemas embebidos, la fragmentación de memoria puede ser 
  un problema crítico.  
- En lugar de `new` y `delete`, se recomienda el uso de **buffers estáticos** o 
  **pools de memoria preasignados**.  
- Si se necesita asignación dinámica, considerar **memoria de pila (`stack`) en 
  lugar de heap**.  

### Excepciones y su impacto en sistemas embebidos  
- **Excepciones (`try-catch`) generalmente están deshabilitadas** en compiladores 
  para embebidos debido a la sobrecarga de código.  
- **Alternativa:** Usar códigos de error y estructuras de control en lugar de 
  excepciones.  
- **Compiladores como `arm-none-eabi-g++` permiten desactivar excepciones (`-fno-exceptions`)** 
  para reducir el tamaño del binario.  

---

## 4. Buenas Prácticas y Directrices Profesionales

### Uso de `constexpr` y `inline` para optimización  

#### ¿Qué hace `constexpr` y en qué se diferencia de una constante?  
`constexpr` garantiza que el valor se calcule en **tiempo de compilación**, 
mientras que `const` solo indica que el valor 
**no puede cambiar en tiempo de ejecución**, pero no obliga a que se evalúe en 
compilación.

#### Ejemplo de `constexpr` vs `const`
```cpp
const int x = 5 + rand();  // ERROR: No se puede evaluar en compilación
constexpr int y = 5 * 2;   // Se calcula en tiempo de compilación
```
- `x` no puede ser `constexpr` porque usa `rand()`, que se evalúa en ejecución.  
- `y` sí puede ser `constexpr` porque `5 * 2` es una operación resolvible en 
  compilación.  

**Ventaja**: Reduce consumo de RAM porque las constantes `constexpr` 
**no ocupan espacio en memoria RAM**, ya que el compilador las sustituye directamente 
en el código máquina.

---

### ¿Qué hace `inline` y cuándo usarlo?  
`inline` **sugiere** al compilador que inserte el código de la función en el lugar 
donde se llama, evitando el overhead de una llamada a función.  

#### Ejemplo de `inline` vs función normal
```cpp
inline void toggleLED() { GPIOA->ODR ^= (1 << 5); }  // Puede ser insertada en el código
void normalToggle() { GPIOA->ODR ^= (1 << 5); }      // Llamada a función normal
```
**Ventaja**: Reduce la latencia de funciones muy pequeñas al evitar la sobrecarga 
de llamada de función.  

**No usar `inline` en funciones grandes**, porque aumentará el tamaño del binario.  

---

### Definición de HAL (Hardware Abstraction Layer)  
HAL (Hardware Abstraction Layer) es una capa de software que proporciona 
**abstracción del hardware**, permitiendo programar periféricos sin escribir 
directamente en los registros.

#### Ejemplo de uso de HAL en STM32  
```cpp
HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_SET);  // Activa LED
```
Equivalente a escribir directamente en los registros:  
```cpp
GPIOA->BSRR = (1 << 5);  // Activa LED sin usar HAL
```
**Ventaja**: Facilita la portabilidad del código entre diferentes microcontroladores.  
**Desventaja**: Introduce overhead en comparación con acceso directo a registros.  

---

### Manejo eficiente de interrupciones en C++

#### Explicación del ejemplo de interrupción
```cpp
volatile bool flag = false;  // Indicador de interrupción

extern "C" void EXTI0_IRQHandler() {  // Rutina de interrupción
    flag = true;  // Se activa la bandera para indicar que ocurrió la interrupción
}
```
- **`volatile bool flag = false;`** : Evita que el compilador optimice esta variable, porque 
  puede cambiar en una interrupción.  
- **`extern "C"`** : Indica que la función debe usarse con **nomenclatura de C**, para 
  que el linker la reconozca correctamente (útil cuando la interrupción está 
  definida en un archivo de ensamblador o C).  
- **`EXTI0_IRQHandler()`** : Es la rutina de interrupción asociada al 
  **pin 0 del puerto externo (EXTI0)**.  

**Buena práctica**: No hacer cálculos dentro de la interrupción, solo marcar una 
bandera y procesarla en el `main()`.  

---

### Uso de `volatile` y `restrict` en programación de registros  

#### ¿Por qué usar `volatile`?
`volatile` indica al compilador que **el valor de la variable puede cambiar en cualquier momento**, 
evitando que la optimización del compilador lo elimine o lo almacene en un 
registro.

#### Ejemplo correcto (`volatile` usado bien)
```cpp
volatile uint32_t *gpio = (volatile uint32_t *)0x48000014;  // Dirección del registro GPIO
*gpio = 0x01;  // Escritura en el puerto GPIO
```
**El compilador NO optimiza este acceso**, porque la memoria puede cambiar por 
hardware o interrupciones.  

#### Ejemplo incorrecto (sin `volatile`)
```cpp
uint32_t *gpio = (uint32_t *)0x48000014;
*gpio = 0x01;  // El compilador puede optimizar y eliminar esta línea
```
**Problema**: El compilador puede pensar que `gpio` nunca cambia y optimizarlo, 
eliminando la escritura al registro.

---

### Explicación paso a paso del ejemplo de encapsulación con clases  

```cpp
class LED {
public:
    constexpr LED(uint32_t pin) : pinMask(1 << pin) {}  // Constructor calcula la máscara
    void on() { GPIOA->BSRR = pinMask; }  // Enciende LED
    void off() { GPIOA->BSRR = pinMask << 16; }  // Apaga LED
private:
    const uint32_t pinMask;  // Máscara de bits para el pin
};
LED led(5);  // Se crea un LED en el pin 5
```

#### Explicación línea por línea  
- **`class LED {}`** :_ Se define una clase llamada `LED` para manejar un LED.  
- **`constexpr LED(uint32_t pin) : pinMask(1 << pin) {}`**  
  - `constexpr` : Calcula la máscara en **compilación** en lugar de en ejecución.  
  - `pinMask(1 << pin)` : Convierte el número de pin en un **bitmask** para controlarlo 
  con registros.  
- **`void on() { GPIOA->BSRR = pinMask; }`**  
  - Escribe en `BSRR` el bit correspondiente para encender el LED.  
- **`void off() { GPIOA->BSRR = pinMask << 16; }`**  
  - Escribe en `BSRR` el bit desplazado para apagar el LED.  
- **`private: const uint32_t pinMask;`**  
  - `const` : Hace que la variable **no cambie después de ser creada**.  

**Ventaja**: Permite manejar cualquier LED solo con `LED led(5);` sin usar macros 
ni valores mágicos.  

---


## 5. Herramientas y Configuración del Entorno de Desarrollo  

En esta sección, cubriremos las herramientas más utilizadas para desarrollar en 
C++ en microcontroladores, así como la configuración recomendada para un entorno 
eficiente.  

### Compiladores y Toolchains recomendados
Un **toolchain** es un conjunto de herramientas necesarias para compilar y 
programar el microcontrolador. Incluye **compilador, linker y depurador**.  

**Compiladores más utilizados en C++ para microcontroladores**  
| **Compilador**       | **Soporte**            | **Ventajas** |
|----------------------|----------------------|-------------|
| **GCC (arm-none-eabi-g++)** | ARM Cortex-M, STM32, ESP32, RISC-V | Open-source, optimización avanzada |
| **Keil MDK-ARM** | ARM Cortex-M | Compilador optimizado (ARMCC), soporte comercial |
| **IAR Embedded Workbench** | ARM, MSP430, AVR | Optimización extrema, análisis estático avanzado |
| **Clang/LLVM** | ARM Cortex-M, RISC-V | Optimización agresiva, integración con CMake |
| **MPLAB XC32** | PIC32, dsPIC, AVR | Soporte nativo para Microchip |

- **GCC es el más común en entornos de código abierto y proyectos industriales**.  
- **Keil e IAR son usados en la industria cuando se necesita optimización avanzada y soporte comercial**.  

---

### Configuración de CMake y Makefiles para proyectos embebidos
**¿Por qué usar CMake o Makefiles?**  
- Facilitan la **compilación automatizada** en proyectos grandes.  
- Permiten **manejar dependencias** y organizar múltiples archivos.  
- Compatible con **GCC, Clang, IAR y Keil**.  

**Ejemplo de un Makefile para un proyecto en ARM Cortex-M con GCC**
```make
CC = arm-none-eabi-g++
CFLAGS = -mcpu=cortex-m4 -mthumb -O2 -Wall
LDFLAGS = -T linker_script.ld
SRC = main.cpp drivers.cpp
OBJ = $(SRC:.cpp=.o)
OUT = firmware.elf

all: $(OUT)

$(OUT): $(OBJ)
	$(CC) $(CFLAGS) $(LDFLAGS) -o $@ $^

%.o: %.cpp
	$(CC) $(CFLAGS) -c $< -o $@

clean:
	rm -f $(OBJ) $(OUT)
```
- Define el **compilador, flags de optimización, archivos fuente y el proceso de compilación**.  


**Ejemplo de `CMakeLists.txt` para un proyecto embebido**
```cmake
cmake_minimum_required(VERSION 3.10)
project(Firmware LANGUAGES CXX)

add_executable(firmware main.cpp drivers.cpp)
target_compile_options(firmware PRIVATE -mcpu=cortex-m4 -mthumb -O2)
```
- **CMake simplifica la configuración y es compatible con múltiples toolchains.**  

---

### Debugging en C++: herramientas y estrategias
**Depuradores más utilizados en microcontroladores**
| **Herramienta**        | **Uso**               | **Características** |
|------------------------|----------------------|---------------------|
| **GDB (GNU Debugger)** | ARM, RISC-V, ESP32 | Debugger open-source compatible con GCC |
| **OpenOCD** | ARM Cortex-M, STM32 | Depuración con JTAG/SWD, compatible con GDB |
| **Keil uVision Debugger** | ARM Cortex-M | Depurador integrado con simulación avanzada |
| **IAR C-SPY** | ARM, AVR | Depuración con análisis de stack y memoria |
| **J-Link Debugger (Segger)** | ARM, RISC-V | Depuración rápida con RTT (Real-Time Transfer) |

**Ejemplo: Depurar un microcontrolador con GDB y OpenOCD**
```bash
openocd -f interface/stlink-v2.cfg -f target/stm32f4x.cfg
```
- Esto inicia OpenOCD para conectar con un STM32 usando ST-Link.  

**Conectar GDB**
```bash
arm-none-eabi-gdb firmware.elf
target remote localhost:3333
monitor reset halt
load
continue
```
- Permite **cargar y ejecutar el firmware con depuración en tiempo real**.  

---

### Simuladores y entornos de prueba
Para probar código sin un microcontrolador real, se pueden usar **simuladores**.

**Simuladores más usados**
| **Simulador**       | **Soporte**       | **Características** |
|----------------------|------------------|---------------------|
| **QEMU**            | ARM, RISC-V       | Emulación completa de microcontroladores |
| **Renode**          | ARM, RISC-V       | Simulación de sistemas embebidos complejos |
| **Keil Simulator**  | ARM Cortex-M      | Simulación de hardware en Keil MDK |
| **Proteus**         | AVR, PIC, ARM     | Simulación gráfica con circuitos |
| **TINA TI**         | MSP430, Tiva C    | Simulación de sistemas TI |

**Ejemplo: Simular un STM32 en QEMU**
```bash
qemu-system-arm -M stm32f4-discovery -kernel firmware.elf
```
- Permite probar el código **sin necesidad de hardware real**.  

---

### Resumen
- **GCC (arm-none-eabi-g++) es el compilador más usado**, pero Keil e IAR ofrecen optimización avanzada.  
- **CMake y Makefiles ayudan a gestionar proyectos embebidos** de forma más organizada.  
- **GDB y OpenOCD son herramientas clave para debugging**, aunque Keil y IAR ofrecen entornos más avanzados.  
- **QEMU, Renode y Proteus permiten simular hardware sin necesidad de un microcontrolador real**.  

---


## 6. Ejemplo Práctico: "Hola Mundo" en C++ en un Microcontrolador

En este apartado, implementaremos el clásico **"Hola Mundo" de sistemas embebidos**, 
que consiste en **encender y apagar un LED** en un microcontrolador usando C++.  

Veremos **tres versiones** del código:  
- **Usando HAL** (código más portable, pero con overhead).  
- **Acceso directo a registros** (más eficiente, pero menos portable).  
- **Comparación con C puro** para notar la diferencia.

---

### Configuración del entorno
Antes de escribir código, necesitamos:  
- **Un microcontrolador** (Ejemplo: STM32, ESP32, AVR, etc.).  
- **Un entorno de desarrollo** (VS Code + PlatformIO, STM32CubeIDE, Keil, IAR, etc.).  
- **Compilador adecuado** (GCC, Keil, IAR).  
- **Depurador o placa para pruebas** (ST-Link, J-Link, UART para depuración).

---

### Código mínimo usando HAL (abstracción de hardware)
Este método usa la capa de abstracción de hardware (HAL) del fabricante, lo que 
hace que el código **sea portable entre microcontroladores de la misma familia**.

**Ejemplo en STM32 con HAL (STM32Cube)**
```cpp
#include "stm32f4xx.h"  // Librerías HAL

void setup() {
    HAL_Init();  // Inicializar HAL
    __HAL_RCC_GPIOA_CLK_ENABLE();  // Habilitar reloj de GPIOA
    
    GPIO_InitTypeDef GPIO_InitStruct = {0};
    GPIO_InitStruct.Pin = GPIO_PIN_5;  // Configurar pin 5
    GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
    GPIO_InitStruct.Pull = GPIO_NOPULL;
    GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;
    HAL_GPIO_Init(GPIOA, &GPIO_InitStruct);
}

void loop() {
    HAL_GPIO_TogglePin(GPIOA, GPIO_PIN_5);  // Cambiar estado del LED
    HAL_Delay(500);  // Esperar 500ms
}

int main() {
    setup();
    while (1) {
        loop();
    }
}
```
- Usa **HAL_GPIO_TogglePin()** para alternar el LED.  
- Es más **portable**, pero introduce **overhead**.  

---

### Código usando acceso directo a registros (sin HAL)
Este método **es más eficiente**, ya que accede directamente a los registros del 
microcontrolador.

**Ejemplo en STM32 con acceso a registros**
```cpp
#include "stm32f4xx.h"

void setup() {
    RCC->AHB1ENR |= (1 << 0);  // Habilitar reloj GPIOA
    GPIOA->MODER |= (1 << (5 * 2));  // Configurar PA5 como salida
}

void loop() {
    GPIOA->ODR ^= (1 << 5);  // Alternar LED en PA5
    for (volatile int i = 0; i < 1000000; i++);  // Retardo aproximado
}

int main() {
    setup();
    while (1) {
        loop();
    }
}
```
- **Más rápido y eficiente** que HAL.  
- **No es portable**: está escrito específicamente para STM32.  
- **El retardo usa un bucle vacío**, menos preciso que `HAL_Delay()`.

---

### Comparación con C puro
Si hiciéramos lo mismo en **C puro**, la estructura sería similar:
```c
#include "stm32f4xx.h"

void setup() {
    RCC->AHB1ENR |= (1 << 0);  // Habilitar reloj GPIOA
    GPIOA->MODER |= (1 << (5 * 2));  // Configurar PA5 como salida
}

void loop() {
    GPIOA->ODR ^= (1 << 5);  // Alternar LED
    for (volatile int i = 0; i < 1000000; i++);  // Retardo aproximado
}

int main() {
    setup();
    while (1) {
        loop();
    }
}
```
- **C y C++ son casi idénticos aquí**, pero en C++ podríamos usar clases y objetos 
  para una mejor estructura.  

---

### Diferencias clave entre C++ y C en este caso
| **Aspecto** | **C++ con HAL** | **C++ con registros** | **C puro** |
|------------|----------------|----------------|-------------|
| **Portabilidad** | Alta (puede usarse en otros STM32) | Baja (específico para STM32) | Baja (depende del MCU) |
| **Overhead** | Alto (debido a HAL) | Bajo | Bajo |
| **Facilidad de uso** | Alta | Media | Media |
| **Flexibilidad** | Alta (se pueden usar clases) | Media | Media |
| **Eficiencia** | Media | Alta | Alta |

**Conclusión:**  
- **HAL es más portable, pero introduce overhead**.  
- **Acceso directo a registros es más rápido, pero menos portable**.  
- **C++ permite encapsular en clases, pero en este caso no aporta mucho sobre C**.  

---

### ¿Cómo podríamos mejorar el código en C++?
Podríamos **encapsular el control del LED en una clase** para facilitar su uso:

```cpp
class LED {
public:
    LED(GPIO_TypeDef *port, uint32_t pin) : port(port), pin(pin) {
        port->MODER |= (1 << (pin * 2));  // Configurar pin como salida
    }

    void toggle() {
        port->ODR ^= (1 << pin);
    }

private:
    GPIO_TypeDef *port;
    uint32_t pin;
};

int main() {
    LED led(GPIOA, 5);  // Crear objeto LED en PA5
    while (1) {
        led.toggle();
        for (volatile int i = 0; i < 1000000; i++);
    }
}
```
**Ventajas de este enfoque:**  
- Hace el código **más modular y reutilizable**.  
- Si cambiamos de microcontrolador, solo editamos la clase.  
- **Facilita el mantenimiento** en proyectos grandes.  

---

### Resumen
- Implementamos el clásico **"Hola Mundo" en microcontroladores** encendiendo un LED.  
- **Tres versiones:** HAL, acceso directo a registros y C puro.  
- **HAL es más portable, pero más lento.**  
- **Acceso directo es más rápido, pero menos portable.**  
- **C++ permite mejorar la estructura con clases**, aunque no siempre es necesario.  

---


## 7. Desarrollo de un Proyecto Funcional en C++

En este apartado, cubriremos cómo estructurar un proyecto en C++ para 
microcontroladores de manera profesional, incluyendo modularización, 
encapsulación de hardware y multitarea con RTOS.

### Diseño modular de firmware
Un buen firmware debe estar **organizado en capas**, evitando código monolítico.  

**Estructura recomendada para un proyecto en C++**
```
/Proyecto
│── /src
│   │── main.cpp           # Punto de entrada
│   │── system.cpp         # Inicialización del sistema
│   │── led.cpp            # Implementación de LED
│   │── uart.cpp           # Comunicación UART
│   │── sensor.cpp         # Lógica de sensor
│── /inc
│   │── system.h
│   │── led.h
│   │── uart.h
│   │── sensor.h
│── /lib                   # Librerías externas
│── /build                 # Archivos compilados
│── Makefile               # Makefile o CMakeLists.txt
```
- **Cada componente (LED, UART, Sensor) tiene su propio archivo** (`.cpp` y `.h`).  
- **El código en `main.cpp` solo maneja la lógica general, sin detalles de hardware**.  
- **Usar `lib/` para dependencias externas facilita la gestión del proyecto**.  

---

### Implementación de clases para controladores de hardware (drivers)
En lugar de manejar hardware con código suelto, encapsulamos en clases para reutilización.  

**Ejemplo: Clase para controlar un LED**
```cpp
#ifndef LED_H
#define LED_H

#include "stm32f4xx.h"

class LED {
public:
    LED(GPIO_TypeDef *port, uint32_t pin);
    void on();
    void off();
    void toggle();

private:
    GPIO_TypeDef *port;
    uint32_t pin;
};

#endif
```

**Implementación en `led.cpp`**
```cpp
#include "led.h"

LED::LED(GPIO_TypeDef *port, uint32_t pin) : port(port), pin(pin) {
    port->MODER |= (1 << (pin * 2));  // Configurar pin como salida
}

void LED::on() {
    port->BSRR = (1 << pin);
}

void LED::off() {
    port->BSRR = (1 << (pin + 16));
}

void LED::toggle() {
    port->ODR ^= (1 << pin);
}
```

**Uso en `main.cpp`**
```cpp
#include "led.h"

int main() {
    LED led(GPIOA, 5);
    
    while (1) {
        led.toggle();
        for (volatile int i = 0; i < 1000000; i++);  // Retardo
    }
}
```
- Código **modular** y fácil de extender.  
- Permite manejar **múltiples LEDs** sin duplicar código.  

---

### Uso de `templates` para mejorar reutilización de código
En microcontroladores, los `templates` permiten escribir código más **genérico** sin 
perder rendimiento.  

**Ejemplo: Template para un GPIO genérico**
```cpp
template <GPIO_TypeDef* PORT, uint32_t PIN>
class GPIO {
public:
    static void init() {
        PORT->MODER |= (1 << (PIN * 2));
    }

    static void set() {
        PORT->BSRR = (1 << PIN);
    }

    static void reset() {
        PORT->BSRR = (1 << (PIN + 16));
    }
};
```

**Uso**
```cpp
using LED1 = GPIO<GPIOA, 5>;  // Definir LED en PA5

int main() {
    LED1::init();
    while (1) {
        LED1::set();
        for (volatile int i = 0; i < 1000000; i++);
        LED1::reset();
        for (volatile int i = 0; i < 1000000; i++);
    }
}
```
- **Código ultra eficiente** sin penalización de rendimiento.  
- **Sin uso de RAM** porque todo se resuelve en compilación.  

---

### Gestión de tareas con RTOS en C++
Para manejar múltiples tareas (ejemplo: lectura de sensores, comunicación, control), 
usamos un **RTOS**.  

📌 **Ejemplo con FreeRTOS en C++**
```cpp
#include "FreeRTOS.h"
#include "task.h"
#include "led.h"

void blinkTask(void *pvParameters) {
    LED *led = (LED*) pvParameters;
    while (1) {
        led->toggle();
        vTaskDelay(pdMS_TO_TICKS(500));  // Espera 500ms
    }
}

int main() {
    LED led(GPIOA, 5);
    
    xTaskCreate(blinkTask, "Blink", 128, &led, 1, NULL);
    vTaskStartScheduler();  // Iniciar RTOS

    while (1);  // No debería llegar aquí
}
```
- **RTOS permite multitarea eficiente** en microcontroladores.  
- **Tareas encapsuladas en funciones, evitando bloqueos innecesarios**.  

---

### Resumen
- **Organizar el código en módulos** mejora la mantenibilidad.  
- **Encapsular hardware en clases** permite reutilización y escalabilidad.  
- **`templates` ayudan a generar código más eficiente** sin pérdida de rendimiento.  
- **RTOS facilita la multitarea** en sistemas embebidos avanzados.  

---


## **8. Optimización de Código en C++ para Microcontroladores**  

En este apartado, exploraremos las estrategias clave para 
**optimizar el código en sistemas embebidos**, reduciendo el consumo de memoria y 
mejorando el rendimiento sin comprometer la funcionalidad.

### Técnicas de optimización para bajo consumo
En microcontroladores, es esencial minimizar el uso de 
**RAM, Flash y consumo de energía**.

#### Uso de `constexpr` para cálculos en compilación
- Evita cálculos innecesarios en tiempo de ejecución.  
- Reduce el uso de RAM y ciclos de CPU.  

**Ejemplo: Sin `constexpr` (menos eficiente)**
```cpp
int cuadrado(int x) {
    return x * x;  // Se calcula en cada ejecución
}

int area = cuadrado(4);  // Se ejecuta en tiempo de ejecución
```

**Ejemplo: Con `constexpr` (optimizado)**
```cpp
constexpr int cuadrado(int x) {
    return x * x;  // Se evalúa en compilación
}

constexpr int area = cuadrado(4);  // Se calcula en tiempo de compilación
```
- **El compilador reemplaza directamente `area = 16`, sin cálculos en ejecución**.  

---

#### Uso de `volatile` para evitar optimizaciones peligrosas
**Ejemplo de optimización incorrecta**  
```cpp
bool flag = false;

while (!flag) {
    // Código aquí (pero `flag` nunca cambia)
}
```
- **El compilador puede eliminar el bucle** porque no ve cambios en `flag`.  

**Solución con `volatile`**
```cpp
volatile bool flag = false;
```
- Evita que el compilador optimice la variable y **se asegura de leer su valor real**.  

---

#### Apagar periféricos no utilizados
- Apagar módulos no esenciales **reduce consumo de energía**.  
**Ejemplo: Apagar periféricos en STM32**
```cpp
RCC->AHB1ENR &= ~(1 << 1);  // Apagar GPIOB
RCC->APB1ENR &= ~(1 << 2);  // Apagar USART2
```
- **Reduce consumo en modo bajo consumo**.  

---

### Uso de `constexpr` y Template Metaprogramming
C++ permite **calcular valores en tiempo de compilación** en lugar de ejecución.  

**Ejemplo: Factorial en tiempo de compilación**
```cpp
template <int N>
struct Factorial {
    static constexpr int value = N * Factorial<N - 1>::value;
};

template <>
struct Factorial<0> {
    static constexpr int value = 1;
};

// Se calcula en compilación
constexpr int resultado = Factorial<5>::value;  // 120
```
- No consume ciclos de CPU en ejecución, **ahorrando energía y tiempo de cómputo**.  

---

### Estrategias para minimizar el uso de RAM y FLASH
#### Usar `const` en lugar de variables en RAM
**Ejemplo ineficiente (usa RAM innecesariamente)**
```cpp
char mensaje[] = "Hola Mundo";  // Se almacena en RAM
```
**Optimizado con `const` (se almacena en Flash)**
```cpp
const char mensaje[] = "Hola Mundo";
```
- **Ahorra RAM porque el string se almacena en la memoria de programa (Flash)**.  

---

#### Evitar `malloc` y `new`
- La **memoria dinámica causa fragmentación** y puede agotar la RAM en 
  microcontroladores.  

**Ejemplo ineficiente**
```cpp
int *buffer = new int[100];  // Reserva memoria en heap
delete[] buffer;  // Se debe liberar manualmente
```
**Alternativa con memoria estática (mejor en embebidos)**
```cpp
static int buffer[100];  // Reserva memoria en stack/global, sin fragmentación
```
- **Es más seguro y no necesita `delete`**.  

---

#### Evitar el uso innecesario de `float`
- **Las operaciones en `float` son más lentas y consumen más energía** en 
  microcontroladores sin FPU (Unidad de Punto Flotante).  

**Ejemplo ineficiente**
```cpp
float resultado = 5.0 / 3.0;  // Operación de punto flotante
```
**Alternativa optimizada**
```cpp
int resultado = 5 * 1000 / 3;  // Se trabaja con enteros escalados
float final = resultado / 1000.0;
```
- **Reduce el uso de operaciones en punto flotante**.  

---

### Resumen
- **Usar `constexpr` para cálculos en compilación** evita uso innecesario de CPU.  
- **Evitar `malloc` y `new` en microcontroladores** para prevenir fragmentación de 
  memoria.  
- **Usar `volatile` en variables modificadas por interrupciones** para evitar 
  optimizaciones incorrectas.  
- **Apagar periféricos innecesarios para reducir consumo de energía**.
- **Evitar `float` en micros sin FPU** y usar enteros escalados cuando sea 
  posible.

---


## 9. Ventajas y Desventajas del Uso de C++ en Microcontroladores  

En este apartado, analizaremos 
**cuándo es conveniente usar C++ en microcontroladores y cuándo no lo es**, 
comparándolo con C y otros lenguajes usados en sistemas embebidos.  

### Comparación con C puro
C++ es una **evolución de C** que añade **programación orientada a objetos (POO), plantillas, `constexpr` y otras optimizaciones**. Sin embargo, en sistemas embebidos, **no siempre es ideal**.  

| **Característica**  | **C++ en Microcontroladores** | **C puro en Microcontroladores** |
|--------------------|---------------------------|-------------------------------|
| **Eficiencia**     | Puede generar código más grande si no se usa correctamente. | Código más optimizado y sin sobrecarga. |
| **Facilidad de mantenimiento** | Más modular con clases y plantillas. | Puede volverse más difícil de escalar en proyectos grandes. |
| **Uso de memoria** | Puede ser mayor debido a VTables, `new`, `exception handling`. | Usa solo lo necesario, sin mecanismos adicionales. |
| **Optimización**   | `constexpr`, plantillas y `inline` pueden reducir uso de RAM y CPU. | Optimizaciones manuales con macros y funciones. |
| **Portabilidad**   | Más fácil de portar entre arquitecturas. | Más dependiente del hardware específico. |
| **Seguridad**      | Encapsulación y `RAII` mejoran seguridad. | Sin POO, menos mecanismos de control de acceso. |

**Conclusión**:  
- **C++ es útil en sistemas embebidos grandes o cuando se necesita código reutilizable**.  
- **C sigue siendo preferido cuando se busca eficiencia extrema y control total del hardware**.  

---

### Impacto en consumo de memoria y velocidad
#### Cuándo C++ es más eficiente que C  
- Cuando se usa **`constexpr` y templates**, eliminando cálculos en ejecución.  
- Cuando se **evita `new` y `delete`** para prevenir fragmentación de memoria.  
- Cuando se usa **POO para modularizar código sin introducir overhead innecesario**.  

**Ejemplo: Código optimizado en C++**  
```cpp
template<int PIN>
class LED {
public:
    static void toggle() { GPIOA->ODR ^= (1 << PIN); }
};
```
- **Sin `virtual`, sin overhead, código limpio y eficiente**.  

---

#### Cuándo C++ es menos eficiente que C
- Cuando se usa **`new` y `delete`**, generando fragmentación.  
- Cuando se usan **excepciones (`try-catch`)**, que pueden aumentar el binario.  
- Cuando se usan **clases con `virtual`**, generando VTables y aumentando el uso de RAM.  

**Ejemplo: Código ineficiente en C++**  
```cpp
class LED {
public:
    virtual void toggle() { GPIOA->ODR ^= (1 << 5); }
};
```
**Aquí `virtual` introduce una VTable (tabla de funciones virtuales), aumentando el uso de memoria**.  

---

### Casos de uso donde C++ es preferible
**Ejemplos donde C++ es la mejor opción en sistemas embebidos**:  
- **Proyectos grandes con múltiples periféricos y modularidad** (drones, sistemas 
  automotrices).  
- **Firmware de alto nivel con RTOS y comunicación compleja** (IoT, dispositivos 
  médicos).  
- **Dispositivos que requieren escalabilidad y reutilización de código**.  

**Ejemplo: Implementación de un driver modular en C++**  
```cpp
class Sensor {
public:
    virtual int leer() = 0;
};

class SensorI2C : public Sensor {
public:
    int leer() override { return 123; }  // Lectura simulada
};
```
- Código modular y reutilizable para varios sensores sin cambiar la lógica de 
  alto nivel.  

---

### Casos en los que se debe evitar C++
- **Proyectos extremadamente optimizados** donde cada byte de RAM importa.  
- **Aplicaciones críticas de tiempo real** donde no se pueden tolerar ciclos de CPU 
  extra.  
- **Dispositivos con compiladores limitados** (algunos microcontroladores pequeños 
  solo soportan C).  

**Ejemplo: Código más eficiente en C que en C++**  
```c
#define TOGGLE_LED() (GPIOA->ODR ^= (1 << 5))

int main() {
    while (1) {
        TOGGLE_LED();
    }
}
```
- **Esto es más eficiente que usar una clase en C++, sin ninguna sobrecarga extra**.  

---

### Resumen
- **C++ es útil en proyectos embebidos grandes, reutilizables y modulares**.  
- **C sigue siendo preferido en sistemas críticos donde cada ciclo de CPU cuenta**.  
- **Si se usa C++, es importante evitar `new`, `delete`, `virtual` y excepciones** 
  para no introducir overhead innecesario.  
- **La elección entre C y C++ debe basarse en el hardware y los requerimientos del sistema**.  

---

## **10. Prácticas Avanzadas y Futuro de C++ en Sistemas Embebidos**  

En este apartado exploraremos cómo **C++ está evolucionando en sistemas embebidos**, 
las tendencias actuales y el impacto de C++20 y futuras versiones en el 
desarrollo de firmware.

### Uso de C++ en Sistemas de Tiempo Real
Los sistemas embebidos de tiempo real requieren **ejecución determinística** y 
**mínima latencia**.  

**C++ en tiempo real vs C**  
| **Característica**     | **C++ en Tiempo Real**  | **C en Tiempo Real** |
|----------------------|----------------------|---------------------|
| **Sobrecarga de código** | Puede ser mayor si se usa `new`, `delete`, `virtual`. | Menos sobrecarga, más control directo. |
| **Flexibilidad** | Mayor gracias a clases, templates y encapsulación. | Más simple, pero menos modular. |
| **Tiempo de ejecución** | Puede ser impredecible con `exception handling`. | Determinístico, sin manejo de excepciones. |
| **Optimización del compilador** | Puede ser optimizado con `constexpr`, `templates`. | Requiere optimización manual. |

**Buenas prácticas para C++ en tiempo real**  
- Evitar `new` y `delete` (usar memoria preasignada).  
- Evitar `virtual` en código crítico (usar `template`).  
- Usar `constexpr` y `inline` para cálculos en compilación.  
- Si se usa RTOS, optimizar **prioridades de tareas y exclusión mutua**.  

**Ejemplo optimizado con `constexpr`**
```cpp
constexpr int calcularTiempo(int ciclos) {
    return ciclos * 10;  // Se evalúa en compilación
}

constexpr int retardo = calcularTiempo(50);
```
- **Cálculo en compilación, sin penalización en tiempo de ejecución**.  

---

### Modernización del Código Embebido con C++
C++ permite mejorar la organización y eficiencia del código en firmware.  

**Técnicas avanzadas de C++ aplicadas a embebidos**  
- **Uso de `constexpr` para configuraciones en compilación**.  
- **Uso de `template` para evitar código repetitivo sin penalización**.  
- **Uso de `std::array` en lugar de `std::vector`** (evita dinámicas de memoria).  
- **Evitar `std::exception` y `try-catch` en código crítico**.  

**Ejemplo: Definición de pines con `constexpr`**
```cpp
struct GPIO {
    static constexpr uint32_t PIN_LED = 5;
};
```
- **En tiempo de compilación, sin uso de RAM ni variables globales**.  

---

### Librerías y Frameworks Relevantes
Actualmente, hay varios frameworks de **alto nivel** basados en C++ para sistemas 
embebidos.

**Librerías y frameworks usados en C++ embebido**  
| **Framework**  | **Uso**  | **Características**  |
|---------------|---------|---------------------|
| **Arduino (C++)** | IoT, prototipos | Fácil de usar, pero menos optimizado. |
| **mbed OS (C++)** | IoT, ARM Cortex | Soporte para múltiples MCUs, RTOS integrado. |
| **Zephyr (C, C++)** | Sistemas embebidos avanzados | Soporta múltiples arquitecturas, RTOS de bajo consumo. |
| **FreeRTOS (C, C++)** | Multitarea en embebidos | Ligero y altamente usado en la industria. |

**Ejemplo con `mbed OS` en C++**
```cpp
#include "mbed.h"

DigitalOut led(LED1);

int main() {
    while (1) {
        led = !led;
        ThisThread::sleep_for(500ms);
    }
}
```
- Más portable y modular que código en C puro.  

---

### Impacto de C++20 y futuras versiones en embebidos
C++20 introduce mejoras que pueden **optimizar el desarrollo en sistemas embebidos**.

**Características de C++20 útiles en embebidos**  
| **Característica**  | **Ventaja en embebidos**  |
|-------------------|----------------------|
| **`constexpr` mejorado** | Más cálculos pueden resolverse en compilación. |
| **`std::span`** | Alternativa eficiente a `std::vector` sin sobrecarga. |
| **Conceptos y `requires`** | Mejor optimización con `template`. |
| **`std::chrono` extendido** | Medición de tiempo más precisa sin overhead. |

**Ejemplo: `std::span` para evitar dinámicas de memoria**  
```cpp
#include <span>

void imprimir(std::span<const int> datos) {
    for (int d : datos) {
        printf("%d\n", d);
    }
}

int main() {
    int valores[] = {1, 2, 3, 4, 5};
    imprimir(valores);  // Se pasa un array sin crear copias
}
```
- **Evita `std::vector`, reduciendo el uso de memoria dinámica**.  

---

### Resumen
- **C++ sigue evolucionando en sistemas embebidos**, mejorando modularidad y 
  eficiencia.  
- **Los sistemas de tiempo real deben evitar memoria dinámica y código impredecible**.  
- **Frameworks como mbed OS y Zephyr permiten desarrollar firmware moderno en C++**.  
- **C++20 trae optimizaciones clave, como `std::span`, `concepts` y `constexpr` mejorado**.  

---






















