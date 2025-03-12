### **Parte 1: Fundamentos del ESP32 y su Entorno de Desarrollo**

El **ESP32** es un microcontrolador de Espressif que incluye **Wi-Fi y Bluetooth** integrados, con un diseño optimizado para IoT y aplicaciones embebidas de baja 
potencia.

#### **1. Arquitectura del ESP32**
- **CPU:** Doble núcleo Xtensa LX6/LX7 (en la mayoría de versiones).
- **Memoria:** RAM interna (~520 KB) y soporte para PSRAM externa.
- **Almacenamiento:** Flash SPI externa, EEPROM emulada.
- **Periféricos:** ADC, DAC, UART, SPI, I2C, I2S, PWM, GPIOs, etc.
- **Modos de energía:** Activo, Modem-sleep, Light-sleep, Deep-sleep, Hibernación.

#### **2. FreeRTOS en el ESP32**
ESP-IDF usa **FreeRTOS** como sistema operativo en tiempo real (RTOS).  
- **Multitarea con planificación prioritaria.**
- **Administración de memoria dinámica y estática.**
- **Mecanismos de sincronización:** Semáforos, Mutexes, Colas, Eventos.  
- **Timers y watchdogs.**  
- **Idle Task y Garbage Collection (Tareas inactivas).**

#### **3. ESP-IDF (Espressif IoT Development Framework)**
- **SDK oficial** para desarrollar en ESP32.
- Incluye herramientas de **compilación, depuración y monitoreo**.
- Uso de **CMake** y **Kconfig** para configuración de proyectos.
- Manejo de componentes modulares.
- Soporte para **OTA (Over-The-Air Updates)**.


### **Parte 2: Flujo de Desarrollo Profesional y Estructura de Proyectos en ESP-IDF**

Un desarrollo profesional con ESP32 y ESP-IDF sigue una estructura modular basada en buenas prácticas de software embebido.

---

### **1. Estructura de un Proyecto ESP-IDF**
ESP-IDF organiza proyectos en una estructura basada en **componentes modulares**.  
La estructura típica de un proyecto se ve así:

```
/mi_proyecto_esp32
│─── CMakeLists.txt       # Archivo principal de compilación
│─── sdkconfig            # Configuración del proyecto (autogenerado)
│─── main/                # Código principal del proyecto
│    │── CMakeLists.txt   # Configuración de compilación del main
│    │── main.c           # Punto de entrada del firmware
│─── components/          # Componentes personalizados o externos
│    │── mi_componente/
│        │── CMakeLists.txt
│        │── mi_componente.c
│        │── mi_componente.h
│─── build/               # Carpeta generada por la compilación (no se versiona)
│─── flash/               # Contiene la imagen binaria lista para flashear
│─── partitions.csv       # Configuración de particiones de memoria
│─── sdkconfig.defaults   # Configuración base del sistema
```

**Buenas prácticas:**
- **`main/`** debe ser liviano y delegar la lógica a componentes.
- **`components/`** contiene módulos reutilizables, encapsulando funcionalidades específicas (ej. drivers, conectividad, sensores).
- **`sdkconfig`** define parámetros globales ajustables con `menuconfig`.
- Separar **configuración, lógica de negocio y controladores de hardware**.

---

### **2. Flujo de Trabajo Profesional**
El desarrollo de firmware con ESP32 sigue un flujo iterativo basado en diseño 
modular.

1. **Definir Requisitos y Particiones**
   - Seleccionar el esquema de particiones (`partitions.csv`).
   - Definir si se usará actualización OTA.

2. **Diseñar la Arquitectura del Firmware**
   - Identificar **módulos principales** (sensores, comunicación, almacenamiento).
   - Establecer **tareas de FreeRTOS** y su prioridad.
   - Decidir **método de comunicación** entre tareas:  
     - **Colas (Queues)** → Transferencia eficiente de datos entre tareas.  
     - **Eventos (Event Groups)** → Sincronización rápida de señales binarias.  
     - **Semáforos/Mutex** → Acceso seguro a recursos compartidos.  

3. **Implementación**
   - Programar tareas como **hilos separados en FreeRTOS**.
   - Utilizar `esp_log.h` para depuración eficiente.
   - Configurar watchdogs y gestión de errores.

4. **Pruebas y Depuración**
   - Monitorizar con `idf.py monitor`.
   - Usar breakpoints con `gdb` si se necesita depuración avanzada.
   - Simular eventos para verificar la estabilidad del firmware.

5. **Despliegue y Optimización**
   - Habilitar actualización OTA si es necesario.
   - Reducir consumo de energía con modos de suspensión.
   - Optimizar memoria y tiempos de ejecución.


### **Parte 3: Uso Avanzado de FreeRTOS en ESP32**

FreeRTOS es el sistema operativo en tiempo real que administra las tareas en el ESP32. Comprender su funcionamiento es clave para un desarrollo eficiente y profesional.

---

### **1. Gestión de Tareas en FreeRTOS**
FreeRTOS maneja múltiples **tareas (threads)** con planificación basada en prioridades.  

**Estructura de una tarea en FreeRTOS (ESP-IDF)**
```c
void mi_tarea(void *pvParameter) {
    while (1) {
        printf("Ejecutando tarea...\n");
        vTaskDelay(pdMS_TO_TICKS(1000));  // Pausa 1 segundo
    }
}

void app_main() {
    xTaskCreate(&mi_tarea, "MiTarea", 4096, NULL, 5, NULL);
}
```
🔹 **Parámetros de `xTaskCreate()`**
- `nombre_funcion` → Función de la tarea.  
- `"NombreTarea"` → Identificador en depuración.  
- `stack_size` → Tamaño de la pila (en bytes).  
- `parametro` → Parámetro opcional a pasar a la tarea.  
- `prioridad` → Determina el orden de ejecución.  
- `handle` → Permite administrar la tarea externamente.

**Buenas prácticas:**
- Definir tareas **con prioridad correcta** (0 a 25, más alto = mayor prioridad).
- Evitar **bloqueos largos** en tareas críticas.
- Minimizar el uso de tareas innecesarias, usando interrupciones o timers cuando sea posible.

---

### **2. Comunicación entre Tareas**
ESP32 permite la comunicación eficiente entre tareas usando **colas, eventos y semáforos**.

🔹 **Colas (Queues)**
- Se usan para **transferir datos entre tareas**.
- Ejemplo: Un sensor enviando datos a una tarea de procesamiento.

