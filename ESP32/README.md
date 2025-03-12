
---

### Verificación del entorno ESP-IDF y compilación de prueba en ESP32

1. **Reinstalar ESP-IDF si `export.sh` falla**  
   ```bash
   $HOME/esp/esp-idf/install.sh
   . $HOME/esp/esp-idf/export.sh
   idf_tools.py install-python-env
   ```

2. **Verificar la instalación con un proyecto de prueba (`hello_world`)**  
   ```bash
   cd ~/esp
   cp -r $IDF_PATH/examples/get-started/hello_world test_project
   cd test_project
   idf.py set-target esp32
   idf.py build
   ```

3. **Flashear y abrir el monitor serie**  
   ```bash
   ls /dev/ttyUSB*  # Verificar puerto
   idf.py -p /dev/ttyUSB0 flash monitor
   ```

4. **Salir del monitor serie**  
   ```bash
   CTRL + ]
   ```
   (Alternativa: `CTRL + T`, luego `Q`)

---
NOTA:
Sí, tienes razón en que VS Code solo ve carpetas dentro del workspace, y no sabe automáticamente cuál es el proyecto activo. La forma en que ESP-IDF maneja esto es a través del CMakeLists.txt y la configuración del proyecto en la terminal.
---
NOTA:
Sí, "ESP-IDF: Pick a Workspace Folder" es la opción correcta para elegir el proyecto activo dentro de VS Code cuando hay más de un proyecto en el workspace.
---

# **Crear un Proyecto ESP32 en ESP-IDF y Importarlo a VS Code**

## **1. Crear el Proyecto desde la Terminal**
1. Abrir una terminal y ejecutar:
   ```bash
   cd ~/Workspace_VSCode_ESP32/
   idf.py create-project wifi_project
   cd wifi_project
   idf.py set-target esp32
   ```
   Esto creará una carpeta llamada `wifi_project` con la estructura base del proyecto.

---

## **2. Abrir VS Code con el Workspace Correcto**
1. Si ya existe un workspace configurado, abrirlo con:
   ```bash
   code ~/Workspace_VSCode_ESP32/esp32_workspace.code-workspace
   ```
2. Si el proyecto no aparece en el explorador de archivos de VS Code:
   - Ir a **File → Add Folder to Workspace…**.
   - Seleccionar la carpeta del proyecto (`wifi_project`).
   - Guardar el workspace en **File → Save Workspace As…**.

---

## **3. Configurar CMake en VS Code**
Si al abrir el proyecto aparece una ventana pidiendo seleccionar `CMakeLists.txt`:
1. Abrir la Paleta de Comandos con `Ctrl + Shift + P`.
2. Ejecutar:
   ```
   ESP-IDF: Select a CMakeLists.txt file
   ```
3. Seleccionar:
   ```
   ~/Workspace_VSCode_ESP32/wifi_project/CMakeLists.txt
   ```
4. Para evitar que lo vuelva a pedir en futuras sesiones, abrir **Settings** y buscar:
   ```
   cmake.preferredGenerators
   ```
   Configurarlo con:
   ```json
   {
       "cmake.preferredGenerators": ["Ninja"]
   }
   ```

---

## **4. Compilar, Flashear y Monitorear el Proyecto**
1. Para compilar el código:
   ```bash
   idf.py build
   ```
2. Para verificar el puerto serie donde está conectado el ESP32:
   ```bash
   ls /dev/ttyUSB*
   ```
3. Para flashear el ESP32 y abrir el monitor serie:
   ```bash
   idf.py -p /dev/ttyUSB0 flash monitor
   ```
4. Para salir del monitor serie:
   ```bash
   CTRL + ]
   ```

---

## **Notas Adicionales**
- Si hay varios proyectos en el workspace, **VS Code pedirá seleccionar el archivo `CMakeLists.txt` cada vez que se abra el workspace**.
- Para cambiar de proyecto en una sesión abierta:
  - `Ctrl + Shift + P`
  - Ejecutar `ESP-IDF: Select a CMakeLists.txt file`
  - Elegir el proyecto actual
