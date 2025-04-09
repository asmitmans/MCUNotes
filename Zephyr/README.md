# 1. ¿Qué es Zephyr RTOS?

Zephyr es un sistema operativo en tiempo real (**RTOS**) para sistemas embebidos,
desarrollado por la **Linux Foundation** y mantenido por una comunidad de empresas 
y colaboradores.

---

## Características principales:

| Característica             | Detalle                                                                 |
|---------------------------|-------------------------------------------------------------------------|
| **RTOS preemptivo**    | Soporta múltiples hilos, prioridades, semáforos, colas, etc.            |
| **Soporte multiplataforma** | Desde ARM Cortex-M, RISC-V, Xtensa (ESP32), x86 y más.              |
| **Configuración dinámica** | Todo el sistema se configura mediante `Kconfig` y `Devicetree`.     |
| **Modular**            | Solo compilas lo que usas (ideal para IoT y dispositivos pequeños).     |
| **Seguridad**          | Compatible con PSA de ARM, soporte para particionado de memoria.        |
| **Licencia permisiva** | Apache 2.0                                                               |
| **Soporte de ecosistema** | BLE, USB, LoRa, GPIO, I2C, SPI, WiFi (dependiendo de SoC).          |

---

## Filosofía de Zephyr

Zephyr no es solo un RTOS, es una **plataforma de desarrollo embebido configurable**. 
Piensa en él como un *framework* completo que se adapta al hardware, y no al revés. 
Esto lo hace especialmente útil en proyectos industriales y comerciales de IoT.

---

## Árbol de código

Zephyr se organiza en:

- `boards/`: definición de placas
- `drivers/`: controladores oficiales
- `soc/`: soporte específico por familia de microcontroladores
- `kernel/`: el corazón del sistema operativo
- `include/`: headers públicos
- `samples/`: ejemplos predefinidos
- `tests/`: tests del framework

Todo esto se configura con `west` y los archivos de configuración.

---

# 2. Estructura interna de Zephyr

Zephyr está pensado para escalar en distintos dispositivos. Para lograr esto, 
usa varias capas y herramientas que trabajan juntas. 

## 1. **Kconfig** — Configuración del sistema y subsistemas

**¿Qué es?**

Un sistema de configuración heredado del kernel de Linux que permite seleccionar 
**opciones y módulos** de forma modular. 

**¿Cómo funciona?**

- Los archivos `Kconfig` definen **opciones (booleans, strings, choices)** que 
  pueden habilitarse/deshabilitarse.
- Estas se agrupan jerárquicamente.
- Tu proyecto define qué opciones quiere en `prj.conf` o en `defconfig`.

**Ejemplo típico en `prj.conf`:**
```conf
CONFIG_GPIO=y
CONFIG_MAIN_STACK_SIZE=2048
CONFIG_LOG=y
```

> Zephyr interpreta estas líneas al construir, usando los archivos `Kconfig` 
> definidos en los drivers, SoC, placas, etc.

---

## 2. **Devicetree** — Descripción del hardware

**¿Qué es?**

Es un archivo en formato `.dts` que describe el **hardware disponible** para el 
sistema operativo (memoria, periféricos, pines, etc).

**Jerarquía:**

- Cada SoC y cada placa tienen un archivo base en `dts/`.
- Puedes añadir o sobrescribir nodos mediante un **archivo overlay**.

**Ejemplo (overlay):**
```dts
&i2c0 {
    status = "okay";
    my_sensor@1A {
        compatible = "myvendor,mysensor";
        reg = <0x1A>;
    };
};
```

> Zephyr usa el árbol devicetree **en tiempo de build**, no en tiempo de ejecución. 
> Genera macros como `DT_INST_0_MYVENDOR_MYSENSOR_LABEL` y estructuras.

---

## 3. **Bindings (YAML)** — Cómo interpretar los nodos del Devicetree

**¿Qué son?**

Archivos `.yaml` que definen **qué significa cada `compatible`** y qué propiedades 
tienen.

Ejemplo de un binding:

```yaml
compatible: "myvendor,mysensor"
properties:
  reg:
    type: int
    required: true
  foo-threshold:
    type: int
    default: 100
```

> Estos se procesan junto con el Devicetree para generar macros en los headers 
> de build (`devicetree_generated.h`).

> Zephyr, al compilar, lee el Devicetree, encuentra compatible = "algo,xyz" y 
> dice: “Ok, voy al binding .yaml que describe este tipo de dispositivo y desde 
> ahí sé qué propiedades esperar y cómo generar el código”.

---

## 4. **Overlays** — Devicetree específicos por proyecto

**¿Qué son?**

Archivos `.overlay` que complementan el archivo `.dts` de la placa.

Se usan para:

- Habilitar nodos desactivados (`status = "okay"`)
- Agregar periféricos
- Asociar drivers o sensores

