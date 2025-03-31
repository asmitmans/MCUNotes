
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

# 5. Reglas para declaraciones y estructuras de control

Esta sección se enfoca en cómo escribir declaraciones claras, funciones bien 
estructuradas y estructuras de control seguras y fáciles de mantener.

## 5.1 Declaraciones de funciones y variables

- Cada función debe estar **declarada** en un archivo `.h` y **definida** en su 
  correspondiente `.c`.  
- Las variables deben declararse lo más cerca posible de donde se usan.  
- Evitar declaraciones múltiples de la misma variable o función.  
- Siempre inicializar las variables en el momento de la declaración si es 
  posible.

Ejemplo correcto:
```c
// En sensor.h
int leerTemperatura(void);

// En sensor.c
int leerTemperatura(void) {
    int temperatura = 0;
    // lógica
    return temperatura;
}
```

Ejemplo incorrecto:
```c
int leerTemperatura(); // Declaración vaga, sin tipo de retorno explícito (mala práctica)
```

Regla clave: Declarar funciones y variables de forma clara, completa y cercana 
a su uso.

---

## 5.2 Buenas prácticas en estructuras de control

### If / else

- Siempre usar llaves `{}` incluso si el bloque tiene una sola línea.
- La condición entre paréntesis debe estar clara y no contener efectos 
  secundarios.

Ejemplo correcto:
```c
if (sensorActivo) {
    iniciarLectura();
} else {
    detenerProceso();
}
```

Ejemplo incorrecto:
```c
if (sensorActivo)
    iniciarLectura();  // Riesgo si luego se agrega otra línea sin llaves
```

---

### Switch / case

- Siempre incluir un caso `default`.
- Cada `case` debe terminar en `break` (o `return` si aplica).
- Evitar efectos colaterales dentro de `case`.

Ejemplo correcto:
```c
switch (modo) {
    case MODO_1:
        configurarModo1();
        break;
    case MODO_2:
        configurarModo2();
        break;
    default:
        modo = MODO_DEFECTO;
        break;
}
```

Ejemplo incorrecto:
```c
switch (modo) {
    case MODO_1:
        configurarModo1();
    case MODO_2:
        configurarModo2();  // Falta break → error lógico
}
```

---

### Bucles (for, while, do-while)

- Preferir `for` cuando se conoce la cantidad de iteraciones.
- Inicializar e incrementar variables en la línea del `for` cuando sea posible.
- Evitar modificar la variable de control dentro del cuerpo del bucle.
- Asegurar condiciones de salida para evitar bucles infinitos.

Ejemplo correcto:
```c
for (int i = 0; i < MAX; i++) {
    procesar(i);
}
```

Ejemplo incorrecto:
```c
int i = 0;
while (i < MAX) {
    procesar(i);
    // Se olvida incrementar i
}
```

---

# 6. Manejo de memoria y punteros

Esta sección aborda las prácticas seguras y claras para trabajar con punteros, 
memoria estática, uso limitado de memoria dinámica y palabras clave especiales 
como `const`, `static` y `volatile`.

## 6.1 Uso de memoria dinámica

- **Evitar `malloc()` y `free()`** en sistemas embebidos, especialmente en tiempo 
  real.
- La fragmentación de memoria y los errores en la gestión dinámica pueden 
  provocar fallas impredecibles.
- Se prefiere el uso de **memoria estática o preasignada**.

Ejemplo no recomendado:
```c
char* buffer = malloc(128);  // Puede fallar si no hay memoria disponible
```

Alternativa recomendada:
```c
char buffer[128];  // Memoria estática, más predecible
```

Regla clave: En sistemas embebidos, usar memoria estática a menos que se 
justifique claramente lo contrario.

---

## 6.2 Uso correcto de punteros

- **Inicializar siempre los punteros** antes de usarlos.
- Evitar punteros colgantes (que apuntan a memoria liberada o inválida).
- Verificar punteros antes de acceder a la memoria que referencian.

Ejemplo seguro:
```c
int dato = 5;
int* ptr = &dato;

if (ptr != NULL) {
    procesar(*ptr);
}
```