- Para abrir directamente un solo proyecto sin pasar por el workspace:
   ```bash
   code ~/Workspace_VSCode_ESP32/wifi_project
   ```

Esto asegura que el entorno esté correctamente configurado y que no haya problemas al compilar y flashear el ESP32.




 1 Seleccionar el proyecto
 F1 : Pick 
 
 2 export ESP-IDF
 . $HOME/esp/esp-idf/export.sh



Aquí tienes el procedimiento actualizado para **crear un proyecto basado en el ejemplo más básico (`get-started/sample_project`)** y luego importarlo en **VS Code**.

---

# **📌 Crear un Proyecto ESP-IDF desde Terminal y Importarlo en VS Code**
Este procedimiento te permitirá iniciar correctamente un proyecto en **ESP-IDF** sin problemas con CMake.

---

## **1️⃣ Abrir una Terminal y Configurar ESP-IDF**
Antes de hacer cualquier cosa, **importa ESP-IDF en la terminal**:

```bash
. $HOME/esp/esp-idf/export.sh
```

Esto configurará las variables necesarias para que `idf.py` y CMake funcionen correctamente.

---

## **2️⃣ Ubicarte en la Carpeta de Trabajo**
Cambia al directorio donde guardarás los proyectos:
```bash
cd ~/Workspace_VSCode_ESP32/
```

Si este directorio no existe, créalo con:
```bash
mkdir -p ~/Workspace_VSCode_ESP32/
cd ~/Workspace_VSCode_ESP32/
```

---

## **3️⃣ Copiar el Proyecto de Ejemplo**
En lugar de `idf.py create-project`, copia el **proyecto base** desde los ejemplos de ESP-IDF:
```bash
cp -r $IDF_PATH/examples/get-started/sample_project ./my_project
```
Esto creará una nueva carpeta `my_project` con todos los archivos necesarios.

---

## **4️⃣ Configurar el Proyecto**
Ahora entra en la carpeta del proyecto:
```bash
cd my_project
```

Configura el target del ESP32:
```bash
idf.py set-target esp32
```

Ejecuta el menú de configuración (opcional pero recomendado):
```bash
idf.py menuconfig
```
Asegúrate de que el puerto serie y la configuración sean correctos.

---

## **5️⃣ Compilar el Proyecto**
Para verificar que todo está bien:
```bash
idf.py build
```
Si no hay errores, el entorno está listo.

---

## **6️⃣ Abrir en VS Code**
Desde la terminal, abre VS Code directamente en la carpeta del proyecto:
```bash
code .
```
O abre **VS Code manualmente** y usa la opción:
```
ESP-IDF: Pick a Workspace Folder
```
Selecciona la carpeta `my_project`.

---

## **7️⃣ Configurar VS Code (Opcional)**
Si quieres asegurarte de que **IntelliSense funcione correctamente**, revisa que el archivo `.vscode/c_cpp_properties.json` contenga:
```json
{
	"configurations": [
	  {
		"name": "ESP-IDF",
		"cStandard": "c11",
		"cppStandard": "c++17",
		"includePath": [
		  "${config:idf.espIdfPath}/components/**",
		  "${workspaceFolder}/**"
		],
		"browse": {
		  "path": [
			"${config:idf.espIdfPath}/components",
			"${workspaceFolder}"
		  ],
		  "limitSymbolsToIncludedHeaders": false
		},
		"compilerPath": "/home/asb/.espressif/tools/xtensa-esp32-elf/esp-12.2.0_20230208/xtensa-esp32-elf/bin/xtensa-esp32-elf-gcc"
	  }
	],
	"version": 4
}
```

---

## **📌 Resumen del Procedimiento**
1️⃣ **Importar ESP-IDF**  
   ```bash
   . $HOME/esp/esp-idf/export.sh
   ```
