
# 1. Principios generales de programación en C embebido  
_(Basado en el documento Barr C Coding Standard 2018)_

## 1.1 Estándares y Normas de Codificación

* **¿Por qué seguir un estándar?**  
- Reduce errores en el código.  
- Facilita el mantenimiento y la portabilidad.  
- Mejora la legibilidad y consistencia en equipos de desarrollo.  

* **Estándares usados en la industria:**  
- **ISO C (C99 / C11)**: Recomendado para programación de firmware.  
- **MISRA-C**: Estándar usado en la industria automotriz y sistemas críticos.  
- **Barr-C Coding Standard**: Usado en sistemas embebidos de alta confiabilidad.  

**Regla clave:** Se recomienda **C99** como estándar base, evitando características 
no portables de **C11** y dependencias de compiladores específicos.

---

## 1.2 Principios para Reducir Defectos en Código Embebido  

* **Evitar errores comunes**  
- Mantener funciones cortas y con un solo propósito.  
- Minimizar el uso de variables globales.  
- Evitar estructuras de código confusas o con múltiples efectos.  

* **Buenas prácticas para código seguro**  
- Revisar código con análisis estático y herramientas de validación.  
- Evitar el uso de `goto` y otros elementos que compliquen la ejecución.  
- Usar `const` y `volatile` correctamente para definir variables.  
- Definir **valores explícitos** en `enum` para evitar asignaciones inesperadas.  

**Regla clave:** Código **simple y claro** es mejor que código "inteligente" pero 
difícil de mantener.

---

## 1.3 Compatibilidad con MISRA-C  

* **¿Qué es MISRA-C?**  
Es un conjunto de reglas que garantizan código seguro y confiable en sistemas 
embebidos.
Originalmente diseñado para la industria automotriz, pero aplicable en cualquier 
firmware crítico.  

* **Principios clave de MISRA-C**  
- Evitar código ambiguo y no definido en la norma C estándar.  
- Prohibir `malloc()` y `free()` en sistemas embebidos de tiempo real.  
- Garantizar **portabilidad**: evitar dependencias de compiladores específicos.  
- Restringir el uso de conversiones implícitas de tipos (`int` a `char`, etc.).  

**Regla clave:** No se requiere cumplir todas las reglas de **MISRA-C**, pero seguir 
las más importantes mejora la seguridad y confiabilidad del código.  

---

## 1.4 C vs. C++ en sistemas embebidos  

* **¿Debo usar C o C++?**  
- **C** sigue siendo el estándar en sistemas embebidos debido a su simplicidad y 
  control sobre el hardware.  
- **C++** se usa en algunos sistemas embebidos con RTOS avanzados, pero se debe 
  evitar el uso excesivo de características como 
  **herencia múltiple, excepciones y templates**.  

* **C++ en firmware embebido:**  
- Puede mejorar la organización del código con clases y objetos.  
- Útil para desarrollar controladores modulares y reutilizables.  
- Requiere una administración cuidadosa de memoria y tiempos de ejecución.  

**Regla clave:** **Usar C como base** y solo emplear C++ si el proyecto lo justifica, 
evitando características complejas que afecten la estabilidad y rendimiento.  

---

# 2. Reglas de escritura y estilo  

Esta sección cubre las reglas de formato y estilo recomendadas para mejorar la 
legibilidad y mantenibilidad del código en sistemas embebidos.  

## 2.1 Reglas generales de formato  

- El código debe ser consistente en toda la base de código.  
- Se deben seguir estándares de estilo para evitar confusión y errores.  
- El formato debe ser claro para cualquier programador que lea el código.  

Regla clave: La legibilidad es más importante que la brevedad.  

## 2.2 Espaciado, indentación y longitud de línea  

- La indentación debe ser de **cuatro espacios** por nivel de anidamiento. No se 
  deben usar tabulaciones.  
- Cada línea de código debe tener un **máximo de 80 caracteres**.  
- Se debe dejar un espacio después de `if`, `for`, `while`, `switch`, etc.  
- Los operadores binarios (`+`, `-`, `*`, `/`, `&&`, `||`) deben llevar un espacio 
  antes y después.  
- No se debe dejar un espacio entre el nombre de una función y los paréntesis de 
  la llamada: `funcion(argumento);`.  

Ejemplo correcto:  
```c
if (valor == 10) {  
    resultado = valor + 5;  
}
```  

