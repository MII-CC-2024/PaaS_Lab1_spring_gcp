# PaaS con Google AppEngine


## Crear Aplicación

Vamos a crear una aplicación Spring Boot usando Spring Initializr

- Utiliza Spring Initializr

    - Project: Maven
    - Language: Java, versión 17
    - Spring Boot: 3.2.4
    - Group: cc.paas.gcp
    - Artifac: holamundo
    - Packaging: Jar
    - Dependencias: Spring Web y Thymeleaf

- Genera el proyecto, guárdalo en local y súbelo y descomprime en tu IDE (Google Cloud Shell Editor)

## Crear un controlador y una vista

Controlador: Fichero HomeController.java en el directorio src/main/java/cc/paas/gcp/holamundo

```
package cc.paas.gcp.holamundo;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;


@Controller
public class HomeController {
    
    @GetMapping("/")
	public String index() {
	    
		return "index";
	}
    
}
```
Vista: fichero index.html en src/main/resource/templates

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">

    <head>
        <title>Hola Mundo</title>
    </head>
    
    <body>
        <h1>Hola Mundo!!</h1>
	
	</body>
</html>

```

## Construye el proyecto

- Compila el proyecto para generar el paquete .jar

```
./mvnw clean package
```

- Comprueba la aplicación de forma local
 
```
./mvnw spring-boot:run

```

Una vez iniciada, clic en el icono de "Web Preview" y selecciona "Preview on port 8080", esto abrirá una nueva pestaña con la aplicación.

## Despliega a AppEngine

Para llevar a cabo el despliegue, será necesario contar con gcloud CLI y la autorización de la terminal desde donde trabajaremos.

### Crea el entorno en App Engine

```
gcloud app create --project=cc-2024
You are creating an app for project [cc-2024].
WARNING: Creating an App Engine application for a project is irreversible and the region
cannot be changed. More information about regions is at
<https://cloud.google.com/appengine/docs/locations>.

Please choose the region where you want your App Engine application located:

 [1] asia-east1    (supports standard and flexible)
 [2] asia-east2    (supports standard and flexible and search_api)
 [3] asia-northeast1 (supports standard and flexible and search_api)
 [4] asia-northeast2 (supports standard and flexible and search_api)
 [5] asia-northeast3 (supports standard and flexible and search_api)
 [6] asia-south1   (supports standard and flexible and search_api)
 [7] asia-southeast1 (supports standard and flexible)
 [8] asia-southeast2 (supports standard and flexible and search_api)
 [9] australia-southeast1 (supports standard and flexible and search_api)
 [10] europe-central2 (supports standard and flexible)
 [11] europe-west   (supports standard and flexible and search_api)
 [12] europe-west2  (supports standard and flexible and search_api)
 [13] europe-west3  (supports standard and flexible and search_api)
 [14] europe-west6  (supports standard and flexible and search_api)
 [15] northamerica-northeast1 (supports standard and flexible and search_api)
 [16] southamerica-east1 (supports standard and flexible and search_api)
 [17] us-central    (supports standard and flexible and search_api)
 [18] us-east1      (supports standard and flexible and search_api)
 [19] us-east4      (supports standard and flexible and search_api)
 [20] us-west1      (supports standard and flexible)
 [21] us-west2      (supports standard and flexible and search_api)
 [22] us-west3      (supports standard and flexible and search_api)
 [23] us-west4      (supports standard and flexible and search_api)
 [24] cancel
Please enter your numeric choice: 10


Creating App Engine application in project [cc-2024] and region [europe-central2]....done.                                                                                          
Success! The app is now created. Please use `gcloud app deploy` to deploy your first app.
```

### Crea el fichero de configuración para el despliegue: app.yaml
https://cloud.google.com/appengine/docs/standard/reference/app-yaml?hl=es-419&tab=java

En el directorio src/main creamos el directorio appengine y dentro el fichero app.yaml con el siguiente contenido:

```
runtime: java17
service: default

instance_class: F1

```

Desplegamos la app a producción:

```
$  gcloud app deploy

Services to deploy:

descriptor:                  [/home/alvarez_uhu_es/holamundo/pom.xml]
source:                      [/home/alvarez_uhu_es/holamundo]
target project:              [cc-2024]
target service:              [default]
target version:              [20240418t101211]
target url:                  [https://cc-2024.ue.r.appspot.com]
target service account:      [cc-2024@appspot.gserviceaccount.com]


Do you want to continue (Y/n)?  Y

Beginning deployment of service [default]...
Uploading 2 files to Google Cloud Storage
50%
100%
100%
File upload done.
Updating service [default]...done.                                                                                                                                                                                     
Setting traffic split for service [default]...done.                                                                                                                                                                    
Deployed service [default] to [https://cc-2024.ue.r.appspot.com]

You can stream logs from the command line by running:
  $ gcloud app logs tail -s default

To view your application in the web browser run:
  $ gcloud app browse -s default

```
### Crear una nueva versión 


```
$ gcloud app deploy --version=2
Services to deploy:

descriptor:                  [/home/alvarez_uhu_es/holamundo/pom.xml]
source:                      [/home/alvarez_uhu_es/holamundo]
target project:              [cc-2024]
target service:              [default]
target version:              [2]
target url:                  [https://cc-2024.ue.r.acc-2024ppspot.com]
target service account:      [@appspot.gserviceaccount.com]


Do you want to continue (Y/n)?  
...
```
En la consola web podemos ver las versiones existentes y dividir el tráfico entre ellas si fuese necesario.

### Crear un nuevo servicio

Un nuevo servicio sería otra aplicación implementada el cualquier lenguaje, solo necesitamos indicar en el fichero app.yaml el nombre del servicio:

En el directorio src/main creamos el directorio appengine y dentro el fichero app.yaml con el siguiente contenido:

```
runtime: java17
service: servicio2

instance_class: F1

```

En la consola web podemos ver los servicios desplegados y sus URLs.

