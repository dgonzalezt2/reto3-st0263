# ST0263 - Tópicos Especiales de Telemática

## Integrantes:
- David Gonzalez Tamayo (dgonzalez2@eafit.edu.co)
- Juan Esteban Castro Garcia (jecastrog@eafit.edu.co)
- Tomas Bernal Zuluaga (tbernalz@eafit.edu.co)

## Profesor
- **Nombre:** Edwin Nelson Montoya Munera
- **Correo:** emontoya@eafit.edu.co

# Proyecto 1 (Sistema de archivos distribuidos)

## 1. Breve descripción de la actividad

En este proyecto, implementaremos una aplicación centralizada utilizando WordPress en un entorno Docker, centrándonos en garantizar alta disponibilidad y capacidad de ampliación. Nuestro enfoque implica el uso de Nginx como un equilibrador de carga para dirigir el tráfico entre dos instancias de WordPress, mejorando así el rendimiento y la confiabilidad del sistema. Además, la arquitectura contempla instancias separadas para la base de datos MySQL y el almacenamiento de archivos mediante NFS, todos alojados en AWS con cinco máquinas virtuales diseñadas para garantizar un funcionamiento continuo y eficiente. También se incluye una instancia adicional (bastion host) para acceder a las instancias privadas (Wordpress, base de datos y NFSServer). Este diseño no solo facilita la gestión y el mantenimiento del sistema, sino que también fortalece la seguridad y la capacidad de respuesta ante un aumento en la demanda de usuarios.

### 1.1. Que aspectos cumplió o desarrolló de la actividad propuesta por el profesor (requerimientos funcionales y no funcionales)

Aspectos cumplidos de la actividad propuesta por el profesor:

* Aplicación wordpress o app seleccionada dockerizada monolítica en varios nodos que mejore la
disponibilidad de esta aplicación.
* Implementar un balanceador de cargas basado en nginx que reciba el tráfico web https de
Internet con múltiples instancias de procesamiento.
* Tener 2 instancias de procesamiento wordpress o app seleccionada detrás del balanceador de
cargas.
* Tener 1 instancia de bases de datos mysql.
* Tener 1 instancia de archivos distribuidos en NFS.
* Arquitectura propuesta en el documento guía implementada.
* Despliegue correcto con dominio y subdominio propio.
* Certificado SSL.

### 1.2. Que aspectos NO cumplió o desarrolló de la actividad propuesta por el profesor (requerimientos funcionales y no funcionales)

* N/A

## 2. Información general de diseño de alto nivel, arquitectura, patrones, mejores prácticas utilizadas.
Se utilizó la arquitectura presentada a continuación:

![image](https://github.com/dgonzalezt2/reto3-st0263/assets/81880494/631b39e3-bc54-4f72-ad77-b65a94092d4a)

## 3. Descripción del ambiente de desarrollo y técnico: lenguaje de programación, librerias, paquetes, etc, con sus numeros de versiones.

Se utilizó Docker, con sus respectivos docker-compose para contenerizar correctamente las aplicaciones y servicios necesarios, puede encontrar estos archivos en la carpeta `docker` de este repositorio.

## 4. Descripción del ambiente de EJECUCIÓN (en producción) lenguaje de programación, librerias, paquetes, etc, con sus numeros de versiones.

## IP o nombres de dominio en nube o en la máquina servidor.

Para acceder a la página web: https://reto3.reto3.me/

* **Balanceador de carga:** IP elástica de AWS: 3.215.163.106.

* **Wordpress 1:** IP privada: 10.0.2.16

* **Wordpress 2:** IP privada: 10.0.2.118

* **Base de datos:** IP privada: 10.0.2.85

* **NFS Server:** IP privada: 10.0.2.20

![image](https://github.com/dgonzalezt2/reto3-st0263/assets/82610906/2cf4685e-5f0e-4d3f-88f0-d2ef7c9420dc)
![image](https://github.com/dgonzalezt2/reto3-st0263/assets/82610906/d2b2b512-1701-49c5-9f3a-1f79333814aa) 

## 5. Resultado final.

![Video explicativo](https://youtu.be/ce-l0f2PG0M)

![image](https://github.com/dgonzalezt2/reto3-st0263/assets/82610906/e7733d93-1106-4f57-8255-2aa4f1861100)
![image](https://github.com/dgonzalezt2/reto3-st0263/assets/82610906/c198b9d7-791e-4f50-87c6-96239d0d1ed7)
![image](https://github.com/dgonzalezt2/reto3-st0263/assets/82610906/1f2dc4d4-a7fa-4698-93fd-21ad7cac3b71)
![image](https://github.com/dgonzalezt2/reto3-st0263/assets/82610906/334c212a-30b3-4b69-ade4-d0ebc23d782a)



## 6. Referencias.