Ejemplo incorrecto:
```c
int* ptr;
procesar(*ptr);  // Error: ptr no inicializado
```

Regla clave: Los punteros deben ser válidos, inicializados y comprobados antes 
de usarlos.

---

## 6.3 Uso de `const` para proteger datos

- Indica que una variable **no debe cambiarse** después de ser inicializada.
- Es útil tanto para valores fijos como para asegurar que funciones no 
  modifiquen argumentos.

Ejemplo:
```c
const int TIEMPO_MAXIMO = 1000;

void imprimir(const char* mensaje) {
    printf("%s\n", mensaje);
}
```

Regla clave: Usar `const` siempre que una variable no deba cambiar, ayuda a 
evitar errores y mejora la documentación del código.

---

## 6.4 Uso de `volatile` en variables compartidas o de hardware

- Indica al compilador que **el valor de una variable puede cambiar en cualquier momento** 
  (por ejemplo, en una ISR o por hardware).
- Evita que el compilador optimice lecturas/escrituras que deben ser visibles 
  inmediatamente.

Ejemplo típico:
```c
volatile int bandera_ready = 0;

void ISR_handler(void) {
    bandera_ready = 1;
}

void loop() {
    while (!bandera_ready);  // Sin `volatile`, el compilador podría optimizar esto y generar un bucle infinito
}
```

Regla clave: Usar `volatile` en variables modificadas fuera del flujo principal 
(ISR, DMA, registros hardware).

---

## 6.5 Uso de `static` para limitar el ámbito

- Ya lo vimos antes: en variables globales o funciones, `static` **limita el acceso al archivo actual**.
- En variables locales, `static` **preserva el valor entre llamadas**.

Regla clave: Usar `static` para encapsular datos o funciones que no deben ser 
accesibles desde otros archivos.

---

# 7. Reglas para interrupciones y tiempo real

Esta sección aborda cómo escribir código seguro y robusto en presencia de 
interrupciones, una parte esencial del firmware embebido, especialmente en 
sistemas de tiempo real.

## 7.1 Escritura de rutinas de interrupción (ISR)

- Las **ISR deben ser cortas y rápidas**.
- No deben llamar a funciones complejas, realizar cálculos pesados ni usar 
  llamadas bloqueantes (como `delay()`).
- Se recomienda **solo leer datos, establecer banderas o copiar a buffers**, y 
  luego salir.

Ejemplo correcto:
```c
volatile uint8_t dato_listo = 0;

void USART_IRQHandler(void) {
    buffer_rx = USART->DR;
    dato_listo = 1;
}
```

Ejemplo incorrecto:
```c
void USART_IRQHandler(void) {
    procesarMensaje();  // Mal: procesamiento largo dentro de la ISR
}
```

Regla clave: Las ISR solo deben hacer lo mínimo necesario y delegar el 
procesamiento.

---

## 7.2 Variables compartidas entre ISR y código principal

- Debes declarar como `volatile` toda variable que pueda ser modificada por una 
  ISR y leída desde el código principal (o viceversa).
- Esto evita que el compilador optimice su acceso y garantiza 
  lecturas/escrituras reales.

Ejemplo:
```c
volatile uint8_t bandera_adc = 0;

void ADC_IRQHandler(void) {
    bandera_adc = 1;
}

int main(void) {
    while (!bandera_adc);  // `volatile` es necesario para que esto funcione correctamente
}
```

Regla clave: Toda variable compartida entre ISR y código principal debe ser 
`volatile`.

---

## 7.3 Prevención de condiciones de carrera

- Si accedes a variables que pueden ser modificadas simultáneamente por una ISR 
  y el main, **usa técnicas para garantizar acceso atómico**, como:

  - Deshabilitar temporalmente las interrupciones (`__disable_irq()` / `cli()`).
  - Usar funciones atómicas del compilador o procesador (si existen).

Ejemplo:
```c
__disable_irq();
contador++;
__enable_irq();
```

Regla clave: El acceso a datos compartidos debe ser atómico para evitar 
resultados inconsistentes.

---

