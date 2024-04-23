# ST0263 - Tópicos Especiales de Telemática

## Integrantes:
- David González Tamayo (dgonzalez2@eafit.edu.co)
- Juan Esteban Castro García (jecastrog@eafit.edu.co)
- Tomás Bernal Zuluaga (tbernalz@eafit.edu.co)

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

### Docker Compose Wordpress
```yaml
version: '3.1'

services:
  wordpress:
    image: wordpress:latest
    ports:
      - "80:80"
    volumes:
      - /mount/point:/var/www/html/wp-content/uploads
    environment:
      WORDPRESS_DB_HOST: 10.0.2.85
      WORDPRESS_DB_USER: wordpress_user
      WORDPRESS_DB_PASSWORD: strongpassword
      WORDPRESS_DB_NAME: wordpress_db
    networks:
      - vpc_network
networks:
  vpc_network:
    driver: bridge
```

### Docker Compose MySQL

```yaml
version: '3.1'

services:
  mysql:
    image: mysql:latest
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: wordpress_db
      MYSQL_USER: wordpress_user
      MYSQL_PASSWORD: strongpassword
    networks:
      - vpc_network
networks:
  vpc_network:
    driver: bridge
```

## Configuración Nginx
```bash
# Início da configuração do Nginx
user www-data;  # Define o usuário para processos do Nginx
worker_processes auto;  # Número de processos trabalhadores (auto é uma escolha comum)

# Seção 'events' é obrigatória
events {
  worker_connections 1024;  # Número máximo de conexões simultâneas por processo trabalhador
}

# Seção 'http' para configuração de servidores e outros parâmetros
http {
  # Definição do upstream para balanceamento de carga
  upstream wordpress_backend {
    ip_hash;
    server 10.0.2.16:80;  # Primeira instância do WordPress
    server 10.0.2.118:80;  # Segunda instância do WordPress
  }

  # Bloco de servidor para redirecionar HTTP para HTTPS
  server {
    listen 80;
    server_name reto3.reto3.me;

    location / {
      return 301 https://reto3.reto3.me$request_uri;  # Redireciona para HTTPS
    }
  }

  # Bloco de servidor para HTTPS com certificado SSL
  server {
    listen 443 ssl;
    server_name reto3.reto3.me;

    ssl_certificate /etc/letsencrypt/live/reto3.reto3.me/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/reto3.reto3.me/privkey.pem;

    location / {
      proxy_pass http://wordpress_backend;
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }

  # Bloco de servidor para redirecionar acesso por IP para domínio
  server {
    listen 443 ssl;
    server_name 3.211.239.255;

    location / {
      return 301 https://reto3.reto3.me$request_uri;  # Redireciona para domínio
    }
  }
}
```

## 5. Resultado final.

![Video explicativo](https://youtu.be/ce-l0f2PG0M)

![image](https://github.com/dgonzalezt2/reto3-st0263/assets/82610906/e7733d93-1106-4f57-8255-2aa4f1861100)
![image](https://github.com/dgonzalezt2/reto3-st0263/assets/82610906/c198b9d7-791e-4f50-87c6-96239d0d1ed7)
![image](https://github.com/dgonzalezt2/reto3-st0263/assets/82610906/1f2dc4d4-a7fa-4698-93fd-21ad7cac3b71)
![image](https://github.com/dgonzalezt2/reto3-st0263/assets/82610906/334c212a-30b3-4b69-ade4-d0ebc23d782a)



## 6. Referencias.
* [Let's Encrypt](https://medium.com/@utkarsh_verma/how-to-obtain-a-wildcard-ssl-certificate-from-lets-encrypt-and-setup-nginx-to-use-wildcard-cfb050c8b33f)
* [Nginx con Cerbot](https://www.letscloud.io/community/how-to-set-up-an-nginx-with-certbot-on-ubuntu)
* [NFS](https://mohsensy.github.io/sysadmin/2020/04/01/wordpress-nfs.html)
* [ChatGPT](https://chat.openai.com/)