> Un `.overlay` es fundamental cuando estás usando hardware nuevo (como un sensor 
> I2C externo).

---

## 5. **Drivers** — Código que implementa soporte para un dispositivo

- Están en `drivers/`
- Se inicializan con `DEVICE_DT_DEFINE` o `DEVICE_DT_INST_DEFINE`.
- Implementan una API estándar (GPIO, SPI, sensor, etc.)

Los drivers se vinculan al hardware usando el `compatible` del Devicetree y el 
binding YAML correspondiente.

---

## 6. **CMake** — Motor de construcción del proyecto

Zephyr usa **CMake** para estructurar los componentes del proyecto, combinar 
configuraciones y construir todo.

- El archivo raíz es `CMakeLists.txt` (tanto en tu proyecto como en el sistema)
- Inicia el build system con los `Kconfig`, `Devicetree`, etc.

---

## 7. **West** — Herramienta de gestión y build

Zephyr se construye con `west`, una herramienta que:

- Administra múltiples repos (como `zephyr/`, `hal_espressif/`, etc.)
- Llama a CMake con todos los parámetros correctos
- Se encarga del *workspace management*

---

## ¿Qué pasa cuando hago `west build -b esp32`?

1. **CMake** configura el sistema:
   - Lee `prj.conf` y resuelve `Kconfig` (genera `.config`)
   - Lee `.dts` + `.overlay` → Devicetree → Macros
   - Evalúa `CMakeLists.txt` del proyecto y módulos
2. **Devicetree y bindings** generan estructuras en C
3. Se seleccionan solo los drivers/hilos/subsistemas requeridos
4. Se compila con el toolchain adecuado
5. Se genera un `.elf`, `.bin`, `.map`, etc.

---

## Esquema resumido del flujo

```text
               prj.conf     ->   Kconfig
main.c  ---->  CMakeLists  ->   CMake
overlay.dts   ->   Devicetree (.dts + .overlay)
bindings.yaml ->   interpretación del Devicetree

   ==> west build inicia todo
```

---

# Instalación en Ubuntu

## Paso 1: Requisitos del sistema

```bash
sudo apt update && sudo apt install --no-install-recommends \
    git cmake ninja-build gperf \
    ccache dfu-util device-tree-compiler wget \
    python3-dev python3-pip python3-venv \
    gcc g++ make unzip xz-utils file \
    udev
```

---

## Paso 2: Crear entorno virtual Python

```bash
mkdir -p ~/zephyrproject
cd ~/zephyrproject
python3 -m venv venv-zephyr
source venv-zephyr/bin/activate
```

---

## Paso 3: Instalar `west`

```bash
pip install --upgrade pip
pip install west
```

---

## Paso 4: Inicializar el workspace Zephyr

```bash
west init -m https://github.com/zephyrproject-rtos/zephyr --mr main .
west update
west zephyr-export
```

> `--mr main` asegura que traés la rama principal, no una versión antigua.

---

## Paso 5: Instalar Python deps de Zephyr

```bash
pip install -r zephyr/scripts/requirements.txt
```

---

## Paso 6: Instalar toolchains recomendados

### Instalar Zephyr SDK (para todo lo que no sea ESP32)

```bash
cd ~/zephyrproject
wget https://github.com/zephyrproject-rtos/sdk-ng/releases/download/v0.16.4/zephyr-sdk-0.16.4_linux-x86_64.tar.xz
tar xvf zephyr-sdk-0.16.4_linux-x86_64.tar.xz
cd zephyr-sdk-0.16.4
./setup.sh
```
Y agregá a tu ~/.bashrc:
```bash
export ZEPHYR_TOOLCHAIN_VARIANT=zephyr
export ZEPHYR_SDK_INSTALL_DIR=$HOME/zephyrproject/zephyr-sdk-0.16.4
```

### Instalar ESP-IDF para programar ESP32
```bash
cd ~/zephyrproject
mkdir toolchains
cd toolchains

git clone -b v5.1.2 --recursive https://github.com/espressif/esp-idf.git
cd esp-idf
./install.sh
source export.sh
deactivate
cd ~/zephyrproject/toolchains/esp-idf
./tools/idf_tools.py install-python-env
```

> Profesional: separar entornos por target
> Es válido tener:
> - `venv-zephyr` → para placas no ESP32
> - entorno propio de ESP-IDF → para ESP32

---

## Paso 7: Añadir Zephyr al entorno

Añadí estas líneas a tu `~/.bashrc` o `~/.zshrc`:

```bash
export ZEPHYR_BASE=$HOME/zephyrproject/zephyr
source ~/zephyrproject/venv-zephyr/bin/activate
```

Recargá:

```bash
source ~/.bashrc
```

---

## Paso 8: Instalar soporte para ESP32

```bash
cd ~/zephyrproject
source venv-zephyr/bin/activate
west update
```