## 7.4 Uso de banderas y doble buffer

- Para comunicar eventos entre ISR y el bucle principal, es preferible usar 
  **banderas** (`volatile`) o **buffers circulares**.
- Si transfieres datos grandes, considera un **doble buffer**: uno para la ISR, 
  otro para el procesamiento.

Ejemplo de bandera:
```c
volatile uint8_t rx_flag = 0;

void UART_IRQHandler(void) {
    rx_buffer = UART->DR;
    rx_flag = 1;
}
```

Ejemplo de doble buffer (concepto):
```c
// buffer1 usado por ISR, buffer2 por main
```

Regla clave: La comunicación entre ISR y tareas principales debe ser clara, 
rápida y segura.

---

## 7.5. Cuando usar un contador (`while (contador > 0)`) en interrupciones

Usa un contador si:

- **Cada evento importa individualmente** (no quieres perder ninguno).
- El evento puede **ocurrir varias veces** mientras estás ocupado procesando 
  uno.
- Ejemplos:
  - **Contador de pulsos** (ej. RPM, flujo, encoder).
  - **Detección de múltiples interrupciones UART o ADC**.
  - **Medición de frecuencia**.

En estos casos, usar una bandera perdería todos los eventos excepto el último.

**Ejemplo:**
```c
volatile uint16_t contador_pulsos = 0;

void ISR_pulso(void) {
    contador_pulsos++;
}

void main(void) {
    while (contador_pulsos > 0) {
        contador_pulsos--;
        registrar_pulso();
    }
}
```

---

## 7.6 Cuando una bandera (`if (bandera)`) es suficiente en una interrupción

Usa solo una bandera cuando:

- **Solo te interesa saber si el evento ocurrió**, no cuántas veces.
- El evento **no es crítico si se pierde** mientras estás ocupado.
- Ejemplos:
  - **Botón** para abrir una puerta o iniciar un proceso.
  - **Fin de conversión ADC** (si solo necesitas el último valor).
  - **Dato disponible** en UART para leer de un buffer.

En estos casos, si llega un segundo evento mientras procesas el primero, 
**lo puedes ignorar** porque no te afecta.

**Ejemplo:**
```c
volatile uint8_t boton_presionado = 0;

void ISR_boton(void) {
    boton_presionado = 1;
}

void main(void) {
    if (boton_presionado) {
        boton_presionado = 0;
        iniciar_accion();  // Se ejecuta solo una vez, aunque hayas presionado varias veces
    }
}
```

**Conclusión:**

> Cuando importa **la cantidad de eventos**, usa un contador.  
> Cuando solo importa **si ocurrió al menos uno**, una bandera basta.  

---

# 8. Reglas de depuración y seguridad

Esta sección presenta recomendaciones para escribir código embebido más seguro, 
fácil de verificar, depurar y mantener en entornos críticos.

## 8.1 Uso de `assert` y `static_assert`

- `assert()` permite detectar errores lógicos durante el desarrollo (solo si las 
  comprobaciones no se eliminan en compilación).
- `static_assert` evalúa expresiones **en tiempo de compilación**, útil para validar 
  tamaños de estructuras, tipos, límites, etc.

Ejemplo con `assert` (requiere `#include <assert.h>`):
```c
assert(valor >= 0);  // Termina el programa si la condición no se cumple (en debug)
```

Ejemplo con `static_assert` (desde C11):
```c
_Static_assert(sizeof(int) == 4, "int no tiene 4 bytes");
```

Regla clave: Usar `assert` en desarrollo para validar condiciones críticas y 
`static_assert` para errores detectables en tiempo de compilación.

---

## 8.2 Evitar comportamiento indefinido

- No acceder a memoria fuera de límites (`array[5]` en un arreglo de 5 elementos).
- No hacer divisiones por cero.
- No usar punteros no inicializados.
- No modificar una variable más de una vez en una sola expresión (esto causa 
  resultados impredecibles).

Ejemplo incorrecto:
```c
a = a++ + ++a;  // Comportamiento indefinido
```

Ejemplo correcto:
```c
a++;
a = a + 1;
```