```c
QueueHandle_t mi_cola;
mi_cola = xQueueCreate(10, sizeof(int));  // Cola con 10 espacios para enteros
xQueueSend(mi_cola, &valor, portMAX_DELAY);  // Enviar dato
xQueueReceive(mi_cola, &valor_recibido, portMAX_DELAY);  // Recibir dato
```

🔹 **Eventos (Event Groups)**
- Permiten **sincronizar múltiples tareas** con señales binarias.

```c
EventGroupHandle_t evento;
evento = xEventGroupCreate();
xEventGroupSetBits(evento, BIT0);  // Activar BIT0
xEventGroupWaitBits(evento, BIT0, pdTRUE, pdFALSE, portMAX_DELAY);  // Esperar BIT0
```

🔹 **Semáforos y Mutex**
- Se usan para **controlar acceso a recursos compartidos**.

```c
SemaphoreHandle_t mutex;
mutex = xSemaphoreCreateMutex();
xSemaphoreTake(mutex, portMAX_DELAY);  // Bloquear recurso
xSemaphoreGive(mutex);  // Liberar recurso
```

**Buenas prácticas:**
- Usar **colas** para **transferencia de datos** entre tareas.
- Usar **eventos** cuando varias tareas deban esperar una señal.
- Usar **mutex** para proteger acceso a recursos críticos.

---

### **3. Gestión de Memoria y Watchdog**
ESP32 tiene **memoria limitada**, por lo que una buena gestión es crucial.

🔹 **Heap y Stack**
- La memoria en ESP-IDF se maneja con `pvPortMalloc()` y `vPortFree()`.
- Se recomienda **evitar malloc** en tareas críticas y preasignar memoria.

🔹 **Watchdog Timer**
- Protege contra **bloqueos inesperados**.
- Se activa con `esp_task_wdt_init(timeout_segundos, habilitar_reset)`.
- Se alimenta con `esp_task_wdt_reset()`.

**Buenas prácticas:**
- Usar buffers estáticos en vez de `malloc()` cuando sea posible.
- Implementar watchdogs en tareas de prioridad alta.
- Monitorear consumo de memoria con `heap_caps_get_free_size(MALLOC_CAP_8BIT)`.


### **Parte 4: Conectividad y Almacenamiento en ESP32**

El ESP32 es ideal para IoT porque integra **Wi-Fi y Bluetooth**, además de opciones de almacenamiento en Flash para configuraciones y datos persistentes.

---

### **1. Conectividad Wi-Fi en ESP32**
El ESP32 puede operar en tres modos:
- **STA (Station Mode):** Se conecta a una red Wi-Fi.
- **AP (Access Point Mode):** Crea su propia red Wi-Fi.
- **STA + AP:** Ambas al mismo tiempo.

**Ejemplo de conexión a Wi-Fi (Modo Station)**
```c
wifi_config_t wifi_config = {
    .sta = {
        .ssid = "MiRed",
        .password = "Clave123",
    },
};
esp_wifi_set_config(WIFI_IF_STA, &wifi_config);
esp_wifi_start();
```
**Buenas prácticas:**
- Usar **almacenamiento no volátil (NVS)** para guardar credenciales Wi-Fi.
- Implementar **reconexión automática** en caso de fallos (`esp_event_handler_register`).
- Configurar **Wi-Fi Low Power Mode** para optimizar consumo energético.

---

### **2. Bluetooth y BLE en ESP32**
El ESP32 soporta:
- **Bluetooth Clásico:** Para transmisión de audio y datos.
- **Bluetooth Low Energy (BLE):** Para comunicación de baja potencia con sensores y dispositivos móviles.

**Ejemplo de escaneo BLE**
```c
esp_ble_gap_start_scanning(10);  // Escanea dispositivos BLE por 10 segundos
```
**Buenas prácticas:**
- **Usar BLE** en lugar de Wi-Fi si se necesita bajo consumo.
- Implementar **modo de suspensión** entre conexiones BLE para ahorrar batería.

---

### **3. Almacenamiento en ESP32**
Existen varias opciones para almacenar datos en ESP32:

🔹 **NVS (Non-Volatile Storage)**
- Se usa para guardar **configuraciones y credenciales** en Flash.

```c
nvs_handle mi_nvs;
nvs_open("storage", NVS_READWRITE, &mi_nvs);
nvs_set_i32(mi_nvs, "contador", 42);
nvs_commit(mi_nvs);
nvs_close(mi_nvs);
```

🔹 **SPIFFS (Sistema de Archivos en Flash)**
- Permite leer y escribir archivos en la memoria Flash.
- Ideal para **archivos de configuración, logs y HTML** en servidores embebidos.

🔹 **SD Card**
- Para almacenamiento externo con **SPI o SDIO**.
- Se recomienda formatear en **FAT32**.

**Buenas prácticas:**
- **Evitar escribir frecuentemente en Flash** para prolongar su vida útil.
- **Usar caché** en RAM para reducir accesos innecesarios.
- **Deshabilitar logs en producción** si no son necesarios.

---

### **4. Actualización de Firmware (OTA)**
Las actualizaciones OTA permiten actualizar el firmware del ESP32 sin conectarlo físicamente.

🔹 **Tipos de OTA en ESP32**
- **OTA Manual:** Se descarga y escribe la nueva imagen en Flash manualmente.
- **OTA con ESP-IDF:** Usa `esp_https_ota()` para descargar y flashear una nueva versión automáticamente.

**Ejemplo de configuración de particiones para OTA**
```plaintext
# partitions.csv
nvs,      data, nvs,     0x9000,  0x6000,
otadata,  data, ota,     0xf000,  0x2000,
phy_init, data, phy,     0x11000, 0x1000,
ota_0,    app,  ota_0,   0x20000, 0x140000,
ota_1,    app,  ota_1,   0x160000, 0x140000,
```
**Buenas prácticas:**
- **Dividir la memoria Flash** en particiones OTA (`ota_0` y `ota_1`).
- **Firmar y verificar** el firmware para evitar ataques malintencionados.
- **Validar la actualización antes de reiniciar** para evitar bricks (`esp_ota_mark_app_valid()`).

---

### **Parte 5: Modos de Bajo Consumo y Optimización de Energía en ESP32**

El ESP32 es ideal para aplicaciones de bajo consumo gracias a sus modos de suspensión y técnicas de optimización de energía.

---

### **1. Modos de Bajo Consumo en ESP32**
El ESP32 tiene varios modos de suspensión para reducir el consumo de energía.