2️⃣ **Ubicarte en la carpeta de trabajo**  
   ```bash
   cd ~/Workspace_VSCode_ESP32/
   ```
3️⃣ **Copiar el proyecto base**  
   ```bash
   cp -r $IDF_PATH/examples/get-started/sample_project ./my_project
   ```
4️⃣ **Configurar y compilar el proyecto**  
   ```bash
   cd my_project
   idf.py set-target esp32
   idf.py menuconfig
   idf.py build
   ```
5️⃣ **Abrirlo en VS Code**  
   ```bash
   code .
   ```
6️⃣ **Seleccionar la carpeta en VS Code** usando `ESP-IDF: Pick a Workspace Folder` si es necesario.

---

Este procedimiento debería funcionar **sin problemas** y evitar los errores de CMake. 🚀

Si necesitas que lo optimicemos más, dime cómo lo ajustamos. 🎯


### **📌 Ajustes Adicionales al Procedimiento**
✅ **Abrir VS Code en el workspace específico**.  
✅ **Renombrar correctamente el proyecto después de copiarlo**.

---

## **1️⃣ Abrir VS Code en el Workspace Específico**
En lugar de abrir solo el proyecto, podemos abrir **todo el workspace** desde la terminal:

```bash
code ~/Workspace_VSCode_ESP32/esp32_workspace.code-workspace
```
Si no tienes un **workspace** guardado, puedes crear uno en VS Code con:
1. Abre **VS Code**.
2. Ve a **"File" > "Save Workspace As..."**.
3. Guarda el workspace en `~/Workspace_VSCode_ESP32/esp32_workspace.code-workspace`.

Después, cada vez que quieras trabajar con ESP32, solo usa:
```bash
code ~/Workspace_VSCode_ESP32/esp32_workspace.code-workspace
```

---

## **2️⃣ Renombrar el Proyecto Después de Copiarlo**
Si ya copiaste el proyecto con el nombre `my_project`, pero quieres cambiarlo a `wifi_project`, sigue estos pasos:

### **📌 Renombrar la Carpeta**
Primero, renombra la carpeta del proyecto:
```bash
mv ~/Workspace_VSCode_ESP32/my_project ~/Workspace_VSCode_ESP32/wifi_project
```

### **📌 Ajustar el Nombre en `CMakeLists.txt`**
Abre el archivo `CMakeLists.txt` dentro del proyecto y cambia:
```cmake
project(my_project)
```
Por:
```cmake
project(wifi_project)
```

### **📌 Ajustar el Nombre en `.vscode/settings.json` (si existe)**
Si ya configuraste el proyecto en VS Code, edita `settings.json` y asegúrate de que no haga referencia al nombre antiguo.

---

## **3️⃣ Abrirlo en VS Code con Workspace**
Si el proyecto **ya está agregado al workspace**, puedes abrir **todo el entorno** con:
```bash
code ~/Workspace_VSCode_ESP32/esp32_workspace.code-workspace
```
Si **no está agregado al workspace**, en VS Code:
1. **"File" > "Add Folder to Workspace..."**.
2. Selecciona `~/Workspace_VSCode_ESP32/wifi_project`.
3. Guarda el workspace con **"Save Workspace As..."**.

---

## **📌 Resumen del Ajuste**
1. **Renombrar la carpeta del proyecto**:
   ```bash
   mv ~/Workspace_VSCode_ESP32/my_project ~/Workspace_VSCode_ESP32/wifi_project
   ```
2. **Cambiar el nombre en `CMakeLists.txt`**:
   ```cmake
   project(wifi_project)
   ```
3. **Abrir todo el workspace en VS Code**:
   ```bash
   code ~/Workspace_VSCode_ESP32/esp32_workspace.code-workspace
   ```
4. **Agregar el proyecto al workspace en VS Code** si no está.

---

### **✅ Ahora el proyecto estará correctamente renombrado y funcionando dentro del workspace en VS Code.** 🚀

Si hay algo más que quieras ajustar, dime. 🎯




















