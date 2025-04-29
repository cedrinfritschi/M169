- Sie kennen die Syntax von Dockerfiles.
- Sie können das Beispiel von Dockerfiles nachvollziehen.
- Sie können eigene Dockerfiles erstellen, testen und dokumentieren.
- Sie kennen den Einsatzzweck von Docker compose.
- Sie verstehen den Aufbau einer YAML-Datei und können eigene YAML-Dateien erstellen.
- Sie können Docker compose anwenden.
- Sie verstehen wie in Docker compose mit Passwörtern oder sonstigen geheimen Informationen umgegangen wird.

Hier eine kurze, prägnante Erläuterung jedes Lernziels – damit du sofort siehst, worauf es ankommt und was du am Ende praktisch beherrschen solltest:

|#|Lernziel|Was das konkret bedeutet|Praktischer Kompetenz‑Check|
|---|---|---|---|
|1|**Syntax von Dockerfiles kennen**|Du verstehst die Schlüsselwörter (`FROM`, `RUN`, `COPY`, …) und weißt, in welcher Reihenfolge sie typischerweise stehen (Build‑Stages, Layer‑Reihenfolge).|Kannst du ein vorhandenes Dockerfile lesen und erklären, was jeder Befehl tut?|
|2|**Beispiel‑Dockerfiles nachvollziehen**|Du kannst Schritt für Schritt herleiten, wie ein Beispiel‑Image gebaut wird, welche Layer entstehen und warum bestimmte Befehle gewählt wurden.|Baue ein Beispiel‑Dockerfile, zeige mit `docker history`, welche Layer entstanden, und begründe die Wahl der Basis‑Images.|
|3|**Eigene Dockerfiles erstellen, testen, dokumentieren**|Du erstellst ein schlankes, wiederholbar baubares Dockerfile, testest es lokal (`docker build && docker run`) und versiehst es mit Kommentaren/README.|Baue ein Microservice‑Image < 150 MB, starte es und beschreibe in einem kurzen README alle Schritte.|
|4|**Einsatzzweck von Docker Compose kennen**|Du weißt, warum man Compose nutzt: Mehrere Container als einen logischen Stack starten, Netzwerke/Volumes definieren, lokale Dev‑Umgebungen automatisieren.|Kannst du verbal erklären, wann Compose sinnvoller ist als ein einzelnes `docker run`‑Aufruf?|
|5|**Aufbau einer YAML‑Datei verstehen & eigene YAML schreiben**|Du kennst Einrückung, Schlüssel‑Wert‑Paare, Sequenzen/Listen, Verankerungen (`&`, `*`), und typische Stolperfallen (Tabs vs. Spaces).|Schreibe eine minimale, aber gültige YAML‑Datei (z. B. ein Compose‑Snippet) ohne Linter‑Fehler.|
|6|**Docker Compose anwenden**|Du formulierst `docker-compose.yaml` (oder v2‑`compose.yaml`), startest/stopst Services (`docker compose up/down`) und nutzt Volumes, Netzwerke, Abhängigkeiten, Healthchecks.|Starte einen Stack mit Datenbank + Backend + Frontend, prüfe Log‑Ausgaben und Container‑Health.|
|7|**Umgang mit Passwörtern/Secrets in Compose verstehen**|Du kennst Best Practices: Umgebungsvariablen aus `.env`, Docker Secrets, externe Secret‑Manager, niemals Klartext im Git.|Richtest in Compose ein Datenbank‑Passwort ein, das **nicht** im Repo landet (z. B. via `.env` oder `secrets:`).|

**So kannst du vorgehen, um jedes Ziel zu erreichen**

1. **Lesen & Nachbauen**
    
    - Offizielle Docker‑Doku zu Dockerfiles & Compose lesen.
        
    - Beispiel‑Projekte klonen und lokal durchspielen.
        
2. **Experimentieren**
    
    - Variiere Befehle (`RUN`, `CMD`, `ENTRYPOINT`) und beobachte Unterschiede.
        
    - Vergleiche `docker compose` v1 vs. v2‑Syntax.
        
3. **Mini‑Projekt umsetzen**
    
    - Wähle ein kleines Web‑ oder CLI‑Tool.
        
    - Schreibe ein optimiertes Dockerfile.
        
    - Baue eine `compose.yaml`, die zusätzlich eine DB startet.
        
    - Dokumentiere alles in Markdown.
        