| **Modo**         | **Consumo Aproximado** | **Estado** |
|-----------------|----------------------|------------|
| **Active Mode** | ~160-240 mA          | CPU encendido, Wi-Fi/BLE activos. |
| **Modem-sleep** | ~3-20 mA             | CPU activo, Wi-Fi suspendido entre paquetes. |
| **Light-sleep** | ~0.8-3 mA            | CPU suspendido, RAM retenida, periféricos pueden despertar. |
| **Deep-sleep**  | ~10-150 μA           | CPU apagado, RAM no retenida, solo RTC despierta. |
| **Hibernation** | ~5 μA                | Todo apagado, solo GPIO o RTC pueden despertar. |

**Ejemplo de Light-Sleep con temporizador**
```c
esp_sleep_enable_timer_wakeup(5000000);  // Despertar en 5s
esp_light_sleep_start();  // Entrar en Light-Sleep
```

**Ejemplo de Deep-Sleep con GPIO como wake-up**
```c
esp_sleep_enable_ext0_wakeup(GPIO_NUM_0, 1);  // Despertar con GPIO 0 en HIGH
esp_deep_sleep_start();  // Entrar en Deep-Sleep
```

**Buenas prácticas:**
- Usar **Light-Sleep** si se requiere despertar rápido sin perder RAM.
- Usar **Deep-Sleep** en sensores IoT que solo transmiten datos periódicamente.
- Evitar **polling en loops**, usar interrupciones (`gpio_isr_handler_add()`).

---

### **2. Optimización de Wi-Fi y Bluetooth**
Wi-Fi y Bluetooth son los principales consumidores de energía en el ESP32.

🔹 **Wi-Fi Low Power Mode**
- Reduce consumo al activar `WIFI_PS_MIN_MODEM`.

```c
wifi_config_t wifi_config = {
    .sta = {
        .ssid = "MiRed",
        .password = "Clave123",
    },
};
esp_wifi_set_ps(WIFI_PS_MIN_MODEM);  // Activar modo de bajo consumo
esp_wifi_set_config(WIFI_IF_STA, &wifi_config);
esp_wifi_start();
```

🔹 **BLE en modo de bajo consumo**
- Se recomienda configurar intervalos largos de advertising y conexión.

```c
esp_ble_gap_config_adv_data_raw(adv_data, adv_size);
esp_ble_gap_start_advertising(&adv_params);
```

**Buenas prácticas:**
- Reducir **frecuencia de transmisión Wi-Fi** si no es necesaria la conexión continua.
- Configurar **intervalos largos en BLE** (`conn_interval_min` y `conn_interval_max`).
- Usar **caché de paquetes** para reducir la cantidad de transmisiones.

---

### **3. Optimización del Uso de Memoria y CPU**
- **Reducir el uso de tareas en FreeRTOS**: Evitar tareas innecesarias.
- **Usar timers en lugar de delays bloqueantes** (`xTimerCreate()`).
- **Evitar malloc() en tiempo de ejecución**, preasignar buffers estáticos.
- **Deshabilitar logs y debugging en producción** (`ESP_LOG_NONE`).

**Ejemplo de Timer en lugar de `vTaskDelay()`**
```c
TimerHandle_t mi_timer;
void mi_callback(TimerHandle_t xTimer) {
    printf("Evento del timer\n");
}
mi_timer = xTimerCreate("Timer", pdMS_TO_TICKS(1000), pdTRUE, NULL, mi_callback);
xTimerStart(mi_timer, 0);
```


### **Parte 6: Manejo de Errores, Depuración Avanzada y Seguridad en ESP32**

Un desarrollo profesional con ESP32 requiere **manejo robusto de errores**, **técnicas de depuración** y **medidas de seguridad** para proteger el firmware.

---

### **1. Manejo de Errores en ESP32**
ESP-IDF ofrece mecanismos para detectar y manejar errores de forma eficiente.

🔹 **Código de Retorno y `ESP_ERROR_CHECK()`**
- Muchas funciones de ESP-IDF devuelven un `esp_err_t` con códigos de error estándar.
- `ESP_ERROR_CHECK()` facilita la detección de fallos en tiempo de ejecución.

**Ejemplo de manejo de errores**
```c
esp_err_t ret = esp_wifi_start();
if (ret != ESP_OK) {
    ESP_LOGE("WiFi", "Error al iniciar Wi-Fi: %s", esp_err_to_name(ret));
}
```
**Códigos de error comunes:**
- `ESP_OK` → Operación exitosa.
- `ESP_ERR_NO_MEM` → Memoria insuficiente.
- `ESP_ERR_INVALID_ARG` → Argumento inválido.
- `ESP_FAIL` → Error genérico.

---

### **2. Técnicas de Depuración en ESP32**
ESP-IDF permite depuración avanzada a través de logs, GDB y herramientas de monitoreo.

🔹 **Logging con `esp_log.h`**
- Se usa para imprimir mensajes en diferentes niveles de severidad.

**Ejemplo de logging**
```c
ESP_LOGE("ERROR", "Mensaje de error");
ESP_LOGW("WARNING", "Mensaje de advertencia");
ESP_LOGI("INFO", "Mensaje informativo");
ESP_LOGD("DEBUG", "Mensaje de depuración");
ESP_LOGV("VERBOSE", "Mensaje detallado");
```
**Buenas prácticas:**
- Usar `ESP_LOGE()` solo para errores críticos.
- Deshabilitar logs en producción con `make menuconfig` (`CONFIG_LOG_DEFAULT_LEVEL_NONE`).

🔹 **Monitor en Tiempo Real (`idf.py monitor`)**
- Permite ver logs y reinicios en vivo por puerto serie.

```sh
idf.py monitor
```

🔹 **Depuración con GDB**
- Compatible con **JTAG Debugging** para inspeccionar memoria y variables en ejecución.

```sh
xtensa-esp32-elf-gdb -x gdbinit build/mi_proyecto.elf
```

**Buenas prácticas:**
- **Usar `assert()`** para detectar errores lógicos (`assert(variable != NULL);`).
- **Activar watchdog** para reiniciar en caso de fallos (`esp_task_wdt_add()`).
- **Registrar fallos de arranque** con `esp_reset_reason()`.

---

### **3. Seguridad en ESP32**
Para proyectos IoT, la seguridad es fundamental. ESP32 ofrece mecanismos para proteger datos y firmware.

🔹 **Protección del Firmware**
- **Firma de firmware** (`Secure Boot`) para evitar ejecución de código no autorizado.
- **Cifrado de Flash** (`Flash Encryption`) para proteger datos almacenados.

**Activación de Secure Boot**
```sh
idf.py menuconfig
# Activar "Enable hardware Secure Boot in bootloader"
```

