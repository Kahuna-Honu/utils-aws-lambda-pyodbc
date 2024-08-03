# Python 3.9 con pyodbc y MSSQL

Este repositorio contiene un Dockerfile para construir una imagen Docker que instala las dependencias requeridas para instalar y compilar pyodbc, finalmente copia los compilados a un archivo ZIP, que se utilizará para crear un layer para una Lambda. A continuación, se detallan los pasos para construir y utilizar el layer en AWS Lambda.

## Archivos en el Repositorio

- **Dockerfile**: Archivo de configuración para construir la imagen Docker.
- **layer.zip**: Archivo ZIP generado que contiene el layer para Lambda (este archivo será generado durante el proceso de construcción).

## Instrucciones

### 1. Construir la Imagen Docker

Compila la imagen Docker utilizando el Dockerfile incluido en el directorio actual. Ejecuta el siguiente comando:

```powershell
docker build -t pyodbc-mssql .
```


### 2. Extraer el Archivo ZIP del Layer
Una vez que la imagen Docker ha sido construida, corre el contenedor para copiar el archivo layer.zip generado. Ejecuta el siguiente comando:

```powershell
docker run --rm --entrypoint bash -v "${PWD}:/local" pyodbc-mssql -c "cp /layer.zip /local/"
```
Esto copiará layer.zip al directorio actual en tu máquina local.

### 3. Crear un Layer en AWS Lambda
    1. Ve a la Lambda.
    2. En el panel de navegación, selecciona Layers.
    3. Haz clic en Create layer.
    4. Introduce un nombre para el layer.
    5. Selecciona Upload a .zip file y carga el archivo layer.zip que acabas de extraer.
    6. Configura el runtime del layer como Python 3.9
    7. Asegúrate de que la arquitectura esté configurada como x86/64.

### 4. Asignar el Layer a tu Función Lambda
En la consola de AWS Lambda, selecciona la función a la que deseas agregar el layer.
En la sección Layers, haz clic en Add a layer.
Selecciona Custom layers y elige el layer que creaste en el paso anterior.
Guarda los cambios.

### 5. PROFIT
Tu función Lambda ahora puede utilizar pyodbc para conectarse a MSSQL Server 18.
```python
import pyodbc

...
```

_Nota_:
_En la raíz de este proyecto se encuentra una compilación del mismo, que puede ser útil como referencia o utilización directa._
_Puedes editar el Dockerfile para cambiar las versiones del driver de MSSQL o de Python._