4. **Security‑Schwerpunkt**
    
    - Übe den Einsatz von `.env`‑Dateien.
        
    - Teste Docker Secrets (Linux‑only) oder binde HashiCorp Vault/Azure Key Vault an.
        

Mit diesen Etappenzielen und praktischen Checks kannst du systematisch prüfen, ob du wirklich bereit bist – statt nur „verstanden zu haben“. Viel Erfolg!


Natürlich! Ich erkläre dir die Lernziele Schritt für Schritt und bringe jeweils Beispiele, damit du ein gutes Verständnis entwickelst. Lass uns direkt loslegen:

---

### ✅ **1. Sie kennen die Syntax von Dockerfiles.**

![image](https://github.com/user-attachments/assets/7865d49c-cd0e-4602-8c00-242d4bc9cb91)


Ein **Dockerfile** ist eine Textdatei, die Anweisungen enthält, wie ein Docker-Image erstellt werden soll. Jede Anweisung beschreibt einen Schritt im Build-Prozess.

#### Häufige Dockerfile-Befehle:

- `FROM`: Gibt das Basis-Image an.
- `RUN`: Führt einen Befehl im Container aus (z. B. Installation).
- `COPY` oder `ADD`: Kopiert Dateien in das Image.
- `CMD` oder `ENTRYPOINT`: Gibt an, welcher Befehl beim Start des Containers ausgeführt wird.

#### Auftrag 2.1

##### Dockerfile Syntax
```sh
FROM node:13-alpine
ENV MONGO_DB_USERNAME=admin \
	MONGO_DB_PWD=password
RUN mkdir -p /home/app
COPY . /home/app #. (alles) source /home/app destination
CMD["node","server.js"]
```

```sh
docker build -t my-app:1.0 .
```

- . source

##### connect to container
```sh
docker exec -it image_id /bin/sh
```

#### Beispiel:

```Dockerfile
FROM node:18

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000
CMD ["npm", "start"]
```

🔍 **Was passiert hier?**
- Node.js 18 wird als Basis verwendet.
- Der Arbeitsordner ist `/app`.
- `package.json` wird kopiert und `npm install` wird ausgeführt.
- Der Rest des Codes wird kopiert.
- Port 3000 wird freigegeben.
- Beim Start wird `npm start` ausgeführt.

---

### ✅ **2. Sie können das Beispiel von Dockerfiles nachvollziehen.**

Am Beispiel oben:
- Der Container wird auf Node.js-Basis gebaut.
- Alle notwendigen Dateien werden hinzugefügt.
- Das Projekt kann direkt mit einem einzigen Befehl (`docker build`) containerisiert werden.

#### Build und Run Beispiel:

```bash
docker build -t mein-node-app .
docker run -p 3000:3000 mein-node-app
```

#### Auftrag 2.2


##### 1. Versuchen Sie die einzelnen Kommandos beim Einführungsbeispiel nachzuvollziehen und zu beschriften

```sh
# Datei Docker file
FROM ubuntu:20.04
LABEL maintainer "name@somehost.com"
RUN apt—get update & & \
	apt—get install —y joe && \
	apt—get clean && \
	rm —rf /var/lib/apt/lists/*
CMD [ "/bin/bash" ]
```

- **`FROM ubuntu:20.04`** – Basis-Image (Ubuntu 20.04).
- **`LABEL maintainer "name@somehost.com"`** – Metadaten mit dem Maintainer Zusatzinfo
- **`RUN apt-get update && \`** – Aktualisiert Paketlisten.
- **`apt-get install -y joe && \`** – Installiert den Editor **joe**.
- **`apt-get clean && \`** – Entfernt unnötige Paketdaten.
- **`rm -rf /var/lib/apt/lists/*`** – Löscht Paketlisten, um Platz zu sparen.
- **`CMD [ "/bin/bash" ]`** – Standardkommando: Startet eine Bash-Shell.
##### 2. Recherchieren Sie den Unterschied zwischen ADD und COPY.

✅ **`COPY` bevorzugen**, wenn nur Dateien kopiert werden.  
✅ **`ADD` nur nutzen**, wenn **Archive entpackt oder externe URLs geladen** werden sollen.

##### 3. Recherchieren Sie den Unterschied zwischen CMD und ENTRYPOINT (Hinweis: Bei vielen Base-Images ist der ENTRYPOINT nicht definiert)

|Feature|CMD|ENTRYPOINT|
|---|---|---|
|Überschreibbar?|Ja, durch Kommandozeilenbefehl|Nein, es bleibt bestehen|
|Verwendung|Standardbefehl (kann ersetzt werden)|Festgelegter Startprozess|
|Beispiel|`CMD ["python", "app.py"]`|`ENTRYPOINT ["python", "app.py"]`|

⚠️ **Hinweis:** Viele Base-Images haben keinen definierten `ENTRYPOINT`, sodass `CMD` als Standard genutzt wird.

Beispiel:
```sh
FROM ubuntu:20.04
CMD ["/bin/bash"]
ENTRYPOINT ["ping"]
```
##### 4. Warum werden bei RIJN die Befehle mit einem verknüpft und nicht einzeln abgesetzt?

**Warum `&&` bei `RUN`?**

1. **Weniger Layer** → Reduziert Image-Größe.
2. **Effizienter** → Verhindert unnötige Datenreste.
3. **Atomare Ausführung** → Falls ein Befehl fehlschlägt, stoppt der ganze `RUN`-Block.


---

### ✅ **3. Sie können eigene Dockerfiles erstellen, testen und dokumentieren.**

Ein eigener Dockerfile hängt vom Projekt ab – hier ein Beispiel für eine Python-App:

```Dockerfile
# Dockerfile für eine Flask-App
FROM python:3.10

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .

EXPOSE 5000
CMD ["python", "app.py"]
```

#### Testen:

```bash
docker build -t meine-flask-app .
docker run -p 5000:5000 meine-flask-app
```

#### Dokumentieren:
Du kannst in Dockerfiles Kommentare mit `#` schreiben:
```Dockerfile
# Installiere Abhängigkeiten
RUN pip install -r requirements.txt
```

---

### ✅ **4. Sie kennen den Einsatzzweck von Docker Compose.**

**Docker Compose** ist ein Tool, mit dem du Multi-Container-Anwendungen definieren und ausführen kannst – z. B. eine App mit Datenbank.

Anstatt mehrere `docker run` Befehle manuell auszuführen, beschreibst du deine Services in einer `docker-compose.yml`.

---

### ✅ **5. Sie verstehen den Aufbau einer YAML-Datei und können eigene YAML-Dateien erstellen.**

**YAML** ist eine lesbare Struktur für Konfigurationsdateien. Es verwendet Einrückungen statt Klammern.

#### Beispiel einer einfachen `docker-compose.yml`:

```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "5000:5000"
  db:
    image: postgres
    environment:
      POSTGRES_PASSWORD: geheim123
```

Hier siehst du:
- Zwei Services: `web` und `db`.
- `web` wird aus einem Dockerfile gebaut.
- `db` nutzt ein fertiges Postgres-Image mit Umgebungsvariablen.

---

### ✅ **6. Sie können Docker Compose anwenden.**

#### Starten:

```bash
docker-compose up
```

#### Stoppen:

```bash
docker-compose down -v
```

Vorteile:
- Ein Befehl reicht zum Starten.
- Alles ist versionierbar und reproduzierbar.
- Du kannst mehrere Dienste einfach verknüpfen.

---

### ✅ **7. Sie verstehen, wie in Docker Compose mit Passwörtern oder sonstigen geheimen Informationen umgegangen wird.**

**Nie Passwörter direkt in Compose schreiben**! Besser:

#### Variante A: `.env` Datei

**.env Datei**:
```
POSTGRES_PASSWORD=supersecret
```

**docker-compose.yml**:
```yaml
environment:
  POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
```

#### Variante B: Docker Secrets (besonders im Swarm-Modus)

```yaml
secrets:
  db_password:
    file: ./db_password.txt

services:
  db:
    image: postgres
    secrets:
      - db_password
```

Das ist sicherer, da Secrets nicht im Container als Umgebungsvariablen auftauchen, sondern im Dateisystem sicher abgelegt werden.

---

### Fazit

| Lernziel | Wichtigste Punkte |
|----------|-------------------|
| Dockerfile Syntax | `FROM`, `RUN`, `COPY`, `CMD` verstehen |
| Dockerfiles nachvollziehen | Struktur analysieren |
| Eigene Dockerfiles | Erstellen, testen, kommentieren |
| Docker Compose | Koordination mehrerer Container |
| YAML | Struktur mit Einrückungen, keine Klammern |
| Compose anwenden | `up`, `down`, `build` Befehle |
| Secrets | `.env` oder Docker Secrets nutzen |

---

Wenn du magst, kann ich dir auch kleine Aufgaben zum Üben geben oder dir bei einem Projekt helfen. Lust drauf? 😄