**Activación de Flash Encryption**
```sh
idf.py menuconfig
# Activar "Enable Flash Encryption"
```

🔹 **Comunicación Segura**
- Usar **TLS con certificados** (`mbedtls` en ESP-IDF).
- **Evitar contraseñas en texto plano**, almacenarlas en **NVS cifrado**.
- Usar **JWT o OAuth** en autenticaciones en la nube.

**Ejemplo de conexión HTTPS segura**
```c
esp_http_client_config_t config = {
    .url = "https://servidor.com",
    .cert_pem = certificado_ca_pem,
};
esp_http_client_handle_t cliente = esp_http_client_init(&config);
esp_http_client_perform(cliente);
```

**Buenas prácticas:**
- **Deshabilitar acceso a JTAG en producción** (`CONFIG_SECURE_JTAG`).
- **Configurar certificados y claves seguras**, evitando contraseñas en el código fuente.
- **Verificar integridad del firmware OTA** (`esp_image_verify()`).


### **Parte 7: Buenas Prácticas Generales y Flujo de Desarrollo en Proyectos Complejos**

Un desarrollo profesional con ESP32 debe seguir principios de diseño modular, manejo eficiente de recursos y metodologías de trabajo adecuadas.

---

### **1. Principios de Diseño Modular**
En proyectos grandes, mantener una arquitectura clara es clave para la escalabilidad y mantenibilidad.

🔹 **División en Componentes**
- Separar funcionalidades en **componentes reutilizables** dentro de `components/`.
- Un componente debe **encapsular una única funcionalidad** (ej. Wi-Fi, sensores, almacenamiento).
- **No mezclar lógica de aplicación con controladores de hardware.**

**Ejemplo de estructura modular**
```
/mi_proyecto_esp32
│─── main/                # Código principal
│    │── main.c           # Punto de entrada
│─── components/          # Componentes modulares
│    │── wifi_manager/    # Manejo de Wi-Fi
│    │── sensor_driver/   # Manejo de sensores
│    │── storage/         # Almacenamiento en NVS
│─── sdkconfig            # Configuración global
│─── partitions.csv       # Esquema de particiones
```
**Buenas prácticas:**
- Crear **interfaces (`.h`) bien definidas** para cada componente.
- Usar `static` para limitar el alcance de funciones a su módulo.
- Documentar el uso de cada componente en comentarios y `README.md`.

---

### **2. Flujo de Trabajo Profesional**
El desarrollo de firmware con ESP32 sigue una metodología iterativa con pruebas continuas.

**Flujo típico de desarrollo**
1. **Definición del Proyecto**
   - Identificar módulos principales (**Wi-Fi, sensores, almacenamiento, etc.**).
   - Definir **particiones de memoria** (`partitions.csv`).
   - Establecer **modos de bajo consumo y seguridad**.

2. **Configuración del Entorno**
   - Usar `idf.py set-target esp32` para definir la arquitectura.
   - Configurar parámetros con `idf.py menuconfig`.

3. **Implementación Modular**
   - Crear módulos en `components/`.
   - Definir **tareas FreeRTOS** con prioridades bien distribuidas.
   - Implementar comunicación entre tareas (colas, eventos, semáforos).

4. **Pruebas y Depuración**
   - Usar `idf.py monitor` para analizar logs.
   - Implementar pruebas unitarias (`Unity Framework` en ESP-IDF).
   - Medir consumo de memoria con `heap_caps_get_free_size()`.

5. **Optimización y Seguridad**
   - Reducir consumo energético con **modos de suspensión**.
   - Cifrar firmware con **Flash Encryption**.
   - Implementar **OTA segura** para futuras actualizaciones.

6. **Despliegue y Mantenimiento**
   - Documentar el código y las interfaces de los módulos.
   - Monitorear fallos con `esp_reset_reason()`.
   - Implementar mecanismos de **reconexión Wi-Fi/BLE**.

---

### **3. Métodos de Comunicación en Proyectos Complejos**
Para aplicaciones IoT avanzadas, es clave elegir un método de comunicación eficiente.

🔹 **Protocolo MQTT (Message Queue Telemetry Transport)**
- Ideal para comunicación con la nube y dispositivos IoT.
- Funciona con **Publicador/Suscriptor**.
- Compatible con **AWS IoT, Azure IoT, Mosquitto**.

**Ejemplo de Publicación MQTT**
```c
esp_mqtt_client_publish(cliente, "sensor/temperatura", "25.3", 0, 1, 0);
```

🔹 **Protocolo HTTP/HTTPS**
- Útil para comunicación con servidores REST.
- Puede combinarse con **JSON** para intercambio de datos estructurados.

🔹 **Comunicación Local (UART, SPI, I2C)**
- UART para **comunicación con otros microcontroladores**.
- SPI para **sensores de alta velocidad**.
- I2C para **múltiples dispositivos en un solo bus**.

**Buenas prácticas:**
- Usar **MQTT sobre TLS** para seguridad en la nube.
- **Evitar polling**, preferir eventos e interrupciones.
- **Minimizar el uso de Wi-Fi activo** para prolongar batería.

---

### **Conclusión: ESP32 en Desarrollo Profesional**
Trabajar con ESP32 de manera profesional implica:
- **Diseño modular** y separación de responsabilidades.  
- **Uso eficiente de FreeRTOS** para multitarea estable.  
- **Optimización de energía** con Deep-Sleep y Light-Sleep.  
- **Depuración avanzada** con logs, GDB y monitor serie.  
- **Seguridad en firmware y comunicaciones** con TLS, Secure Boot y Flash Encryption.  
- **Metodologías de desarrollo estructuradas** para proyectos escalables.  


---


## **1. Concepto de Componentes en ESP-IDF**
ESP-IDF recomienda organizar los proyectos en **componentes (`components/`)**, que funcionan como **librerías reutilizables**. Un **componente** puede contener controladores de hardware, middleware o incluso lógica de negocio modularizada.

**Diferencias clave entre un enfoque tradicional y ESP-IDF:**
| **Enfoque Tradicional (C estándar)** | **ESP-IDF (Componentes Modulares)** |
|--------------------------------------|--------------------------------------|
| `dht22.c` y `dht22.h` en `main/`     | `components/dht22/dht22.c` y `dht22.h` |
| `uart.c` y `uart.h` en `main/`       | `components/uart/uart.c` y `uart.h` |
| `common.h` con macros/config.        | `config.h` o `sdkconfig` de ESP-IDF |
| `main.c` con lógica de aplicación    | `main/main.c` solo inicializa tareas |

