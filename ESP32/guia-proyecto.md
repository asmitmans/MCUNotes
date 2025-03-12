## **Guía de Desarrollo de Proyectos en ESP-IDF: Basada en el Nodo IoT**

---

## **Introducción**

Esta guía describe cómo crear un proyecto en **ESP-IDF** aplicando buenas prácticas 
profesionales, basada en el desarrollo de un **nodo IoT simple** que realiza:

- Lectura de un sensor (DHT22).
- Envío de datos por UART.
- Control de un LED según condiciones específicas.

El enfoque es modular, eficiente y adaptable a sistemas más complejos.

---

## **Estructura del Proyecto**

### **Estructura de Carpetas Recomendada**

```plaintext
nodo_base/
├── CMakeLists.txt               # Configuración global del proyecto
├── sdkconfig                    # Configuración de ESP-IDF (generada automáticamente)
├── main/
│   ├── main.c                   # Punto de entrada del firmware
│   ├── app_tasks.c              # Gestión de la lógica principal
│	├── app_tasks.h 
│   └── CMakeLists.txt
└── components/
    ├── dht22/                   # Componente para el sensor DHT22
    │   ├── dht22.c
    │   ├── dht22.h
    │   └── CMakeLists.txt
    ├── uart_comm/               # Componente para comunicación UART
    │   ├── uart_comm.c
    │   ├── uart_comm.h
    │   └── CMakeLists.txt
    ├── led/                     # Componente para el control del LED
    │   ├── led.c
    │   ├── led.h
    │   └── CMakeLists.txt
    └── sensor_manager/          # Lógica de alto nivel para la gestión de sensores
        ├── sensor_manager.c
        ├── sensor_manager.h
        └── CMakeLists.txt
```

### **Principios de Organización:**
- **`main/`:** Lógica de arranque del sistema.  
- **`components/`:** Módulos independientes y reutilizables.  
- **`sensor_manager/`:** Ejemplo de una capa intermedia que coordina varios componentes.

---

## **Configuración de `CMakeLists.txt`**

### **CMakeLists.txt (Global)**

```cmake
set(EXTRA_COMPONENT_DIRS components)
cmake_minimum_required(VERSION 3.16)

include($ENV{IDF_PATH}/tools/cmake/project.cmake)
project(nodo_base)
```
- **`EXTRA_COMPONENT_DIRS`**: Indica dónde buscar los componentes personalizados.  
- **`project(nodo_base)`**: Define el nombre del proyecto.

---

### **CMakeLists.txt de `main`**

```cmake
idf_component_register(SRCS "main.c" "app_tasks.c" INCLUDE_DIRS "." REQUIRES sensor_manager)
```
- **`SRCS`**: Lista de archivos fuente a compilar.  
- **`INCLUDE_DIRS`**: Directorios donde buscar archivos `.h`.  
- **`REQUIRES`**: Dependencias de otros componentes.

---

### **CMakeLists.txt de un Componente (Ej: `sensor_manager`)**

```cmake
idf_component_register(SRCS "sensor_manager.c" INCLUDE_DIRS "." REQUIRES dht22 uart_comm led)
```
- Esto asegura que el componente `sensor_manager` pueda usar funciones de `dht22`, `uart_comm`, y `led`.

---

## **Gestión del Tiempo: Timers vs. Delays**

### **Evitar `vTaskDelay` para eventos periódicos.**  
Usar `vTaskDelay` bloquea la tarea, lo que puede afectar el rendimiento en sistemas 
más complejos.

### **Uso de Timers de FreeRTOS**

- **Más eficiente:** No bloquea tareas activas.  
- **Flexible:** Puedes iniciar, detener o modificar timers en tiempo de ejecución.  
- **Escalable:** Facilita el manejo de múltiples eventos temporizados.

---

## **Buenas Prácticas de Desarrollo en ESP-IDF**

Esta sección resume las directrices y prácticas profesionales aplicadas en el 
proyecto para asegurar un código **modular, escalable y mantenible**.

---

### **1. Modularización del Código**

- **Separación de Responsabilidades:**  
  Cada componente (como `dht22`, `uart_comm`, `led`) debe tener una responsabilidad 
  clara.

- **Regla de Oro:**  
  **Un componente = una funcionalidad**.  
  Ej: `led` controla el hardware del LED, pero la lógica de cuándo encenderlo está 
  en `sensor_manager`.

---

### **2. Uso de Timers en Lugar de Delays**