Regla clave: Evitar construcciones complejas y ambigüedades que el compilador no 
garantiza evaluar en orden definido.

---

## 8.3 Uso de herramientas de análisis estático

- Son programas que analizan el código sin ejecutarlo y detectan errores 
  comunes, violaciones de estándares y vulnerabilidades.
- Pueden integrarse al flujo de desarrollo y prevenir fallos antes de compilar.

Ejemplos:
- `cppcheck`, `clang-tidy`, `splint` (gratis).
- PC-lint, Coverity, MISRA compliance tools (comerciales).

Regla clave: Integrar herramientas de análisis estático para mejorar la calidad 
antes de probar en hardware.

---

## 8.4 Validar entradas y proteger contra desbordamientos

- Siempre validar datos externos antes de usarlos (entradas del usuario, 
  registros de periféricos, buffers).
- Verificar los límites antes de copiar datos a arreglos o escribir en memoria.

Ejemplo:
```c
if (indice < MAX_TAM) {
    arreglo[indice] = valor;
}
```

Nunca asumir que la entrada es válida sin comprobarla.

Regla clave: Todo dato externo o no controlado debe validarse antes de usar.

---

## 8.5 Evitar macros peligrosas

- Evitar macros con efectos colaterales, especialmente si usan parámetros 
  múltiples o expresiones complejas.
- Usar funciones en lugar de macros cuando se pueda, o definir macros con 
  paréntesis en todo.

Ejemplo seguro:
```c
#define CUADRADO(x) ((x) * (x))  // Bien: paréntesis protegen
```

Ejemplo problemático:
```c
#define CUADRADO(x) x * x  // Mal: CUADRADO(a + 1) se evalúa como a + 1 * a + 1
```

Regla clave: Proteger macros con paréntesis o reemplazarlas por funciones si son 
complejas.

---

Perfecto, aquí tienes un nuevo punto que resume y formaliza esta recomendación:

---

# 9. Buenas prácticas en el uso de tipos estándar vs. tipos de tamaño fijo

Esta sección explica qué tipos del lenguaje C deben evitarse en firmware 
embebido y cuáles se deben preferir para garantizar portabilidad, claridad y 
control sobre los recursos del sistema.

## 9.1 Evitar tipos clásicos de tamaño variable

Los tipos como `int`, `short`, `long` y sus versiones sin signo (`unsigned`) tienen 
un **tamaño que varía según la arquitectura y el compilador**.

| Tipo clásico     | Tamaño variable según plataforma     |
|------------------|--------------------------------------|
| `int`            | 16 o 32 bits                         |
| `unsigned int`   | 16 o 32 bits                         |
| `short`          | 16 bits normalmente, pero no fijo    |
| `long`           | 32 o 64 bits                         |
| `unsigned long`  | Ídem                                 |

Este comportamiento hace que el mismo código pueda comportarse de forma distinta 
en microcontroladores diferentes.

**Regla clave:** No usar tipos con tamaño dependiente de la plataforma si 
necesitas resultados predecibles.

---

## 9.2 Usar tipos con tamaño fijo

C99 introdujo el encabezado `<stdint.h>` con tipos explícitos y portables.

| Tipo de `<stdint.h>` | Tamaño garantizado |
|-----------------------|--------------------|
| `uint8_t`             | 8 bits sin signo   |
| `int8_t`              | 8 bits con signo   |
| `uint16_t`            | 16 bits sin signo  |
| `int16_t`             | 16 bits con signo  |
| `uint32_t`            | 32 bits sin signo  |
| `int32_t`             | 32 bits con signo  |
| `uint64_t`            | 64 bits sin signo  |
| `int64_t`             | 64 bits con signo  |

**Ventajas:**
- Comportamiento predecible entre plataformas.
- Claridad del rango de valores.
- Mejor control del uso de memoria.

---

## 9.3 ¿Qué tipos estándar siguen siendo aceptables?

- `char`: aceptado para manejo de texto (`char*`, `char[]`), aunque se puede usar 
  `uint8_t` para datos binarios de 8 bits.