---

## **2. Estructura de Archivos Recomendados**
Una estructura **modular y escalable** para tu proyecto sería algo así:

```
/mi_proyecto_esp32
│─── CMakeLists.txt         # Configuración global de compilación
│─── sdkconfig              # Configuración del sistema (autogenerado)
│─── main/                  # Lógica principal del firmware
│    │── main.c             # Inicializa el sistema y las tareas
│    │── app_tasks.c        # Define y arranca las tareas
│    │── app_tasks.h
│─── components/            # Módulos independientes (componentes)
│    │── dht22/             # Módulo para sensor DHT22
│    │   │── dht22.c
│    │   │── dht22.h
│    │   │── CMakeLists.txt
│    │── uart/              # Módulo para UART
│    │   │── uart.c
│    │   │── uart.h
│    │   │── CMakeLists.txt
│    │── led/               # Módulo para control de LEDs
│    │   │── led.c
│    │   │── led.h
│    │   │── CMakeLists.txt
│─── build/                 # Archivos compilados (no se versiona)
│─── sdkconfig.defaults      # Configuración base del sistema
│─── partitions.csv          # Configuración de particiones de memoria
```

---

## **3. Explicación de cada parte**
🔹 **`main/main.c`**  
- Solo se encarga de **inicializar el sistema y las tareas**.  
- No debe contener lógica de sensores, UART ni LEDs.

🔹 **`main/app_tasks.c` y `app_tasks.h`**  
- Define las **tareas de FreeRTOS** (`xTaskCreate()`).  
- Conecta los módulos (`dht22`, `uart`, `led`).  
- Coordina eventos (ej. cada 10s lee el sensor y envía por UART).

🔹 **`components/dht22/`**  
- Implementa solo la lectura del sensor DHT22.  
- Exponer funciones como `dht22_read()` en su header.

🔹 **`components/uart/`**  
- Encapsula la configuración y envío de datos por UART.  
- Función pública: `uart_send(const char* mensaje)`.

🔹 **`components/led/`**  
- Maneja los LEDs (GPIO 21 y 22).  
- Función pública: `led_set_state(gpio_num_t led, bool state)`.

---

## **4. Flujo de Ejecución del Firmware**
1. **`main.c`** inicializa FreeRTOS y arranca las tareas (`app_tasks.c`).
2. **`app_tasks.c`** maneja la ejecución:
   - **Cada 10s:** Llama a `dht22_read()`, recibe temperatura y la manda por `uart_send()`.
   - **Evento especial:** Prende/apaga LEDs con `led_set_state()`.
3. Los **componentes encapsulan la lógica específica** y son reutilizables.

---

## **1. Organización de la Lógica de Negocio en Firmware**
En un firmware más complejo, el flujo de ejecución puede ser **reactivo, paralelo o secuencial**, dependiendo de los requisitos.

### **Ejemplo 1: Enfoque Secuencial (Tu Proyecto Base)**
En tu primer proyecto, la ejecución sigue un flujo **secuencial y predecible**:
1. **Cada 10s**, el timer dispara una acción.
2. Se lee el **DHT22** y el dato se envía por **UART**.
3. Se activa o desactiva un **LED**.
4. Se vuelve a esperar **10s** y el ciclo se repite.

📌 **Aquí, `app_tasks.c` solo define el orden de las operaciones**:
```c
while (1) {
    float temperatura = dht22_read();
    uart_send("Temperatura: %.2f°C", temperatura);
    led_set_state(GPIO_NUM_21, true);  // LED verde ON
    vTaskDelay(pdMS_TO_TICKS(10000)); // Espera 10s
}
```
🔹 Este enfoque funciona bien para **tareas repetitivas**, pero es limitado para sistemas con múltiples eventos simultáneos.

---

### **Ejemplo 2: Enfoque Reactivo con Eventos**
En sistemas embebidos más avanzados, se prefiere **reaccionar a eventos en lugar de ejecutar en bucles fijos**.

**Ejemplo con FreeRTOS Event Groups**  
- Se define un `EventGroupHandle_t` para eventos como **"nuevo dato del sensor"** o **"botón presionado"**.
- Las tareas esperan estos eventos en vez de ejecutarse en loop.

```c
EventGroupHandle_t eventos;

void tarea_sensor(void *param) {
    while (1) {
        float temperatura = dht22_read();
        xEventGroupSetBits(eventos, BIT0);  // Dispara evento
        vTaskDelay(pdMS_TO_TICKS(10000));
    }
}

void tarea_uart(void *param) {
    while (1) {
        xEventGroupWaitBits(eventos, BIT0, pdTRUE, pdFALSE, portMAX_DELAY);
        uart_send("Temperatura actualizada!");
    }
}
```
🔹 **Aquí la lógica de negocio se dispara cuando hay datos nuevos**, en lugar de ejecutarse cíclicamente.

---

### **Ejemplo 3: Enfoque con Procesos en Paralelo**
Si tu proyecto crece, podrías tener **tareas concurrentes**:
1. **Tarea 1**: Leer el sensor y almacenar el dato.
2. **Tarea 2**: Enviar datos por UART solo cuando un usuario lo solicite.
3. **Tarea 3**: Manejar botones o interrupciones externas.

**Ejemplo con Colas de FreeRTOS**  
En este caso, una tarea se encarga de recolectar datos y enviarlos a otra tarea encargada de la comunicación.

```c
QueueHandle_t cola_datos;

void tarea_sensor(void *param) {
    float temperatura;
    while (1) {
        temperatura = dht22_read();
        xQueueSend(cola_datos, &temperatura, portMAX_DELAY);
        vTaskDelay(pdMS_TO_TICKS(10000));
    }
}

void tarea_uart(void *param) {
    float temperatura;
    while (1) {
        xQueueReceive(cola_datos, &temperatura, portMAX_DELAY);
        uart_send("Temperatura: %.2f°C", temperatura);
    }
}
```
🔹 **Cada tarea se ejecuta en paralelo y se comunican entre sí** mediante una cola de mensajes.

---

## **2. Conclusión: ¿Dónde Va la Lógica de Negocio?**
- **Los módulos (`components/`) solo implementan funcionalidades específicas (DHT22, UART, LED).**  
- **La lógica de negocio (cuándo hacer qué) se define en `app_tasks.c`, pero puede ser secuencial, reactiva o paralela.**  
- **Si el proyecto crece, FreeRTOS permite tareas independientes que se comunican por eventos o colas.**  
- **Si tu sistema tiene muchos eventos (sensores, botones, Wi-Fi), se recomienda un enfoque basado en eventos en lugar de un bucle secuencial.**  