- **Evitar:** `vTaskDelay` para eventos periódicos, ya que bloquea la tarea.  
- **Usar:** **Timers de FreeRTOS** para ejecutar funciones periódicamente sin bloquear 
  la CPU.

**Ventajas de los Timers:**
- No bloquean tareas activas.
- Permiten manejo flexible de eventos (iniciar, detener, modificar intervalos).
- Mejoran la eficiencia en sistemas con múltiples eventos concurrentes.

---

### **3. Gestión de Dependencias con `CMakeLists.txt`**

- **Definir Dependencias Localmente:**  
  Cada componente declara sus propias dependencias usando `REQUIRES`, en lugar de 
  hacerlo de forma global.

- **Ejemplo:**
  ```cmake
  idf_component_register(SRCS "sensor_manager.c" INCLUDE_DIRS "." REQUIRES dht22 uart_comm led)
  ```

- **Beneficios:**  
  - Reduce acoplamiento entre módulos.  
  - Facilita la reutilización de componentes en otros proyectos.

---

### **4. Nombres de Funciones y Archivos Claros**

- **Funciones:** Deben describir claramente su propósito.  
  Ej: `led_set_state(true)` indica claramente que encenderá el LED.

- **Archivos:** Usar nombres que reflejen la funcionalidad que contienen.  
  Ej: `sensor_manager.c` gestiona la lógica de sensores, no solo del DHT22.

---

### **5. Inicialización Centralizada**

- **Patrón Recomendado:**  
  Una función de inicialización en `app_tasks.c` que orquesta la configuración de 
  todo el sistema.

- **Ejemplo:**
  ```c
  void iniciar_tareas() {
      sensor_manager_init();
  }
  ```

- **Ventaja:**  
  Mantiene un flujo de inicialización limpio y fácil de entender.

---

### **6. Estructura de Carpetas Coherente**

Organizar el código en carpetas lógicas:

```plaintext
main/               # Arranque del sistema
components/         # Funcionalidades modulares (drivers, lógica de negocio)
├── dht22/
├── uart_comm/
├── led/
└── sensor_manager/
```

- **Regla:** Cada funcionalidad independiente debe estar en su propio componente.

---

### **7. Documentación en el Código**

- **Comentarios Claros:** Explicar *por qué* se hace algo, no solo *qué* hace.  
- **Estructura Sugerida:**
  ```c
  /**
   * @brief Inicializa el gestor de sensores.
   * Configura el DHT22, UART y LEDs.
   */
  void sensor_manager_init();
  ```

- **No abusar de los comentarios:** El código limpio se explica por sí mismo.

---

### **8. Uso de Defines para Configuraciones**

- Definir constantes en los `.h` en lugar de valores "mágicos" en el código.  
- Ejemplo en `uart_comm.h`:
  ```c
  #define BAUD_RATE 115200
  #define TX_PIN    17
  ```

- **Ventaja:** Facilita cambios de configuración sin tocar la lógica del código.

---

## **Flujo de Trabajo para el Desarrollo de Firmware**

Esta sección describe el flujo de trabajo recomendado para desarrollar firmware en ESP-IDF, basado en las directrices aplicadas en el proyecto del nodo IoT. Este 
enfoque asegura un desarrollo estructurado, eficiente y alineado con buenas 
prácticas.

---

### **1. Planificación del Proyecto**

Antes de escribir código, define:  
- **Objetivos funcionales:** ¿Qué debe hacer el firmware?  
- **Requisitos de hardware:** Sensores, actuadores, puertos de comunicación, etc.  
- **Directrices de desarrollo:** Buenas prácticas, modularización, uso de timers, etc.

---

### **2. Configuración Inicial del Proyecto**

1. **Crear un nuevo proyecto con ESP-IDF:**
   ```bash
   idf.py create-project nodo_base
   cd nodo_base
   ```

2. **Estructura básica de carpetas:**
   - `main/` para el arranque del sistema.  
   - `components/` para funcionalidades específicas.

3. **Configurar `CMakeLists.txt`:**  
   Asegura que todos los componentes se detecten correctamente.

---

### **3. Desarrollo Modular de Componentes**

1. **Definir la funcionalidad de cada componente:**
   - Ej: `led` para el control del LED, `dht22` para el sensor, `uart_comm` para la comunicación.

2. **Estructura de cada componente:**
   ```plaintext
   components/
   └── led/
       ├── led.c
       ├── led.h
       └── CMakeLists.txt
   ```

3. **Implementar funciones con nombres claros y bien documentadas.**

---

### **4. Integración de Componentes**

