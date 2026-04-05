# Virtual Linux With Docker


## Ziel des Projekts 
Eigenständiger Aufbau einer virtualisierten Linux-Serverumgebung mit Docker für NGINX, MySQL und phpMyAdmin./ Independent setup of a virtualized Linux server environment using Docker (NGINX, MySQL, phpMyAdmin).

## VM Setup
- VirtualBox
- Ubuntu Server 22.04
- 2 GB RAM
- 20 GB Disk

## Architektur
Die Umgebung besteht aus mehreren Docker-Containern, die über ein internes Docker-Netzwerk miteinander kommunizieren.

- Ein NGINX-Container stellt eine einfache Website bereit
- Ein MySQL-Container dient als Datenbank
- Ein phpMyAdmin-Container ermöglicht die Verwaltung der Datenbank über den Browser

Die Container sind logisch voneinander getrennt, können aber intern miteinander kommunizieren (z. B. phpMyAdmin → MySQL).

Zugriff erfolgt über definierte Ports:

- Port 8080 → Webserver (NGINX)
- Port 8081 → phpMyAdmin

Die Datenbank ist nicht direkt von außen erreichbar, sondern nur innerhalb des Docker-Netzwerks, was die Sicherheit erhöht.

## Verwendete Tools
- Ubuntu Server (virtuelle Maschine)
- Docker & Docker Compose (Containerisierung)
- NGINX (Webserver)
- MySQL (Datenbank)
- phpMyAdmin (Datenbankverwaltung)
- VirtualBox (Virtualisierung)


## Ausführung
Ich erstelle hier eine VMBox mit Linux-Server(Ubuntu 64-bit). Nun nutze ich "sudo apt update" in der Bash, um Index-Files lokaler Pakete mit ihren Quellrepositories abzugleichen und eventuelle Updates zu finden und zu installieren. Danach installiere Docker mit "sudo apt install docker.io -y" und teste die Installation mit "docker --version" und "sudo apt install docker-compose -y". 
Um ein eigenes Projekt zu starten, erstelle ich ein Verzeichnis mit mkdir ("mkdir sysadmin-homelab"), gehe in dieses Verzeichnis mit "cd sysadmin-homelab".
Nun erstelle ich eine yml-File mit dem GNU nano-Editor ("nano docker-compose.yml"), mit der ich per yml-File Nginx-Server mit phpMyAdmin für die MySQL-Datenbank installiere. Hier werden Port 8080 und 8081 des Hosts auf Port 80 der VM weitergeleitet.


![erläuterung_ports_s_chat](https://github.com/user-attachments/assets/de5655b5-f2fb-4a00-9166-a7ea26d172bf)



## yml-File

![yml](https://github.com/user-attachments/assets/52dfd794-17df-4dda-b82b-df8ff75c85c1)


      
Schließlich bringe ich den Container mit "sudo docker-compose up -d" im Hintergrund zum laufen und prüfe mit "docker ps" die Container-Namen und -IDs. Mit "ip a" sehe ich die IP auf dem Hostsystem, unter der ich den Server erreichen kann. 

![docker_compose](https://github.com/user-attachments/assets/1e7b7e79-df38-4d0e-8b0d-a1bde092435f)


Der Server war über den Host nicht erreichbar. Ich habe das Problem online recherchiert und fand heraus, das dies häufig an der Netzwerk-Einstellung der VM liegt.
Ich löse das Problem, indem ich die VM herunterfahre, auswähle und in ihren Systemeinstellungen die Netzwerkeinstellungen von NAT zu Bridge ändere.

![Screenshot 2026-03-29 225520](https://github.com/user-attachments/assets/c0e0574d-fac5-49ce-a148-da0f85094b93)

![Screenshot 2026-03-29 225841](https://github.com/user-attachments/assets/9b2adfea-c7b6-4b53-9f9d-89e9dfe67452)



Außerdem war eine Zeile der yml-Datei falsch eingerückt, was ich geändert habe.

Schließlich waren der Server und phpMyAdmin über den Host erreichbar, die MySQL-Datenbank soll bewusst nicht "von außen"/ über den Host erreichbar sein, um Datensicherheit zu gewährleisten.

![nginx_screenshot](https://github.com/user-attachments/assets/3671bc6b-ae59-42bd-875e-0ec79dc6fae2)


![phpmyadmin_screenshot](https://github.com/user-attachments/assets/e75a4f59-6252-4b4e-924e-3925a581d12b)


![phpmyadmin_screenshot2](https://github.com/user-attachments/assets/2a919e64-17c0-4a0f-9b50-29783f134504)


Schließlich kann man z.B. noch eine Datenbank mit entsprechenden MySQL-Befehlen anlegen. Hier ein Beispiel für eine Nutzerdatenbank einer Lernsoftware mit den Tabellen Users, Session, Subject, Item, Item_Session:

![table](https://github.com/user-attachments/assets/4fd71364-2558-4b14-84d4-818be429ba1f)

...
CREATE TABLE Users (
    Users_ID INT AUTO_INCREMENT PRIMARY KEY,
    Users_Name VARCHAR(100) NOT NULL,
    Hash VARCHAR(100) NOT NULL,

    Salt VARCHAR(100) NOT NULL
);

CREATE TABLE Session (
    Session_ID INT AUTO_INCREMENT PRIMARY KEY,
    Session_Date DATETIME NOT NULL,
    Session_Points DECIMAL(5,2) NOT NULL,
    Users_ID INT,
    CONSTRAINT fk_session_users 
        FOREIGN KEY (Users_ID) 
        REFERENCES Users(Users_ID)
        ON DELETE CASCADE
);

CREATE TABLE Subject (
    Subject_ID INT AUTO_INCREMENT PRIMARY KEY,
    Subject_Name VARCHAR(100)
);

CREATE TABLE Item (
    Item_ID INT AUTO_INCREMENT PRIMARY KEY,
    Item_Name VARCHAR(100),
    Item_Content VARCHAR(100) NOT NULL,
    Subject_ID INT,
    CONSTRAINT fk_item_subject 
        FOREIGN KEY (Subject_ID) 
        REFERENCES Subject(Subject_ID)
        ON DELETE CASCADE
);

CREATE TABLE ItemSession (
    Item_ID INT NOT NULL,
    Session_ID INT NOT NULL,
    CONSTRAINT fk_item 
        FOREIGN KEY (Item_ID) 
        REFERENCES Item(Item_ID)
        ON DELETE CASCADE,
    CONSTRAINT fk_session 
        FOREIGN KEY (Session_ID) 
        REFERENCES Session(Session_ID)
        ON DELETE CASCADE,
    UNIQUE (Item_ID, Session_ID)
);
...


![phpMyAdmin_SQL](https://github.com/user-attachments/assets/34f0d62f-917d-43ad-bbc6-883fa9da5443)


Auf diese Datenbank kann man nun über eine Anpassung der index.html auf der Seite zugreifen.



