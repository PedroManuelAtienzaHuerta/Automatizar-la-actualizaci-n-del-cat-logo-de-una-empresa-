# Automatizar-la-actualización-del-catálogo-de-una-empresa-

En una simulación de una situación real, en una frutería online, se necesitaba desarrollar un **sistema que actualizara la información del catálogo** con los datos proporcionados por sus proveedores.

Los proveedores envían los **datos como imágenes grandes con una descripción asociada** de los productos en dos archivos (.tiff para la imagen y .txt para la descripción). 

Es necesario convertir las imágenes en **jpeg** más pequeños y el texto en un **archivo HTML** que muestre la imagen y la descripción del producto. 

El contenido del archivo HTML debe cargarse en un **servicio web** que ya esté funcionando con Django. 

También es necesario recoger el **nombre y el peso** de todas las frutas de los archivos .txt y utilizar una **petición Python** para subirlo a su servidor Django.

Crearemos un script de Python que **procese** las imágenes y las descripciones y luego **actualice** el sitio web de la empresa para añadir los nuevos productos.

Una vez completada la tarea, se notifica al proveedor con un **correo electrónico** que indique el peso total de la fruta (en libras) que se había subido. 

El **correo electrónico tiene adjunto un PDF** con el nombre de la fruta y su peso total (en lbs).

Por último, paralelamente al funcionamiento de la automatización, queremos **comprobar la salud del sistema y enviar un correo electrónico si algo va mal.**

# Datos de los Proveedores 

Los proveedores nos envían un archivo con un directorio llamado **"supplier-data"**, que contiene subdirectorios llamados **"images"** y **"descriptions".**

Liste el contenido del directorio supplier-data utilizando el siguiente comando:
```
ls ~/supplier/data
```

Salida:
```
images descriptions
```
El subdirectorio **"images"** contiene imágenes de varias frutas, mientras que el subdirectorio **"descriptions"** tiene ficheros de texto que contienen la descripción de cada fruta. 

Puede echar un vistazo a cualquiera de estos archivos de texto utilizando el comando cat:
```
cat ~/supplier-data/descriptions 
```
Salida:
```
Mango
115 libs
```

# Trabajar con imágenes de proveedores:

En esta sección, escribiremos un script en Python llamado **changeImage.py** para procesar las imágenes de los proveedores. 

Usaremos la **biblioteca PIL** para actualizar todas las imágenes del directorio ~/supplier-data/images/ con las siguientes especificaciones:

- **Tamaño:** Cambiar la resolución de la imagen de 3000x2000 a 600x400 píxeles

- **Formato:** Cambiar el formato de imagen de .tiff a .jpeg 

El subdirectorio "images" contiene capas de transparencia alfa.

Por lo tanto, es mejor **convertir primero el formato RGBA de 4 canales a RGB de 3 canales** antes de procesar las imágenes. 

Utilice el método **convert("RGB")** para convertir la imagen RGBA a RGB.

Tras procesar las imágenes, se guardan en la misma ruta ~/supplier-data/images/, pero con extensión.jpg

Cree y abra el archivo con el **editor nano.**
```
 nano ~/changeImage.py
 ```
El archivo sería el siguiente:
```python

#!/usr/bin/env python3

from PIL import Image
import os

path = "./supplier-data/images/"
for f in os.listdir("./supplier-data/images"):
      if f.endswith(".tiff"):
           split_f = f.split(".")
           name = split_f[0] + ".jpeg"
           im = Image.open(path + f).convert("RGB")
           im.resize((600, 400)).save("./supplier-data/images/" + name, "JPEG")
```
Una vez que haya terminado de editar el script changeImage.py, **guarde el archivo pulsando Ctrl+o, tecla Intro y Ctrl+x.**

Conceda **permisos de ejecución** al script changeImage.py:
```
sudo chmod +x ~/changeImage.py
```
Ahora, ejecute el script changeImage.py:
```
./changeImage.py
```

# Cargando imágenes al servidor web

Usted ha modificado las imágenes de frutas a través del script changeImage.py. 

Ahora, tendrás que **subir estas imágenes modificadas al servidor web** que está manejando el catálogo de frutas. 

Para ello, tendrás que utilizar el módulo de Python **requests** para enviar el contenido del fichero a la URL [external-IP-address]/upload.

