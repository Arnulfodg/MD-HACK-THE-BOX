# Reconocimiento
sudo nmap -sS -sV -A --min-rate 5000 --open -vvv -Pn 10.10.11.23

# Resultado

| port | state | service | reason | version |
|------|-------|---------|--------|---------|
| 22 | open | ssh |  syn-ack | OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0) |
| 80 | open | http |  syn-ack | Apache httpd 2.4.52 |

