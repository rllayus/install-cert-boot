# Instación de certificados gratuitos certbot
Se puede generar certificados gratuitos para cualquier dominio público siempre y cuando se pueda verificar la existencia de dicho dominio.
En el presente tutorial veremos los siguientes temas:  

 **Contenido**
* 1.- Instalación por defecto de NGINX  
* 2.- Instalación de la herramienta snap  
* 3.- Instalación de certbot  
* 4.- Generación de JKS en formato PKCS12  

## 1.- Instalación por defecto de NGINX 
Certbot verifica la existencia del dominio un servidor desplegado en el puerto 80, para lo cual debemos instalar este servidor con el único propósito de verificar la existencia del dominio.  
Ejecute el siguiente comando (opcional) para actualizar el repositorio del SO

    $ sudo yum update
Este proceso la primera vez puede durar varios minutos. Luego de que finalice ejecute el comando para instalar **NGINX** 

    $ sudo yum install nginx
En el proceso de instalación se le solicitará que confirme la instalación el cual debe aceptar. Al finalizar la instalación inicie el proceso con el siguiente comando.

    $ sudo systemctl start nginx
Con lo cual **NGINX** ya quedó instalado y ejecutandose
## 2.- Instalación de la herramienta snap
Es una herramienta para gestión de paquetes independientes del SO, en esta oportunidad lo utilizaremos para instalar **certbot**. Para instalar **snap** siga ejecute los siguientes comandos.  
La url del sitio web de snap es la siguiente https://snapcraft.io/docs/installing-snap-on-centos

    $ sudo yum install snapd
    $ sudo systemctl enable --now snapd.socket
    $ sudo ln -s /var/lib/snapd/snap /snap

Si siguió todos los pasos, hasta el momento ya tiene configurado y ejecutando se correctamente **snap**

*Nota. Se debe habilitar el acceso desde la internet al puerto 80, esto porque certbot necesita verificar el dominio*

## 3.- Instalación de certbot   
Certbot es una herramienta que nos permite obtener un certificado emitido por una entidad certificadora gratuita y válido por la mayoria de los navegadores y clientes de servicios web. 
El sitio de **certbot** es https://certbot.eff.org/ y tiene ayuda para instalar en diferentes SO y servidores web.
**Certbot** a diferencia de https://www.sslforfree.com/ que también permite obtener certificados emitidos por una entidad certificado es que automatiza la renovación del certificado, puesto que estos certificados sólo tienen una vigencia de 90 días.  
Para instalar **certbot** ejecute los siguientes comandos  

    $ sudo snap install --classic certbot
    $ sudo ln -s /snap/bin/certbot /usr/bin/certbot
    
Con ejecutar los anteriores comando ya se tiene instalado y configurado certbot. Pero falta un paso que es para configurar nginx para validar el dominio. Para ello ejecute el siguiente comando.

    $ sudo certbot --nginx
Al ejecutar el comando, certbot le solicitará una **dirección de correo electrónico** al cual notificará cuando el certificado esté por vencer y también el **dominio para el cual quiere generar el certificado** gratuito. Cuando finalice el proceso ya se tendrá generado certificados válidos en al ruta `/etc/letsencrypt/live/` como se muestra a continuación.

    #ls
    README  mi.dominio.com
    
  La carpeta `mi.dominio.com` contiene los certificados como se muestra a continuación.
  
    #ls
    README  cert.pem  certificate.pfx  chain.pem  fullchain.pem  privkey.pem
NGINX ha quedado configurado con htpps y con el certificado, para verificar puede ingresar a la ruta https://mi.dominio.com y se mostrará como un sitio seguro.

## 4.- Generación de JKS en formato PKCS12
En muchas ocaciones, se necesita el certificado no para desplegar servicios con nginx si con servidores java como *JBoss* *Wildfly* o microservicios con Spring Boot y para ello los certificados se los debe colocar en almacen de claves JKS o PKCS12. Crear un PKCS12 con los certificados generado por **certbot** ejecute el siguiente comando.  

    # openssl pkcs12 -export -out mi.dominio.com.p12 -inkey privkey.pem -in cert.pem -certfile 
Al ejecutar el comando la herramienta openssl le solicitará una contraseña para el almacen de claves, el mismo debe anotarlo puesto que se requiere para configurar el servidor de aplicaciones. 
Los certificados se han guardado en el almacen de claves bajo el alias **1** que será necesario para configurar en el servidor de aplicaciones.

    
    
    