Ejemplo incorrecto:  
```c
if(valor==10){
  resultado=valor+5;
}
```  

Regla clave: Un código bien espaciado reduce errores y mejora la legibilidad.  

## 2.3 Uso adecuado de llaves y paréntesis  

- **Siempre** usar llaves `{}` en estructuras de control, incluso si solo hay una 
  línea de código.  
- La apertura de la llave debe estar en la misma línea de la declaración (`{` no 
  en una nueva línea).  

Ejemplo correcto:  
```c
if (estado == ACTIVO) {  
    ejecutarProceso();  
}
```  

Ejemplo incorrecto:  
```c
if (estado == ACTIVO)  
    ejecutarProceso();
```  

Regla clave: El uso obligatorio de llaves evita errores al modificar el código 
en el futuro.  

## 2.4 Convenciones de nomenclatura  

Nombres claros y descriptivos facilitan la comprensión del código.  

Reglas generales:  
- **Variables**: `nombreEnMinusculasConGuionesBajos` (ejemplo: `contador_total`).  
- **Constantes**: `NOMBRE_EN_MAYUSCULAS` (ejemplo: `TIEMPO_MAXIMO`).  
- **Funciones**: `nombreEnCamelCase()` (ejemplo: `iniciarProceso()`).  
- **Estructuras (`struct`) y tipos (`typedef`)**: `EstructuraCamelCase` (ejemplo: `ConfiguracionSistema`).  

Ejemplo correcto:  
```c
int temperatura_actual;  
const int TIEMPO_LIMITE = 1000;  
void iniciarSistema(void);  
typedef struct ConfiguracionSistema {  
    int velocidad;  
    int temperatura;  
} ConfiguracionSistema;
```  

Ejemplo incorrecto:  
```c
int tempAct;  
const int tiempoLim = 1000;  
void iniciosis();  
typedef struct {  
    int vel;  
    int temp;  
} cfgSys;
```  

Regla clave: Un buen uso de nombres reduce errores y hace que el código sea más 
comprensible.  

---

# 3. Reglas de estructura de código  

Esta sección cubre cómo organizar correctamente los archivos y módulos en un 
proyecto de firmware embebido, asegurando **modularidad, claridad y mantenibilidad**.  

## 3.1 Organización de módulos y archivos  

- Cada módulo debe estar contenido en un **archivo fuente (.c) y un archivo de encabezado (.h)**.  
- Un módulo representa **una funcionalidad única y bien definida** (ejemplo: un 
  driver de SPI, un gestor de memoria, etc.).  
- No debe haber múltiples definiciones de una función en diferentes archivos.  

Ejemplo correcto:  

```
/proyecto_firmware/
├── src/
│   ├── main.c
│   ├── spi.c
│   ├── uart.c
│   ├── sensor.c
│   └── memoria.c
├── include/
│   ├── spi.h
│   ├── uart.h
│   ├── sensor.h
│   └── memoria.h
```

Regla clave: Un módulo = un `.c` + su correspondiente `.h`.  

## 3.2 Uso correcto de `#include`, `#define` y encabezados  

- **Cada archivo `.c` debe incluir solo los encabezados que realmente necesita.**  
- **Todos los archivos de encabezado (`.h`) deben tener un "Include Guard"** para 
  evitar inclusiones múltiples.  
- No se deben usar valores "mágicos" en el código, sino **constantes definidas** 
  en un archivo de configuración.  

Ejemplo correcto de include guard:  
```c
#ifndef SENSOR_H
#define SENSOR_H

void inicializarSensor(void);
int leerTemperatura(void);

#endif
```

Ejemplo incorrecto (sin include guard):  
```c
void inicializarSensor(void);
int leerTemperatura(void);
```

Regla clave: Todos los `.h` deben tener `#ifndef` para evitar inclusiones múltiples.  

## 3.3 Gestión adecuada de funciones y variables globales  

- **Evitar el uso de variables globales siempre que sea posible.**  
- Si es necesario usar variables globales, deben ser `static` dentro del archivo 
  donde se definen.  
- Las funciones que no deben ser visibles fuera de un módulo deben ser `static`.  

Ejemplo correcto:  
```c
// En sensor.c
static int temperatura_interna;  // Variable global oculta a otros archivos

static void calibrarSensor(void) { // Función interna no accesible fuera
    temperatura_interna = 0;
}
```