1. **Crear una capa de gestión (Ej: `sensor_manager`):**
   - Encapsula la lógica de negocio que coordina varios componentes.  
   - Ej: Leer el sensor y activar el LED según la temperatura.

2. **Centralizar la inicialización en `app_tasks.c`:**
   ```c
   void iniciar_tareas() {
       sensor_manager_init();
   }
   ```

3. **Asegurar que cada componente tenga su propio `CMakeLists.txt` bien configurado.**

---

### **5. Gestión del Tiempo y Eventos**

- **Para eventos periódicos:** Usar timers de FreeRTOS en lugar de `vTaskDelay`.  
- **Para operaciones concurrentes complejas:** Evaluar el uso de colas, semáforos o tareas.

---

### **6. Pruebas y Depuración**

1. **Compilar y cargar el firmware:**
   ```bash
   idf.py build flash monitor
   ```

2. **Usar el monitor serie para depurar:**
   - Ver logs de `ESP_LOGI`, `ESP_LOGW`, `ESP_LOGE`.  
   - Ajustar niveles de log si es necesario (`make menuconfig`).

3. **Pruebas en hardware:**
   - Validar la funcionalidad en escenarios reales.  
   - Probar casos límite para asegurar la robustez.

---

### **7. Optimización y Mejora Continua**

1. **Revisar el uso de memoria y CPU:**  
   Usar herramientas de ESP-IDF para optimizar el rendimiento.

2. **Refactorizar el código:**  
   - Identificar redundancias.  
   - Mejorar la legibilidad y eficiencia.

3. **Actualizar la documentación:**  
   Asegurar que el código esté bien comentado y que exista documentación técnica del proyecto.

---

### **8. Preparación para Producción**

- **Revisar configuraciones de seguridad.**  
- **Optimizar el firmware para el tamaño final.**  
- **Generar imágenes de firmware para actualizaciones OTA si es necesario.**

---

## **Recomendaciones Finales para Proyectos Profesionales**

En esta sección se resumen las mejores prácticas y recomendaciones adicionales 
que ayudan a mantener un desarrollo de firmware robusto, profesional y sostenible 
a largo plazo.

---

### **1. Estandarización del Código**

- **Nombres Consistentes:** Usa una convención de nombres clara para funciones, 
  variables y archivos. Ej: `led_set_state()`, `dht22_init()`.
- **Indentación Clara:** Mantén un formato uniforme en todo el código (espaciado, 
  llaves, etc.).
- **Documentación Interna:** Comenta el *por qué* de decisiones importantes, no 
  solo el *qué*.

---

### **2. Gestión de Errores y Manejo de Logs**

- **Manejo de Errores Consistente:**  
  - Utiliza códigos de error definidos (`ESP_OK`, `ESP_FAIL`).
  - Retorna errores en funciones críticas y gestiona adecuadamente en el código 
  que las llama.

- **Uso de Logs (`ESP_LOG`):**  
  - Niveles: `ESP_LOGE` (error), `ESP_LOGW` (advertencia), `ESP_LOGI` (informativo).  
  - Incluye el identificador de módulo para facilitar la depuración:
    ```c
    static const char *TAG = "UART_COMM";
    ESP_LOGI(TAG, "UART inicializado correctamente");
    ```

- **Control del Nivel de Log:**  
  - Ajustable desde `menuconfig`:
    ```bash
    idf.py menuconfig
    → Component config → Log output → Set log level
    ```

---

### **3. Optimización de Recursos**

- **Memoria:**  
  - Minimiza el uso de `heap` si no es necesario.  
  - Evita buffers grandes innecesarios.

- **CPU:**  
  - Prefiere timers en lugar de delays.  
  - Reduce el uso de ciclos ocupados (`busy loops`).

- **Energía:**  
  - Considera modos de bajo consumo para aplicaciones IoT.  
  - Apaga periféricos cuando no se usen.

---

### **4. Gestión de Configuraciones**

- **Centraliza Configuraciones:**  
  Define parámetros globales (baudrate, pines, umbrales de sensores) en archivos de cabecera (`config.h` o `defines.h`).

- **Uso de `sdkconfig`:**  
  Configura opciones específicas del hardware desde `menuconfig`.

---

### **5. Modularidad y Reutilización**

- **Componentes Reutilizables:**  
  Diseña cada componente para que pueda ser fácilmente usado en otros proyectos.

- **Separación de Lógica:**  
  La lógica de negocio debe estar separada del control de hardware.  
  Ej: `sensor_manager` gestiona cuándo leer el sensor, pero `dht22` solo sabe cómo hacerlo.

---

