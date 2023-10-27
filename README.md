<div align="center">
    <img src="https://centrodeconocimiento.agesic.gub.uy/o/agesic-cci-theme-nuevo/images/logo_presidencia.png" alt="logo" width="200" height="auto" /></br>
    <img src="https://centrodeconocimiento.agesic.gub.uy/o/agesic-cci-theme-nuevo/images/logo-agesic.svg" alt="logo" width="200" height="auto" />
    <h1>Procesos publicación PDI</h1>
	
</div>

<!-- Table of Contents -->
# Tabla de contenido
- [Sobre el repositorio](#sobre-el-repositorio)
  * [Tecnologías](#tecnologías)
- [Como empezar](#como-empezar)
  * [Prerrequisitos](#prerrequisitos)
- [Proceso de carga en el catálogo de datos](#Proceso-de-carga-en-el-catálogo-de-datos)
  * [Lectura de fuente de datos](#Lectura-de-fuente-de-datos)
  * [Generación de archivos](#Generación-de-archivos)
  * [Publicación en el catálogo](#Publicación-en-el-catálogo)
 
## Sobre el repositorio
La solución contiene los archivos necesarios para la creación de procesos en base a un template genérico definido por Agesic con el fin de publicar conjuntos de datos en el Catálogo de Datos Abiertos. Permite la extracción de datos de diferentes fuentes como pueden ser bases de datos o archivos planos. 

### Tecnologías

- JAVA
- Pentaho Data Integration
- Javascript



## Como empezar

### Prerrequisitos
- Usuario en el catálogo de datos
- Pentaho Data Integration
- Configuración de archivo xml y archivos jar necesarios para publicación mediante API en el catálogo de datos abiertos: 
en las carpetas **data-integration\plugins\pdi-pur-plugin\lib** y **data-integration\lib**  validar que estén los archivos **httpmime-4.3.jar**, **httpcore-4.4.11.jar** y **httpclient-4.5.9.jar**.
En la carpeta **data-integration\plugins\pdi-pur-plugin** y editar el archivo **plugin.xml** agregando la siguiente sección, el archivo debe quedar como muestra la siguiente imagen y guardar el cambio.

```
 <localized_name>
    <name locale="en_US">Pentaho Repository</name>
  </localized_name>

  <localized_description>
    <description locale="en_US">Pentaho Repository</description>
  </localized_description>
  
  <libraries>
    <library name="lib/httpmime-4.3.jar" />
  </libraries>

```


## Proceso de carga en el catálogo de datos

El proceso de carga consta de 3 pasos principales, lectura de fuente de datos, generación de archivos y publicación en el catálogo de datos.



![](/PDI/print_1.png)




### Lectura de fuente de datos

En este paso se realiza la lectura de la fuente de datos. Pentaho Data Integration nos permite consultar distintas fuentes de datos, por ejemplo: csv, txt, xml, json, bases de datos en general y generar un archivo xlsx.


![](/PDI/print_2.png)



La fuente de datos de nuestro ejemplo se encuentra en un formato CSV y contiene los siguientes datos:


![](/PDI/print_3.png)


En este paso vamos a generar un archivo XLSX que va a ser el input para el siguiente paso. Este archivo utiliza como template el archivo **planilla_base.xlsx**. El archivo planilla base tiene 2 pestañas, la primera corresponde al los datos y la segunda pestaña corresponde a los metadatos.  En la pestaña datos se encuentran los cabezales con los nombres de los campos, por lo tanto es importante que el orden de los campos tanto en el input como en el output coincida con el ordén en la planilla base. La pestaña metadatos contiene la descripción de los atributos, el tipo de dato e información adicional que se quiera añadir con respecto al atributo. Desde la pestaña metadatos se va a generar el archivo JSON a publicar.


**Input**

![](/PDI/print_4.png)


**Output**

![](/PDI/print_5.png)

**Planilla base**

![](/PDI/print_5_2.png)

![](/PDI/print_13.png)


Una vez finalizado este paso deberíamos tener un archvio en la carpeta **\Archivos_a_publicar** un archivo xlsx (ejemplo.xlsx) con 2 pestañas, una con los datos y otra con los metadatos.

### Generación de archivos

En este paso se generan los archivos en formato xml, csv y json que van a ser publicados en el catálogo de datos.


![](/PDI/print_6.png)



El primer paso es **Seteo variables** donde se definen las variables a utilizar en los siguientes pasos. En la generación de los archivos json, xml y csv se utiliza como fuente de dataos el archivo **ejemplo.xlsx**, el archivo json va a utilizar la pestaña metadatos y los pasos csv y xml van a utilizar la pestaña datos. 

Los tres pasos (json, csv y xml) son similares. Primero se carga la información que se encuentra el archivo **ejemplo.xlsx**, luego en caso de que sea necesario se realizan las modificaciones necesarias y finalmente se crea el archivo a publicar en el catalogo de datos abiertos. 

A modo de ejemplo en la siguiente imagen se puede visualizar el proceso de creación del archivo XML.

![](/PDI/print_7.png)

Una vez que finalizada la ejecución de los 3 pasos los archivos generado van a quedar almacenados en la carpeta Archivos a **..Publicar \Otros_Formatos.**
 
![](/PDI/print_8.png)



El archivo **XML** generado para nuestro caso de ejemplo es el que podemos observar en la siguiente imagén.



![](/PDI/print_9.png)


### Publicación en el catálogo.

Hay 1 job para cada formato de archivo. 

![](/PDI/print_10.png)

En esta etapa del proceso se realiza la publicación de los archivos generados en pasos anteriores.  La publicación consta de 2 pasos en el primero se cargan las variables y luego la publicación en el catalogo de cada uno de los archivos. Los 4 jobs tienen la misma estructura con la diferencia de la fuente de datos a utilizar.
A modo de ejemplo en la siguiente imagen se puede visualizar el proceso de publicación del archivo XML.

![](/PDI/print_11.png)

En el primer paso se setean las variables que se detallan en la siguiente tabla.

|     Variable             | Descripción |
| -------------------   | ----  |
|      **package_descripcion**             | Descricpción del conjunto |
|      **resource_nombre_metadatos**       | Nombre del archivo JSON de metadatos |
|      **resource_desc_metadatos**             | Descricpción del archivo JSON de metadatos |
|      **ruta_csv_datos**             | Ruta donde se encuentra almacenado el archivo origen  |
|      **archivo_datos_meta**             | Ruta donde se encuentra almacenado el archivo de metadatos a publicar |
|      **package_name**             | Nombre del conjuto de datos |
|      **url**             | Url del catalogo de datos abiertos |
|      **recursoDescripcion**             | Descripción del recurso en el catalogo de datos abiertos |
|      **rutaArchivo**             | Ruta donde se encuentra almacenado el archivo de metadatos a publicar. Esta ruta se utiliza en caso de estar en un entrono linux.|
|      **apiKey**             | API key de CKAN |
|      **accion**             | Acción a realizar, ej. update |
|      **cobertura_temporal**             | Periodo de tiempo al que refieren los datos |




El paso siguiente al seteo de variables es la publicación del archivo, a modo de ejemplo agregamos un extracto de la clase java donde se setean las variables necesarias para la publicación de nuevo recurso en el catálogo. 

``` 
	String urlString = get(Fields.In, "url").getString(r);
	String recursoNombre = "Datos de Ejemplo";
	String recursoDescripcion = "Ejemplo";
	String packageName = get(Fields.In, "package_name").getString(r);
	String rutaArchivo = get(Fields.In, "rutaArchivo").getString(r) + "\\ejemplo.xlsx";
	String apiKey = get(Fields.In, "apiKey").getString(r);
	String FrecuenciaDeActualizacion = "Trimestral";
	String CoberturaEspacial = "Uruguay";
	String CoberturaTemporal = get(Fields.In, "cobertura_temporal").getString(r);
	String Licencia = "Licencia de DAG de Uruguay";
	String accion = get(Fields.In, "accion").getString(r);	
	String ResourceId = "ID del Recurso en el Catalogo de Datos";     
```



```
	//cuerpo del post, en esta parte se ponen los parametros ej:nombre
		entity.addTextBody("package_id", packageName);
   		entity.addTextBody("description", recursoDescripcion, ContentType.create("text/plain", MIME.UTF8_CHARSET));
		entity.addTextBody("name", recursoNombre, ContentType.create("text/plain", MIME.UTF8_CHARSET));     
		entity.addTextBody("update_frecuency",FrecuenciaDeActualizacion, ContentType.create("text/plain", MIME.UTF8_CHARSET));
		entity.addTextBody("spatial_coverage", CoberturaEspacial, ContentType.create("text/plain", MIME.UTF8_CHARSET));
		entity.addTextBody("temporal_coverage", CoberturaTemporal, ContentType.create("text/plain", MIME.UTF8_CHARSET));
		entity.addTextBody("licence", Licencia, ContentType.create("text/plain", MIME.UTF8_CHARSET));
		entity.addTextBody("format", "xml"); 
 		entity.addBinaryBody("upload", file,ContentType.APPLICATION_OCTET_STREAM, file.getName());
		if(accion.equals("update")){
			entity.addTextBody("id", ResourceId);
		}

```