Ejemplo incorrecto:  
```c
// En sensor.c
int temperatura_interna;  // Accesible desde otros archivos (mala práctica)

void calibrarSensor(void) { // Visible globalmente sin necesidad
    temperatura_interna = 0;
}
```

Regla clave: Variables y funciones globales deben ser `static` salvo que deban 
ser accesibles fuera del módulo.  

**Recordatorio sobre `static`**
- En C, static limita el acceso a variables y funciones dentro de un archivo o 
  mantiene el valor de una variable local.  
- En Java, static hace que algo pertenezca a la clase, compartido por todas las 
  instancias.

---

# 4. Buenas prácticas en tipos de datos  

Esta sección cubre cómo seleccionar correctamente los tipos de datos en C para 
mejorar **portabilidad, eficiencia y seguridad** en sistemas embebidos.  

## 4.1 Uso de tipos de datos con ancho fijo  

- En sistemas embebidos, el tamaño de los tipos de datos estándar (`int`, `short`, 
  `long`) **puede variar** entre plataformas.  
- Para garantizar **portabilidad y precisión**, se recomienda usar los 
  **tipos de ancho fijo** definidos en `<stdint.h>`.  

Ejemplos recomendados:  
```c
#include <stdint.h>

uint8_t  contador;      // 8 bits, siempre
int16_t  temperatura;   // 16 bits, siempre
uint32_t tiempo;        // 32 bits, siempre
```

Ejemplo incorrecto (no garantiza el tamaño en todas las plataformas):  
```c
unsigned char contador; // Puede no ser de 8 bits
short temperatura;      // Puede ser 16 o 32 bits según el compilador
int tiempo;             // Puede ser 16 o 32 bits
```

Regla clave: **Siempre usar `stdint.h` para asegurar el tamaño exacto de las variables.**  

---

## 4.2 Evitar mezclas de tipos `signed` y `unsigned`  

- Mezclar tipos con y sin signo puede causar **comportamiento inesperado** en 
  comparaciones y cálculos.  
- Un número `unsigned` siempre es positivo, mientras que un número `signed` puede 
  ser negativo.  

Ejemplo incorrecto:  
```c
int a = -1;
unsigned int b = 1;

if (a < b) {
    printf("¿Se ejecuta?\n");   // En algunas plataformas, esto NO se ejecuta
}
```
**Explicación:**  
- `a` es `signed` (-1) y `b` es `unsigned` (1).  
- Cuando `a` se convierte a `unsigned`, se vuelve un número grande 
  (ej. 4294967295 en 32 bits).  
- Entonces, la comparación `a < b` en realidad evalúa `4294967295 < 1`, lo cual 
  es `false`.  

Solución correcta:  
```c
int a = -1;
int b = 1;  // Ambos `signed`, comparación segura
```

Regla clave: **No mezclar `signed` y `unsigned` en comparaciones y cálculos.**  

---

## 4.3 Uso correcto de estructuras (`struct`), `union` y alineación en memoria  

Las **estructuras (`struct`)** permiten agrupar datos relacionados, pero su 
alineación en memoria puede afectar el rendimiento y el consumo de espacio.  

Ejemplo incorrecto (mala alineación, desperdicia espacio):  
```c
typedef struct {
    char  estado;   // 1 byte
    int   id;       // 4 bytes
    short valor;    // 2 bytes
} Sensor;
```
Aquí, la memoria se alinea en **múltiplos de 4 bytes**, generando 
**espacios vacíos (padding)** innecesarios.  

Ejemplo correcto (mejor alineación, menos desperdicio):  
```c
typedef struct {
    int   id;       // 4 bytes
    short valor;    // 2 bytes
    char  estado;   // 1 byte
} Sensor;
```
Ahora, el compilador puede empaquetar mejor los datos, reduciendo el uso de 
memoria.  

### Uso de `union` para compartir memoria  
- Un `union` permite almacenar diferentes tipos de datos en 
  **el mismo espacio de memoria**, útil en sistemas embebidos para optimizar 
  recursos.  

Ejemplo de `union`:  
```c
typedef union {
    int entero;
    float flotante;
} Dato;
```
Aquí, `Dato` ocupa **el tamaño del tipo más grande** (`int` o `float`, lo que sea 
mayor).  

Regla clave: **Ordenar `struct` para minimizar padding y usar `union` para optimizar memoria.**  

---

