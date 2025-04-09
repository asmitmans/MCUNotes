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

---
