# Herramienta OWASP ZAP 🕷️


OWASP Zed Attack Proxy (También conocido como ZAP) se trata de un proxy-interceptro y escáner de seguridad web open source mantenido por OWASP. Esta herramienta está creada tanto para la exploración manual como para automatización (scripts, API, CI). Es capaz de detectar multitud de problemas de seguridad web como son las inyecciones, XSS, problemas con las cookies y encabezados, etc... Además, provee de herramientas como spidering, active scanning, interceptación de tráfico, fuzzing y una API REST.

## ¿Qué encontraremos en este recurso? 📃✏️


*  Instalación y configuración ZAP (GUI y Docker).
*  Instalación de un sitio web de prueba local (OWASP Juice Shop).
*  Realizar escaneos (spider + active scan) y análisis de resultados.
*  Exportar reportes y detectar falsos positivos.
*  Limitaciones y comparativa con otras herramientas.

### Requisitos previos y entorno de pruebas

*  Máquina con Windows / macOS / Linux.
*  Sitio de prueba recomendado: OWASP Juice Shop. Se trata de una aplicación intencionalmente vulnerable con ejercicios y retos. Este puede ejecutarse localmente mediante Docker.

# Instalación ➡️


## OWASP ZAP

Tenemos dos opciones de instalación: La descarga nativa (GUI) o por Docker.


1.   Descarga nativa (GUI)