---

## **CMakeLists**

**CMake** es una herramienta de automatización de compilación utilizada para gestionar 
proyectos en C/C++. En el entorno de ESP-IDF, **`CMakeLists.txt`** define cómo se 
organiza y compila el proyecto.

### **¿Qué hace `CMakeLists.txt`?**
- **Organiza el código fuente:** Especifica los archivos `.c` y `.h` que deben compilarse.  
- **Gestión de dependencias:** Declara qué bibliotecas o componentes externos necesita 
  cada módulo.  
- **Configuración del proyecto:** Establece configuraciones como el nombre del proyecto 
  o la versión mínima de CMake.

### **Estructura Básica**
   **`CMakeLists.txt` en la raíz del proyecto:**  
   - Define la configuración global del proyecto.  
   - Incluye la ruta de los componentes externos si es necesario.  
   ```cmake
   set(EXTRA_COMPONENT_DIRS components/esp-idf-lib/components)
   cmake_minimum_required(VERSION 3.16)
   include($ENV{IDF_PATH}/tools/cmake/project.cmake)
   project(nodo_base)
   ```

   **`CMakeLists.txt` en cada componente:**  
   - Define los archivos fuente y dependencias del componente específico.  
   ```cmake
   idf_component_register(SRCS "dht22.c" INCLUDE_DIRS "." REQUIRES dht)
   ```

### **Flujo de Trabajo**
1. **CMake analiza los `CMakeLists.txt`** para identificar la estructura del proyecto.  
2. **Genera archivos de construcción** (para Ninja o Make).  
3. **Compila el proyecto** respetando las dependencias y configuraciones definidas.  

---

## **DHT22**

El **DHT22** es un sensor digital de temperatura y humedad ampliamente utilizado 
en aplicaciones IoT.

### **Características Principales**
- **Medición de Temperatura:** Rango de -40°C a +80°C con una precisión de ±0.5°C.  
- **Medición de Humedad:** Rango del 0% al 100% de humedad relativa con ±2-5% de precisión.  
- **Alimentación:** 3.3V o 5V, ideal para microcontroladores como el ESP32.

### **Protocolo de Comunicación**
- **Protocolo Digital Propietario:** Utiliza una sola línea de datos (single-wire) para 
  enviar la información.  
- **Sin I2C ni SPI:** Aunque es digital, no emplea protocolos estándar como I2C o SPI.  
- **Esquema de Pulsos:** Los datos se codifican mediante la duración de pulsos de alto 
  y bajo voltaje.

### **Lectura de Datos**
- **Lectura Simultánea de Temperatura y Humedad:**  
  - Aunque algunas implementaciones dividen la lectura en funciones separadas, 
  el sensor envía ambos datos en un solo paquete de 40 bits.  
  - Optimizar el código para realizar una sola lectura mejora la eficiencia.

---

## **Gestión de Librerías en ESP-IDF**

### **¿Dónde se Almacenan las Librerías?**
- Las librerías de terceros suelen colocarse en la carpeta `components/esp-idf-lib/`.  
- **No se instalan automáticamente:** Deben clonarse o descargarse manualmente.  
- Puedes organizarlas directamente en `components/` si prefieres una estructura más 
  simple.

### **¿Es Obligatorio Usar `esp-idf-lib`?**
- **No es obligatorio.**  
- Es una convención para mantener el proyecto ordenado, especialmente si usas 
  múltiples librerías externas.

### **Incluir Librerías en el Proyecto**
- Se deben declarar en el `CMakeLists.txt` raíz:  
  ```cmake
  set(EXTRA_COMPONENT_DIRS components/esp-idf-lib/components)
  ```
- Luego se incluyen en cada componente según sea necesario con `REQUIRES`.

---

## **Uso de `REQUIRES` en Componentes**

### **¿Dónde Definir las Dependencias?**
- **Se recomienda definirlas en el componente que las necesita.**  
- Esto mantiene la modularidad y facilita el mantenimiento.

### **Ejemplo Correcto:**
```cmake
idf_component_register(SRCS "dht22.c" INCLUDE_DIRS "." REQUIRES dht)
```
- Aquí, solo el componente `dht22` sabe que depende de `dht`.  
- Si otro componente no necesita `dht`, no se ve afectado.  

---

## **Componentes en ESP-IDF**

   En el contexto de **ESP-IDF**, cada unidad de funcionalidad modular se denomina 
   un **componente**.

Un **componente** puede ser:  
- Un **módulo de hardware** (como el DHT22).  
- Una **característica de software** (como una interfaz de comunicación UART).  
- O incluso una **lógica de aplicación específica** (como un gestor de tareas o 
  control de eventos).  

Esto permite mantener el código **modular, reutilizable y fácil de mantener**. 

---

## Uso de Tareas o Funciones 
  La decisión de **usar tareas o simplemente llamar funciones de un componente** 
depende de **cómo y cuándo** necesitas que esa acción ocurra.

---

### **¿Se Puede Encender un LED Sin Usar una Tarea Separada?**
**Sí, puedes hacerlo perfectamente.**  
Por ejemplo, dentro de la tarea del DHT22:

```c
if (temperature > 30) {
    led_set_state(true);  // Encender LED si la temperatura es mayor a 30°C
} else {
    led_set_state(false); // Apagar LED en caso contrario
}
```

Esto no va en contra de ninguna "filosofía" de ESP-IDF.

---

### **¿Cuándo Usar Solo Funciones y Cuándo Usar Tareas?**

| **Escenario**                     | **¿Tarea Necesaria?** | **Explicación**                              |
|-----------------------------------|-----------------------|---------------------------------------------|
| **Acción simple y rápida**        | ❌ No                 | Encender/apagar un LED toma microsegundos.  |
| **Lectura de sensores simples**   | ❌ No                 | Si no necesitas lecturas periódicas.        |
| **Operación periódica o concurrente** | ✅ Sí               | Para procesos que requieren temporización.  |
| **Procesos que bloquean o tardan** | ✅ Sí                | Ej. comunicación I/O que puede demorar.     |

---

### **Filosofía de Diseño en ESP-IDF**

- **Modularidad:** Los componentes (como `led`, `uart_comm`, `dht22`) deben ser 
  reutilizables y fáciles de integrar.  
- **Flexibilidad:** Puedes usarlos como funciones simples o integrarlos en tareas 
  según lo requiera tu lógica.  
- **Eficiencia:** No crear tareas innecesarias. Cada tarea consume recursos (RAM 
  y CPU).  

