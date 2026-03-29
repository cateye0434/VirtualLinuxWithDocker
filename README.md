# Virtual Linux With Docker
Eigenständiger Aufbau einer virtualisierten Linux-Serverumgebung mit Docker für NGINX, MySQL und phpMyAdmin./ Independent setup of a virtualized Linux server environment using Docker (NGINX, MySQL, phpMyAdmin).

Ich erstelle hier eine VMBox mit Linux-Server(Ubuntu 64-bit). Nun nutze ich "sudo apt update" in der Bash, um Index-Files lokaler Pakete mit ihren Quellrepositories abzugleichen und eventuelle Updates zu finden und zu installieren. Danach installiere Docker mit "sudo apt install docker.io -y" und teste die Installation mit "docker --version" und "sudo apt install docker-compose -y". 
Um ein eigenes Projekt zu starten, erstelle ich ein Verzeichnis mit mkdir ("mkdir sysadmin-homelab"), gehe in dieses Verzeichnis mit "cd sysadmin-homelab".
Nun erstelle ich eine yml-File mit dem GNU nano-Editor ("nano docker-compose.yml"), mit der ich per yml-File Nginx-Server mit phpMyAdmin für die MySQL-Datenbank installiere. Hier werden Port 8080 und 8081 des Hosts auf Port 80 der VM weitergeleitet.

<YML>
version: '3'

services:
  web:
    image: nginx
    ports:
      - "8080:80"

  db:
    image: mysql
    environment:
      MYSQL_ROOT_PASSWORD: root

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    ports:
      - "8081:80"
      
Schließlich bringe ich den Container mit "sudo docker-compose up -d" im Hintergrund zum laufen und prüfe mit "docker ps" die Container-Namen und -IDs. Mit "ip a" sehe ich die IP auf dem Hostsystem, unter der ich den Server erreichen kann. 

Der Server war über den Host nicht erreichbar. Ich habe das Problem online recherchiert und fand heraus, das dies häufig an der Netzwerk-Einstellung der VM liegt.
Ich löse das Problem, indem ich die VM herunterfahre, auswähle und in ihren Systemeinstellungen die Netzwerkeinstellungen von NAT zu Bridge ändere.

![Screenshot 2026-03-29 225520](https://github.com/user-attachments/assets/c0e0574d-fac5-49ce-a148-da0f85094b93)

![Screenshot 2026-03-29 225841](https://github.com/user-attachments/assets/9b2adfea-c7b6-4b53-9f9d-89e9dfe67452)



Außerdem war eine Zeile der yml-Datei falsch eingerückt, was ich geändert habe.

Schließlich waren der Server und phpMyAdmin über den Host erreichbar, die MySQL-Datenbank soll bewusst nicht "von außen"/ über den Host erreichbar sein, um Datensicherheit zu gewährleisten.

![nginx_screenshot](https://github.com/user-attachments/assets/3671bc6b-ae59-42bd-875e-0ec79dc6fae2)

![phpmyadmin_screenshot](https://github.com/user-attachments/assets/e75a4f59-6252-4b4e-924e-3925a581d12b)


![phpmyadmin_screenshot2](https://github.com/user-attachments/assets/2a919e64-17c0-4a0f-9b50-29783f134504)