Debemos ir a la [web oficial de OWASP ZAP](https://www.zaproxy.org/) y descargar el instalador apropiado para el SO que se utilice.


Acto seguido, instalar y lanzar ZAP. La primera vez pedirá aceptar políticas y generará un certificado raíz para HTTPS intercept.


2.   Método APT / repositorios (SO como Debian, Ubuntu o Kali)
```
sudo apt update
sudo apt install zaproxy
```
## OWASP Juice Shop

Para instalar el sitio de prueba OWASP Juice Shop, el sitio de prueba local, utilizaremos Docker:
```
# Tenemos que instalar/actualizar docker y docker compose
sudo apt update
sudo apt install -y docker.io docker-compose
# Primero debemos activar el servicio docker
sudo systemctl enable --now docker
# Descargar y ejecutar Juice Shop
sudo docker pull bkimminich/juice-shop
sudo docker run --rm -p 3000:3000 bkimminich/juice-shop
# Se puede acceder en http://localhost:3000
```
En el caso de que se quisiese cerrar el servicio docker de Juice Shop bastaría con hacer Ctrl + C en la consola donde se ha iniciado el servicio o utilizar:
````
sudo docker stop juice
# Si no se ha usado --rm anteriormente, para borrarlo
sudo docker rm <ID_CONTAINER>
# Para saber el ID del contenedor se puede utilizar el siguiente comando para ver los contenedores activos
sudo docker ps
````
# Inicio y uso básico de ZAP: Escaneo manual y automático 🕸️


Lo primero de todo será iniciar ZAP, ya sea desde su ejecutable o mediante el comando
````
zaproxy
````
Cuando se inicia ZAP, nos preguntará si deseamos que la sesión que empecemos se mantenga activa. Podremos elegir que si con nombre personalizado y localización que deseemos o automático, o el caso de que no queramos que se guarde la sesión(Podemos pedirle que recuerde nuestra opción en la y no pregunte más).

Puede dar la posibilidad de que, al iniciar por primera vez o tras un tiempo sin usarlo, que nos pida actualizar los addons. Podemos seleccionar los que queramos o seleccionarlos todos.

### Configuración del proxy

En el navegador que utilicemos (Chrome, Firefox, etc...) tenemos que configurar el proxy a localhost:8080 por defecto (u otro puerto según configuremos en ZAP)

Tras ello, deberemos iniciar nuestro entorno local de pruebas si no lo hemos realizado antes. (Ver el apartado anterior sobre Juice-Shop)

## Realización de un escaneo manual 🔍🕵️‍♀️


Para realizar un escaneo de forma manual debemos:


1.   Seleccionar el modo "Manual explore".
2.   En la caja de texto colocamos la URL a escanear y explorar junto el navegador a utilizar (en nuestro caso, el que hayamos configurado el proxy) y clicamos en "Launch browser".
3.   Basta con navegar por todo el dominio web que queramos. Esta navegación, al realizar las peticiones disponibles, pasará por el proxy configurado de ZAP y este, a su vez, irá registrando cada URL con sus distintos archivos disponibles como imágenes, archivos de configuración disponibles, fuentes de letras, etc...
4.   Una vez registrado todo, cambiaríamos el modo de "Standard mode" a "ATTACK mode". En este modo, basta con seleccionar la URL registrada anteriormente, clicar con botón derecho y realizar un escaneo activo ("Active Scan"). Con este escaneo intentará obtener todas las vulnerabilidades web posibles dentro del dominio y que ZAP tenga en su base de datos.
5.   Para ver estos datos, podemos dirigirnos a la pestaña de "Alerts" donde estarán clasificadas por una bandera que indica el nivel de riesgo de la vulnerabilidad (Azul informativas, amarillo riesgo bajo, naranja riesgo medio y rojo riesgo alto).
6.   Una vez obtenida toda la información, podemos realizar un reporte automático con todo lo obtenido clicando en la opción "Report" en la barra de herramientas de la parte de arriba.

También podemos guardar la sesión de escaneo en un archivo de ZAP Session en cualquier momento por si queremos retomarlo en otro momento.

## Realización de un escaneo automático 💻🕷️


Esta opción es más rápida y bastante completa para lo simple que es. Se basa en módulos Spider. Los Spider exploran y descubren automáticamente las páginas, rutas y recursos de un sitio web navegando del mismo modo que lo haría un usuario o un bot, siguiendo enlaces, formularios y rutas accesibles con el objetivo de construir un mapa completo de las URLs del dominio junto los parámetros y recursos que existen en este. En ZAP tenemos dos tipos de Spider:
*   Spider tradicional: Sin manejar JavaScript, un escaneo rápido basado en HTML estático ideal para webs tradicionales.
*   Spider AJAX: Este tipo si maneja JavaScript, por lo que puede descubrir rutas en aplicaciones SPA (Como Angular, React y Vue) y rutas dinámicas. Usa un navegador real pero es más lento que el escaneo tradicional spider, aunque es más efectivo que este.

Si nuestro objetivo es tener una idea básica de vulnerabildiades de una web, con el tradicional es suficiente; sin embargo, si necesitamos un reporte mucho más completo optaremos por el spider AJAX.

Para utilizar el escaneo automático tendremos que:


1.   Seleccionar el modo "Automated Scan".
2.   Al igual que el método manual, introducimos la URL a atacar.
3.   Seleccionamos el tipo de Spider que queramos usar, si tradicional o AJAX junto al navegador en este último.
4.   Una vez todo configurado, clicaremos en el botón "Attack" para comenzar el escaneo automático.
5.   Acabado el escaneo, podremos ver todo el descubrimiento cual mapa en el menú izquierdo al igual que el escaneo manual, con todas las URLs secundarias y recursos disponibles.
6.   Como el escaneo es automático, también lo será el "Active scan", el escaneo activo para obtener todas las posibles vulnerabilidades que ZAP tenga en su base de datos.

Este escaneo automático nos puede dar resultados bastante óptimos donde se pueden encontrar vulnerabilidades de riesgo alto más ocultas en la navegación normal de un usuario que no caigamos de primeras.

Además, al clicar sobre la alerta podremos ver información de esta y como solucionarlo si tuviesemos permisos para configurar la página web. Incluso nos dan indicaciones básicas para aprovechar la vulnerabilidad detectada, como por ejemplo inyección de código (siempre con fines legítimos y/o educativos con permisos del propietario de la página o en un laboratorio virtual 👻).

Para finalizar, también podemos tanto realizar el reporte como guardar la sesión para continuarla en un futuro.

# Ejemplo práctico con Juice Shop local 🍹


Veamos el ejemplo con nuestra Juice Shop local:
1.   Primero ejecutaremos nuestro Juice Shop con Docker tal y como explicamos en el apartado de instalación.

<img src="https://github.com/Siannodel97/OWASP-ZAP---Arsenal-del-analista/blob/main/Imágenes/Juice1.png" width="60%">

2.   Con ZAP abierto y configurado ya, vamos a realizar un escaneo automático escogiendo Spider AJAX.
   
<img src="https://github.com/Siannodel97/OWASP-ZAP---Arsenal-del-analista/blob/main/Imágenes/Juice2.png" width="60%">

3.   Una vez escaneado la Juice Shop local, realizaremos el Active Scan para encontrar las vulnerabilidades y generaremos un reporte de estas.
   
<img src="https://github.com/Siannodel97/OWASP-ZAP---Arsenal-del-analista/blob/main/Imágenes/Juice3.png" width="60%">

## Para ver nuestro reporte de prueba [haga clic aquí](https://Siannodel97.github.io/OWASP-ZAP---Arsenal-del-analista/Reporte_Prueba_JuiceShop.html)


![Reporte de prueba imagen](https://github.com/Siannodel97/OWASP-ZAP---Arsenal-del-analista/blob/main/Imágenes/Juice4.png)