- `float`: aceptado si necesitas precisión decimal (por ejemplo, sensores).
- `double`: aceptado solo si el hardware lo soporta eficientemente. En micros 
  pequeños puede ser costoso.
- `size_t`: aceptado para funciones estándar (`strlen`, `memcpy`), aunque también 
  tiene tamaño variable.

---

## 9.4 Recomendaciones prácticas

- **Siempre que se use una variable numérica en firmware**, declarar con tipo fijo 
  (`uint8_t`, `int16_t`, etc.).
- Evitar `int`, `short`, `long`, excepto en código muy específico o portado.
- Reservar `char` para texto, `float` para valores decimales y `size_t` en funciones 
  estándar.

---

Perfecto. Aquí tienes un resumen organizado con **todas las reglas clave** que vimos, agrupadas por tema para facilitar la búsqueda rápida. Este puede usarse como **guía de referencia** en tu documentación personal de desarrollo de firmware.

---

# Resumen de reglas y buenas prácticas en desarrollo de firmware en C

## 1. Principios generales

- Seguir un estándar como **C99** y evitar extensiones no portables.
- Adoptar guías como **Barr-C** o **MISRA-C** si el proyecto lo justifica.
- Evitar código complejo o "inteligente"; preferir claridad.

## 2. Estilo y escritura

- Indentación: **4 espacios**, no tabs.
- Líneas de máximo **80 caracteres**.
- Siempre usar **llaves `{}`** en estructuras de control, incluso para una línea.
- Nombres:
  - Variables: `minusculas_con_guiones`
  - Constantes: `MAYUSCULAS`
  - Funciones: `camelCase()`
  - Structs/typedefs: `NombreCapitalizado`

## 3. Estructura de código

- Un módulo = **archivo `.c` + encabezado `.h`**
- Incluir solo los headers necesarios.
- Usar `static` para limitar el alcance de funciones o variables internas.
- Incluir guardas en todos los headers (`#ifndef`...`#define`...`#endif`).

## 4. Tipos de datos

- **Usar `<stdint.h>`** para `uint8_t`, `int16_t`, `uint32_t`, etc.
- Evitar `int`, `short`, `long`, `unsigned`.
- Usar `char` solo para texto.
- Usar `float` si es necesario; evitar `double` en micros pequeños.

## 5. Declaraciones y control de flujo

- Declarar variables cerca de su uso.
- Evitar condiciones con efectos colaterales.
- Incluir `default` y `break` en `switch`.
- Evitar modificar variables de control dentro de bucles.

## 6. Memoria y punteros

- **Evitar `malloc()` y `free()`** salvo necesidad real.
- Usar **memoria estática** por defecto.
- Siempre inicializar punteros (a dirección válida o `NULL`).
- Usar `const` para datos inmutables.
- Usar `volatile` para variables compartidas con ISR o hardware.
- Usar `static` para limitar el ámbito o preservar valor entre llamadas.

## 7. Interrupciones y tiempo real

- Las ISR deben ser **cortas y rápidas**.
- No hacer procesamiento pesado ni bloqueante en ISR.
- Usar `volatile` para compartir variables entre main e ISR.
- Proteger accesos no atómicos con `__disable_irq()` / `__enable_irq()`.
- Usar **contadores** si no se deben perder eventos; banderas si solo importa que ocurrió.

## 8. Depuración y seguridad

- Usar `assert()` para verificar condiciones en tiempo de ejecución.
- Usar `_Static_assert()` para validar en tiempo de compilación.
- Evitar comportamiento indefinido (como accesos fuera de límites).
- Validar entradas externas antes de usarlas.
- Escribir macros seguras (con paréntesis) o usar funciones si es posible.
- Usar herramientas de análisis estático como `cppcheck`, `clang-tidy`.

## 9. Tipos estándar vs. tamaño fijo

- Reemplazar `int`, `short`, `long`, etc. por tipos de `<stdint.h>`.
- Usar `uint8_t`, `int16_t`, `uint32_t`, etc. para portabilidad y control.
- Usar `char` para texto y `size_t` en funciones estándar.

---

