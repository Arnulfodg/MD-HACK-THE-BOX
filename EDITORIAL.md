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


