Was Wichtig ist:

Standardaufbau des Netzwerk anschauen



# Zusammenfassung Prüfung 1

## Lernziele

- [**LZ 1**: Container-Konzepte verstehen](#1-grundlagen-docker-komponenten)
- [**LZ 2**: Virtualisierung vs. Cloud unterscheiden](#2-virtualisierung-vs-cloud-computing)
- [**LZ 3**: Docker-Container ausführen](#3-docker-container-ausführen)
- [**LZ 4**: Portweiterleitungen verstehen und konfigurieren](#4-portweiterleitungen)
- [**LZ 5**: Mit Docker Volumes arbeiten](#5-arbeiten-mit-docker-volumes)
- [**LZ 6**: Volumes benennen & in Verzeichnissen speichern](#6-volumes-benennen--in-verzeichnissen-speichern)
- [**LZ 7**: Netzwerke mit Docker aufbauen](#7-netzwerktypen-in-docker)
- [**LZ 8**: Eigene Netzwerke definieren](#8-eigene-netzwerke-definieren)
- [**LZ 9**: Docker-Systemressourcen verwalten](#9-docker-administration)

---

## 1. Grundlagen: Docker-Komponenten

| Begriff            | Definition |
|--------------------|------------|
| **Docker-Container** | Laufende Instanz eines Images |
| **Docker-Image**    | Vorlage für Container mit Code & Abhängigkeiten (Read-Only) |
| **Dockerfile**      | Bauanleitung zur Erstellung eines Images |
| **Docker-Registry** | Speicherort für Images (z. B. Docker Hub) |

**Quelle**: [GitHub M169](https://gbssg.gitlab.io/m169)

---

## 2. Virtualisierung vs. Cloud Computing

- **Virtualisierung**: Mehrere virtuelle Maschinen laufen auf einer physischen Hardware, was Ressourcen effizienter nutzt. Häufig in lokalen Rechenzentren angewendet.
- **Cloud Computing**: IT-Ressourcen (Rechenleistung, Speicher, Software) werden über das Internet bereitgestellt. Ermöglicht Skalierbarkeit, Automatisierung und nutzungsbasierte Abrechnung.

**Quelle**: ChatGPT

---

## 3. Docker-Container ausführen

```sh
docker run -d --name mariadb-test 
```
**Parameter:**
- `-d` → Daemon, startet im Hintergrund
- `--name` → name vom container

**Quelle**: Toni

---

## 4. Portweiterleitungen

Portweiterleitungen verhindern Konflikte zwischen Applikationen, die auf denselben Ports laufen.

```sh
docker run -p 8080:80 image_name
```
**Erklärung:**  
- `-p 8080:80` → Leitet Port 80 des Containers auf Port 8080 des Hosts weiter.

---

## 5. Arbeiten mit Docker Volumes
gehe zu auftrag 1.4

- **Volume erstellen**:
  ```sh
  docker volume create mein_volume
  ```

- **Container mit Volume starten**:
  ```sh
  docker run -d --name mariadb-test2 -e MYSQL_ROOT_PASSWORD=geheim -v mein_volume:/var/lib/mysql mariadb
  ```

- **Genutzte Volumes anzeigen**:
  ```sh
  docker ps -a --filter volume=name
  ```

- **Volume-Details abrufen**:
  ```sh
  docker volume inspect mein_volume
  ```

**Quelle**: ChatGPT

---

## 6. Volumes benennen & in Verzeichnissen speichern

```sh
docker run -d --name mariadb-test -e MYSQL_ROOT_PASSWORD=geheim -v /mein/pfad:/var/lib/mysql mariadb
```

### 1. Erstellen Sie einen Ordner, bspw. mkdir / home/username/varlibmysql.

```sh
mkdir varlibmysql
```

### 2. Starten Sie den Container mariadb und geben das lokale Volume an mit —v / : /var/lib/mysgl

```sh
docker run -d --name mariadb-test -e MYSQL_ROOT_PASSWORD=geheim -v /home/vmadmin/varlibmysql:/var/lib/mysql mariadb
```


**Quelle**: ChatGPT

---

## 7. Netzwerktypen in Docker

| Netzwerktyp | Beschreibung |
|------------|--------------|
| **bridge** (Standard) | Container können untereinander kommunizieren, aber nicht direkt mit dem Host |
| **host**  | Container nutzt direkt das Host-Netzwerk (keine Isolation) |
| **none**  | Kein Netzwerk, vollständige Isolation |

Befehl zur Anzeige aller Netzwerke:
```sh
docker network ls
```

**Quelle**: ChatGPT

---

## 8. Eigene Netzwerke definieren

- **Netzwerk erstellen**:
  ```sh
  docker network create mein-netzwerk
  ```

- **Container in Netzwerk aufnehmen**:
  ```sh
  docker run -d --name mariadb-test --network mein-netzwerk mariadb
  ```

### 1. Erstellen Sie ein eigenes Netzwerk.


```sh
docker network create testnet
```

### 2. Nehmen Sie MariaDB, phpMyAdmin und Wordpress als eigene Container in Betrieb und verbinden Sie diese Container über ihr eigenes Netzwerk.

```sh
docker run -d --name mariadb-test --network testnet -e MYSQL_RANDOM_ROOT_PASSWORD=1 -e MYSQL_DATABASE=wp -e MYSQL_USER=wpuser -e MYSQL_PASSWORD=geheim -v myvolume:/var/lib/mysql mariadb
```

```sh
docker run -d --name pma --network testnet -p 8080:80 -e PMA_HOST=mariadb-test phpmyadmin/phpmyadmin
```

```sh
docker run -d --name wordpress-test --network testnet -h wordpress-test -v wp-html:/var/www/html/wp-content -p 8081:80 -e WORDPRESS_DB_HOST=mariadb-test -e WORDPRESS_DB_USER=wpuser -e WORDPRESS_DB_NAME=wp -e WORDPRESS_DB_PASSWORD=geheim wordpress
```

**Quelle**: [GitHub M169](https://gbssg.gitlab.io/m169) & ChatGPT

---

## 9. Docker-Administration

Auftrag 1.6

### **Container verwalten**
```sh
docker system df           # Speicherverbrauch anzeigen
docker ps                 # Laufende Container anzeigen
docker ps -a              # Alle Container (auch gestoppte) anzeigen
docker rm container_name  # Container löschen
docker rm $(docker ps -aq) # Alle gestoppten Container löschen
```

### **Images verwalten**
```sh
docker images               # Alle Images anzeigen
docker rmi image_id         # Ein bestimmtes Image löschen
docker image prune -a       # Alle ungenutzten Images löschen
```

### **Volumes verwalten**
```sh
docker volume ls            # Alle Volumes anzeigen
docker volume inspect name  # Details zu einem Volume
docker volume rm name       # Volume löschen
docker volume prune         # Unbenutzte Volumes löschen
```

### **Netzwerke verwalten**
```sh
docker network ls           # Alle Netzwerke anzeigen
docker network inspect name # Details zu einem Netzwerk
docker network rm name      # Netzwerk löschen
docker network prune        # Unbenutzte Netzwerke löschen
```

### **Speicher freigeben**
```sh
docker system prune -a      # Unbenutzte Daten automatisch entfernen
```

---

Diese Zusammenfassung bietet einen strukturierten Überblick für die Prüfungsvorbereitung mit Docker. 🐳✨