---

### **Ejemplo Práctico**

Si quieres **encender un LED cada vez que lees la temperatura**, sin crear una tarea 
extra:

```c
void tarea_dht22(void *pvParameter) {
    vTaskDelay(pdMS_TO_TICKS(3000));
    float temperature, humidity;
    char buffer[64];

    while (1) {
        if (dht22_read(&temperature, &humidity)) {
            snprintf(buffer, sizeof(buffer), "Temp: %.1f°C, Hum: %.1f%%\r\n", temperature, humidity);
            uart_comm_send(buffer);

            // Encender LED si la temperatura supera los 30°C
            led_set_state(temperature > 30);
        }
        vTaskDelay(pdMS_TO_TICKS(2000));
    }
}
```

**Resultado:** Funciona perfectamente, sin necesidad de crear otra tarea.

---

### **Conclusión**

- **No necesitas una tarea para cada acción simple.**  
- **Usa tareas solo si la lógica lo requiere** (procesos periódicos, bloqueantes, o concurrentes).  
- **ESP-IDF es flexible:** Adáptalo a tus necesidades sin preocuparte de "romper" alguna filosofía rígida.

---

### **Gestión de la Conexión Wi-Fi en ESP32: ¿Por qué Inicializar Wi-Fi de Forma Secuencial?**

---

### **Introducción**

En el desarrollo de aplicaciones IoT con ESP32, es común observar que la 
inicialización del Wi-Fi se realiza de forma secuencial, antes de ejecutar otras 
tareas del sistema, como la lectura de sensores o la transmisión de datos. Aunque 
podría parecer más eficiente manejar la conexión Wi-Fi en una tarea separada, este 
enfoque tiene fundamentos sólidos en términos de eficiencia y simplicidad.

---

### **Razones para Inicializar Wi-Fi de Forma Secuencial**

#### **A) Wi-Fi como Requisito Crítico**

- **Dependencia de la Conectividad:** En muchos dispositivos IoT, como sensores remotos 
  o nodos de monitoreo, la función principal depende de la capacidad de enviar datos a 
  través de la red.  
- **Sin Wi-Fi, sin sentido:** Si el dispositivo no puede conectarse, otras operaciones 
  (como la lectura de sensores) podrían no tener propósito, ya que los datos no podrían 
  transmitirse.

#### **B) Simplicidad en la Gestión de Errores**

- **Detección Temprana de Problemas:** La inicialización secuencial permite identificar de inmediato si hay problemas de conexión.  
- **Respuesta Rápida a Fallos:** En caso de error, se puede detener el sistema o activar un modo de recuperación sin complicaciones adicionales.

#### **C) El Stack de Wi-Fi Ya Gestiona Eventos en Segundo Plano**

- **Manejo Asíncrono Interno:** Aunque la inicialización sea secuencial, el stack de 
  Wi-Fi de ESP-IDF gestiona eventos de red en segundo plano de forma asíncrona.  
- **Menor Sobrecarga de Tareas:** No es necesario crear una tarea dedicada para 
  "vigilar" el estado de la red, ya que ESP-IDF lo hace automáticamente mediante su 
  sistema de eventos.

---

### **¿Cuándo es Mejor Usar una Tarea para Wi-Fi?**

En sistemas más complejos, podría ser necesario manejar el Wi-Fi en una tarea 
dedicada, especialmente si:

- **Aplicaciones en Tiempo Real:** Cuando otras tareas críticas no pueden depender de la inicialización del Wi-Fi.  
- **Reconexión Inteligente:** Si se requiere una lógica compleja de reconexión que no dependa del bucle principal.  
- **Manejo Avanzado de Redes:** Dispositivos que cambian de red dinámicamente o alternan entre modos STA y AP.

---

### **Alternativa Híbrida: Gestión Basada en Eventos**

Una buena práctica en sistemas profesionales es combinar ambos enfoques:

- **Inicialización Secuencial:** Se asegura que la red esté configurada antes de iniciar tareas críticas.  
- **Gestión Basada en Eventos:** Se responde a eventos de conexión/desconexión sin necesidad de una tarea activa permanente.

Ejemplo de eventos en ESP-IDF:
```c
esp_event_handler_register(WIFI_EVENT, ESP_EVENT_ANY_ID, &wifi_event_handler, NULL);
```

Esto permite que otras partes del sistema reaccionen de forma asíncrona a cambios 
en la red.

---

### **Conclusión**

- **Para nodos IoT simples:** La inicialización secuencial de Wi-Fi es eficiente, clara y suficiente.  
- **Para sistemas complejos:** Considerar tareas dedicadas o gestión basada en eventos para mayor flexibilidad.  
- **Mejor práctica:** Iniciar Wi-Fi de forma secuencial y usar eventos para manejar reconexiones o cambios de estado.

---

## **Gestión de Tareas y Comunicación Inter-Tareas en ESP-IDF**

---

## **1. ¿Cuándo y Por Qué Usar una Tarea para un Componente?**

En FreeRTOS (base de ESP-IDF), una **tarea** es un hilo de ejecución independiente 
que permite realizar operaciones concurrentes. Sin embargo, 
**no todo componente necesita su propia tarea**, ya que esto implica consumo de 
recursos (RAM y CPU).

### **Casos en los que SÍ se Debe Usar una Tarea:**

1. **Procesos que Requieren Ejecución Continua o Periódica:**
   - Ej: Monitoreo de sensores que generan datos de forma continua.

2. **Operaciones de Larga Duración o Bloqueantes:**
   - Ej: Comunicaciones HTTP, lecturas de archivos, operaciones de red.

3. **Procesos Asíncronos Independientes:**
   - Ej: Gestión de Wi-Fi, Bluetooth, o una UART que recibe datos en cualquier 
     momento.

1. **Separación de Responsabilidades:**
   - Mejora la legibilidad y el mantenimiento del código.

---

### **Casos en los que NO es Necesario Usar una Tarea:**

1. **Operaciones Simples y Rápidas:**
   - Encender un LED, leer un pin GPIO.

2. **Eventos Esporádicos que Pueden Manejarse con ISR (Interrupciones):**
   - Ej: Pulsación de un botón.

3. **Uso de Timers o Callbacks Suficiente:**
   - Ej: Temporizadores de software para tareas simples sin bloqueo.

---

## **2. ¿Por Qué Usar Colas para la Comunicación Entre Tareas?**

En FreeRTOS, existen varios mecanismos para la **sincronización y comunicación** 
entre tareas. Las **colas** (`xQueue`) son uno de los más utilizados.