### **6. Control de Versiones (Git)**

- **Uso de Git:**  
  Controla el historial del proyecto usando `git`.  
  Ej:
  ```bash
  git init
  git add .
  git commit -m "Versión inicial del nodo IoT"
  ```

- **Organización del Repositorio:**  
  - `main/` para la lógica de aplicación.  
  - `components/` para bibliotecas y drivers.  
  - `docs/` para la documentación.

---

### **7. Mantenimiento y Mejora Continua**

- **Revisión de Código:**  
  Revisa regularmente el código en busca de mejoras.  
  Si es posible, aplica revisiones cruzadas (peer reviews).

- **Pruebas Unitarias:**  
  Implementa pruebas automáticas para validar funciones críticas.

- **Documentación Técnica:**  
  Mantén actualizada la documentación del proyecto (README, diagramas, etc.).

---

## **Cierre: Resumen del Enfoque**

1. **Planificación clara:** Define objetivos y requisitos antes de codificar.  
2. **Estructura modular:** Divide el código en componentes reutilizables.  
3. **Desarrollo eficiente:** Prefiere timers y buenas prácticas de FreeRTOS.  
4. **Pruebas continuas:** Testea en hardware real y usa herramientas de depuración.  
5. **Documentación profesional:** Asegura que el código sea entendible y fácil de mantener.

---

### **Resumen de Procedimiento para Desarrollar un Proyecto en ESP-IDF**

Este resumen presenta los **pasos clave** de forma concisa para estructurar, desarrollar 
e integrar un proyecto en ESP-IDF, aplicando buenas prácticas profesionales.

---

### **Configuración Inicial**

1. **Crear el Proyecto:**
   ```bash
   idf.py create-project nodo_base
   cd nodo_base
   ```

2. **Definir la Estructura de Carpetas:**
   ```plaintext
   main/
   components/
   └── nombre_componente/
   ```

3. **Configurar `CMakeLists.txt` Global:**
   ```cmake
   set(EXTRA_COMPONENT_DIRS components)
   cmake_minimum_required(VERSION 3.16)
   include($ENV{IDF_PATH}/tools/cmake/project.cmake)
   project(nodo_base)
   ```

---

### **Desarrollo de Componentes (Iterativo)**

Para cada componente (ej: `led`, `uart_comm`, `dht22`):

1. **Crear la Estructura:**
   ```bash
   mkdir -p components/led
   touch components/led/led.c components/led/led.h components/led/CMakeLists.txt
   ```

2. **Codificar el Componente:**
   - Implementar funciones específicas (`led_init()`, `led_set_state()`).

3. **Configurar `CMakeLists.txt` del Componente:**
   ```cmake
   idf_component_register(SRCS "led.c" INCLUDE_DIRS ".")
   ```

4. **Crear una Tarea de Prueba Temporal en `main/`:**
   - Verificar que el componente funcione de forma independiente.
   - Compilar y probar:
     ```bash
     idf.py build flash monitor
     ```

5. **Ajustar el Código Tras las Pruebas.**

---

### **Integración de la Lógica de Negocio**

1. **Crear un Módulo de Gestión (Ej: `sensor_manager`):**
   - Encapsula la lógica de negocio (lectura de sensores, decisiones).

2. **Centralizar la Inicialización en `app_tasks.c`:**
   ```c
   void iniciar_tareas() {
       sensor_manager_init();
   }
   ```

3. **Integrar Componentes en `sensor_manager`:**
   - Importar y conectar funciones de todos los módulos.

4. **Configurar Dependencias en `CMakeLists.txt`:**
   ```cmake
   idf_component_register(SRCS "sensor_manager.c" INCLUDE_DIRS "." REQUIRES dht22 uart_comm led)
   ```

---

### **Gestión del Tiempo y Eventos**

- **Reemplazar `vTaskDelay` por Timers de FreeRTOS para tareas periódicas.**
- **Usar colas o semáforos si se requiere comunicación entre tareas.**

---

### **Pruebas Finales**

1. **Compilar y Flashear:**
   ```bash
   idf.py build flash monitor
   ```

2. **Depurar con Logs:**
   - Verificar funcionamiento en hardware real.

3. **Optimizar el Código:**
   - Revisar el uso de recursos y la estructura.

---

### **Mantenimiento y Mejora Continua**

- **Refactorizar y optimizar si es necesario.**  
- **Actualizar la documentación técnica.**  
- **Versionar el proyecto usando Git:**
  ```bash
  git init
  git add .
  git commit -m "Versión final del nodo IoT"
  ```

---