Vas a escribir un script llamado **supplier_image_upload.py** que tome las imágenes jpeg del directorio **supplier-data/images/** que has procesado previamente y las suba al catálogo de frutas del servidor web.

Utilice el **editor nano** para crear un archivo llamado supplier_image_upload.py:
```
nano ~/supplier_image_upload.py
```

El script completo sería el siguiente:

```python

#!/usr/bin/env python3
import requests
import os

# Este ejemplo muestra como un archivo puede ser cargado usando el módulo requests de Python 

url = "http://localhost/upload/"
for f in os.listdir("./supplier-data/images"):
    if f.endswith(".jpeg"):
        with open('./supplier-data/images/' + f, 'rb') as opened:
            r = requests.post(url, files={'file': opened})



```


Una vez que haya terminado de editar el script, **guarde el archivo** pulsando Ctrl+o, tecla Intro y Ctrl+x.

Conceda **permisos de ejecución** al script supplier_image_upload.py:
```
sudo chmod +x ~/supplier_image_upload.py
```

Ahora, ejecute el script supplier_image_upload.py:
```
./supplier_image_upload.py
```

**Visita la url externa** y deberías ver todas las imágenes subidas con éxito.

# Cargando las descripciones 

Para añadir imágenes de frutas y sus descripciones desde el proveedor en el servidor web del catálogo de frutas, **cree un nuevo script en Python que automáticamente POSTEARÁ** las imágenes de frutas y sus respectivas descripciones en formato JSON.

Escriba un script Python llamado **run.py** para procesar los archivos de texto(001.txt,003.txt...) del directorio **supplier-data/descriptions/**

El script debe **convertir los datos en un diccionario JSON** añadiendo todos los campos necesarios, incluida la imagen asociada a la fruta (nombre_imagen), y **cargándolo** en http://[external-IP-address]/fruits utilizando la biblioteca de peticiones de Python.

A continuación tendrás que **procesar los archivos .txt (llamados001.txt, 002.txt, ...) en el directorio supplier-data/descriptions/** y guardarlos en una estructura de datos para que luego puedas subirlos vía JSON.

Ten en cuenta que todos **los archivos se escriben en el siguiente formato**, con cada dato en su propia línea:

- Nombre

- Peso (en libras)

- Descripción

El **Modelo de datos en la aplicación Django fruit** tiene los siguientes campos: 
- Name
- Weight
- Description
- Image_name.

El campo **weight** está definido como un campo entero.

Así que cuando proceses la información del peso de la fruta desde el archivo .txt, necesitas convertirlo en un entero. 

Eliminar "lbs" y convertir "500" en un número entero.

El campo **image_name** permitirá al sistema encontrar la imagen asociada a la fruta. 
¡No olvide añadir todos los campos, incluido image_name!

El **objeto JSON final** debería ser similar a:
```
{"name": "Watermelon", "weight": 500, "description": "Watermelon is good for relieving heat, eliminating annoyance and quenching thirst. It contains a lot of water, which is good for relieving the symptoms of acute fever immediately. The sugar and salt contained in watermelon can diuretic and eliminate kidney inflammation. Watermelon also contains substances that can lower blood pressure.", "image_name": "010.jpeg"}
```

**Iterar sobre todas las frutas y utilizar el método POST** de la librería de peticiones Python para subir todos los datos a la URL http://[external-IP-address]/fruits.

Cree run.py utilizando el **editor nano:**
```
nano ~/run.py
```
El archivo completo sería el siguiente:

```python
#!/usr/bin/env python3

import os
import requests

# Crear un diccionario de frutas que contenga como claves: name, weight, description e image_name

fruits = {}
keys = ["name", "weight", "description", "image_name"]
index = 0
path = "./supplier-data/descriptions/"
img_path = "./supplier-data/images/"

for file in os.listdir(path):
    with open(path + file) as f:
        for ln in f:
            line = ln.strip()
            if "lbs" in line:
                nline = line.split()
                wght = int(nline[0])
                fruits["weight"] = wght
                index += 1
            else:
                try:
                    fruits[keys[index]] = line
                    index += 1
                except:
                    fruits[keys[2]] = line
    
    index = 0
    split_f = file.split(".")
    name = split_f[0] + ".jpeg"
    
    for fle in os.listdir(img_path):
        if fle == name:
            fruits["image_name"] = name
            response = requests.post("http://<External_IP>/fruits/", json=fruits)
    
    fruits.clear()

```


A continuación, **guarde el archivo** pulsando Ctrl+o, tecla Intro y Ctrl+x.

Conceda **permisos de ejecución** al script run.py:
```
sudo chmod +x ~/run.py
```

Ahora, ejecute el script run.py:
```
./run.py
```

Para comprobar los resultados, vuelva a visitar la URL externa.


# Genera un informe en PDF y envíalo por correo electrónico

Una vez subidos los archivos images y descriptions al servidor web de la frutería, tendrás que **generar un archivo PDF para enviárselo al proveedor**, indicando que los datos se han procesado correctamente. 

Para generar informes PDF, puede utilizar la biblioteca **ReportLab.**

El contenido del informe debería tener este aspecto:
```

Actualización procesada en <fecha de hoy>

[línea en blanco]

nombre: Manzana

peso: 500 lbs

[línea en blanco]

nombre: Aguacate
```
Ahora crearemos un script llamado **reports.py** para crear un informe pdf a los proveedores.

Usando la librería Python reportlab, definiremos el método **generate_report** para construir los informes PDF.

 El Script Python completo sería el siguiente:
 ```python

#!/usr/bin/env python3

from reportlab.platypus import SimpleDocTemplate
from reportlab.platypus import Paragraph, Spacer, Table, Image
from reportlab.lib.styles import getSampleStyleSheet
from reportlab.lib import colors


def generate_report(attachment, title, pharagraph):
    styles = getSampleStyleSheet()
    report = SimpleDocTemplate(attachment)
    report.build([report_title, empty_line, report_info])
    report_title = Paragraph(title, styles["h1"])
    report_info = Paragraph(pharagraph, styles["BodyText"])
    table_style = [('GRID', (0, 0), (-1, -1), 1, colors.black),
                   ('FONTNAME', (0, 0), (-1, 0), 'Helvetica-Bold'),


                   ('ALIGN', (0, 0), (-1, -1), 'CENTER')]


    empty_line = Spacer(1, 20)
    report.build([report_title, empty_line, report_info])
```



Una vez que haya terminado de editar el script reports.py, **guarde el archivo** escribiendo Ctrl+o, Tecla intro y Ctrl+x.

Cree otro script llamado **report_email**










