### **Ventajas de Usar Colas:**

1. **Transferencia de Datos Segura:**
   - Permiten enviar datos entre tareas de forma **segura para hilos** (thread-safe).

2. **Sincronización Implícita:**
   - Una tarea puede esperar (bloquearse) hasta que haya datos disponibles.

3. **Manejo de Múltiples Productores y Consumidores:**
   - Ideal cuando varias tareas producen datos y otra(s) los consumen.

4. **Facilidad para Encolar Estructuras Complejas:**
   - Se pueden enviar enteros, estructuras, o incluso punteros.

---

### **¿Cuándo NO Usar Colas?**

1. **Cuando Solo se Requiere una Simple Señalización:**
   - Mejor usar **semáforos** o **event groups** si no se necesita enviar datos, solo indicar 
    un evento.

1. **Para Transferencia de Datos Masivos o Críticos en Tiempo Real:**
   - Las colas añaden cierta latencia. En casos críticos, se puede usar **buffers circulares** 
    o **DMA**.

1. **Alta Frecuencia de Mensajes Pequeños:**
   - Puede volverse ineficiente si la comunicación es muy rápida y simple. En 
    este caso, se prefiere un **event group**.

---

## ⚡ **3. ¿Colas o Event Groups?**

| **Aspecto**                | **Colas (`xQueue`)**                      | **Event Groups**                     |
|----------------------------|------------------------------------------|-------------------------------------|
| **Propósito**              | Transferencia de datos                   | Señalización de eventos             |
| **Datos Transferidos**     | Cualquier tipo de dato                   | Bits individuales (banderas)        |
| **Bloqueo**                | Tareas pueden bloquearse esperando datos| Tareas pueden esperar eventos       |
| **Complejidad**            | Más complejas para simples señales       | Simples y eficientes para flags     |
| **Ejemplo de Uso**         | Enviar temperatura de un sensor          | Notificar que Wi-Fi está conectado  |

---

## **4. ¿Por Qué Usamos una Cola en Nuestro Proyecto?**

En el proyecto del ESP32:

- **Problema:** Estábamos usando un **timer** para gestionar directamente la lectura del 
  DHT22 y el envío HTTP, lo que causó un **desbordamiento de pila**.  
- **Solución:** Creamos una **tarea dedicada** para realizar las operaciones pesadas.
- **Comunicación:** Usamos una **cola** porque:
  - Queremos **transferir datos de manera segura** entre el timer (productor) y la tarea 
  del sensor (consumidor).
  - Permite que la tarea **espere pasivamente** hasta que reciba un mensaje, sin consumir 
  CPU innecesariamente.
  - La cola garantiza que los eventos no se pierdan si llegan varios rápidamente.

---

## **Conclusión**

- **Usa tareas** para operaciones complejas, bloqueantes o que deben ejecutarse de forma 
  independiente.  
- **Usa colas** para transferir datos de forma segura entre tareas.  
- **Considera event groups** si solo necesitas notificaciones simples sin datos.

---

## **Variable global dentro del mismo archivo (`static`)** o en el **archivo de encabezado (`extern`)**

### **¿Por qué dejarlo como una variable `static` global en `sensor_manager.c`?**

1. **Encapsulamiento Local:**
   - Al declararlo como `static`, el timer solo es accesible dentro de `sensor_manager.c`.
   - Esto protege la variable de accesos no deseados desde otros módulos, lo cual 
  es una buena práctica en diseño modular.

1. **Facilidad de Control:**
   - Nos permite pausar, reiniciar o modificar el timer fácilmente desde cualquier 
  función dentro de `sensor_manager.c`, sin necesidad de pasar punteros o referencias.

1. **Eficiencia:**
   - No hay un impacto en la eficiencia de la memoria porque sigue siendo una 
  variable local al archivo.
   - En sistemas embebidos, reducir la complejidad de punteros mejora la 
  mantenibilidad del código.

---

### **¿Cuándo sería mejor declararlo en el `.h` con `extern`?**

1. **Si otros módulos necesitan controlar el timer:**
   - Por ejemplo, si desde `main.c` o `app_tasks.c` quisiéramos pausar el timer cuando 
  el ESP entra en modo de bajo consumo.

1. **Interacción entre componentes:**
   - Si el timer se usa como parte de un sistema de sincronización mayor, donde 
  varios módulos deben coordinarse usando el mismo temporizador.

---

### **Recomendación Final para Nuestro Proyecto**

Dado que el **timer solo es usado dentro de `sensor_manager.c`**, mantenerlo como 
`static` global en ese archivo es la **mejor opción**:

```c
static TimerHandle_t sensor_timer;
```

Si en el futuro necesitamos controlarlo desde otro módulo, podríamos exponerlo en 
el `.h` usando `extern`. Por ahora, estamos bien con el enfoque actual.

---

## Donde declara tipo de datos creado 

Para decidir si declarar `sensor_data_t` en el `.h` o en el `.c`, consideremos estos 
puntos clave:

---

### **1. ¿Se Necesita Fuera de `sensor_manager.c`?**

- **Si solo se usa internamente** (como en este caso):  
  **Mejor declararlo en `sensor_manager.c`** para mantener el encapsulamiento.

- **Si otros módulos necesitan acceder o manipular esta estructura**:  
  **Debemos declararlo en `sensor_manager.h`**.

---

### **En Este Proyecto:**

Actualmente, **`sensor_data_t` solo es usado dentro de `sensor_manager.c`**.  
Por lo tanto, es preferible mantenerlo en el `.c` para cumplir con estas buenas 
prácticas:

1. **Encapsulamiento:** Reduce la exposición innecesaria de estructuras internas.  
2. **Claridad:** Facilita el mantenimiento, ya que otros componentes no dependen de 
   su definición.  
3. **Seguridad:** Previene modificaciones accidentales desde otros módulos.

---

### **¿Cómo Quedaría?**

#### En `sensor_manager.c`:

```c
// Definición interna de la estructura
typedef struct {
    float temperature;
    float humidity;
} sensor_data_t;
```

#### En `sensor_manager.h` (sin cambios):

```c
#ifndef SENSOR_MANAGER_H
#define SENSOR_MANAGER_H

void sensor_manager_init(void);

#endif // SENSOR_MANAGER_H
```

---

### **Conclusión**

- **Mantener `sensor_data_t` en `sensor_manager.c`** mejora la organización y seguridad 
  del código.  
- Si en el futuro necesitas que otros módulos accedan a esta estructura, puedes 
  moverla a `sensor_manager.h` sin problemas.

---