> Esto activa la HAL de Espressif, que usa el SDK oficial debajo.

---

## Verificación

```bash
west list
```

Deberías ver algo como:
```
zephyr          zephyrproject/zephyr
hal_espressif   zephyrproject/modules/hal/espressif
...
```

Y luego podés probar que compile con:

```bash
cd ~/zephyrproject
source ~/zephyrproject/venv-zephyr/bin/activate
source ~/zephyrproject/toolchains/esp-idf/export.sh
west build -b esp32_devkitc_wroom/esp32/procpu zephyr/samples/hello_world
```
Usás `/esp32/procpu` como “sub-target” para decirle a Zephyr qué core de la placa 
debe preparar.

### ¿Qué se generó?
Tu carpeta build/ ahora contiene:
- zephyr.elf: binario enlazado
- zephyr.bin: imagen binaria para flashear
- zephyr.map: mapa de memoria

Archivos intermedios, headers generados, etc.

### Flashear en tu ESP32

Conectá tu ESP32-WROOM-32 por USB, y usá:

```bash
west flash
```

> Zephyr usará `esptool.py` para detectar el puerto y cargar la imagen.  
> Si necesitás especificar el puerto, podés usar:

```bash
west flash --dev /dev/ttyUSB0
```

(ajustá el puerto según tu sistema)
---

### En desarrollo profesional:
- Nunca deberías trabajar en zephyr/samples/ ni en zephyr/ directamente.
- Lo correcto es crear tu propio directorio de proyecto fuera de zephyr/, para 
  mantener:
  - Separación limpia entre tu código y el framework
  - Portabilidad y control de versiones propios
  - Posibilidad de compartir tu código o mantenerlo en Git sin cargar todo Zephyr

---

# Proyecto Zephyr desde cero: Blink en GPIO2 del ESP32

### Objetivo:
Parpadear un LED conectado al pin GPIO2 del ESP32-WROOM-32, de forma correcta 
según el flujo Zephyr (no hardcodeada).

---

## 1. Estructura de carpetas y archivos

Desde `~/zephyrproject/`:

```bash
mkdir -p blink_esp32/src
touch blink_esp32/CMakeLists.txt
touch blink_esp32/prj.conf
touch blink_esp32/src/main.c
mkdir blink_esp32/boards
touch blink_esp32/boards/esp32_devkitc_wroom.overlay
```

---

## 2. Contenido de archivos

### `CMakeLists.txt`

```cmake
cmake_minimum_required(VERSION 3.20.0)
find_package(Zephyr REQUIRED HINTS $ENV{ZEPHYR_BASE})
project(blink_esp32)
target_sources(app PRIVATE src/main.c)
```

---

### `prj.conf`

```conf
CONFIG_GPIO=y
CONFIG_MAIN_STACK_SIZE=1024
CONFIG_LOG=y
```

---

### `src/main.c`

```c
#include <zephyr/kernel.h>
#include <zephyr/device.h>
#include <zephyr/drivers/gpio.h>
#include <zephyr/logging/log.h>

LOG_MODULE_REGISTER(main);

#define LED_NODE DT_ALIAS(led0)

#if !DT_NODE_HAS_STATUS(LED_NODE, okay)
#error "No se definió led0 en el devicetree overlay"
#endif

static const struct gpio_dt_spec led = GPIO_DT_SPEC_GET(LED_NODE, gpios);

int main(void)
{
    int ret;

    if (!device_is_ready(led.port)) {
        LOG_ERR("GPIO controlador no está listo");
        return 0;
    }

    ret = gpio_pin_configure_dt(&led, GPIO_OUTPUT_ACTIVE);
    if (ret < 0) {
        LOG_ERR("Error configurando pin");
        return 0;
    }

    while (1) {
        gpio_pin_toggle_dt(&led);
        k_msleep(500);
    }

    return 1;
}

```

---

### `boards/esp32_devkitc_wroom.overlay`

```dts
/ {
    leds {
        compatible = "gpio-leds";

        led2: led_2 {
            gpios = <&gpio0 2 GPIO_ACTIVE_HIGH>;
            label = "LED en GPIO2";
        };
    };

    aliases {
        led0 = &led2;
    };
};
```

---

## 3. Compilar y flashear

Desde `~/zephyrproject/`:

```bash
source ~/zephyrproject/venv-zephyr/bin/activate
source ~/zephyrproject/toolchains/esp-idf/export.sh

cd ~/zephyrproject/blink_esp32
west build -b esp32_devkitc_wroom/esp32/procpu
west flash
```

Si necesitás especificar el puerto serie:

```bash
west flash --dev /dev/ttyUSB0
```

---

## Resultado esperado

El pin GPIO2 se activa/desactiva cada 500 ms (LED parpadeando si tenés uno 
conectado ahí, o el led onboard si tu placa lo tiene en GPIO2).

---
