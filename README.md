# Workshop IoT - Node-RED avec ESP32

<div align="center">

![Node-RED](https://img.shields.io/badge/Node--RED-8F0000?style=for-the-badge&logo=node-red&logoColor=white)
![ESP32](https://img.shields.io/badge/ESP32-000000?style=for-the-badge&logo=espressif&logoColor=white)
![MQTT](https://img.shields.io/badge/MQTT-660066?style=for-the-badge&logo=mqtt&logoColor=white)
![Arduino](https://img.shields.io/badge/Arduino-00979D?style=for-the-badge&logo=arduino&logoColor=white)
![ThingSpeak](https://img.shields.io/badge/ThingSpeak-00629B?style=for-the-badge&logo=mathworks&logoColor=white)
![Blynk](https://img.shields.io/badge/Blynk-04C3FF?style=for-the-badge&logo=blynk&logoColor=white)

</div>

---

## 📋 Table des matières
1. [Introduction](#introduction)
2. [Prérequis](#prérequis)
3. [Installation de Node-RED](#installation-de-node-red)
4. [Configuration ESP32](#configuration-esp32)
5. [Manipulation 1 : MQTT avec Node-RED](#manipulation-1--mqtt-avec-node-red)
6. [Manipulation 2 : HTTP avec Node-RED](#manipulation-2--http-avec-node-red)
7. [Manipulation 3 : Intégration ThingSpeak](#manipulation-3--intégration-thingspeak)
8. [Manipulation 4 : Intégration Blynk](#manipulation-4--intégration-blynk)
9. [Projet Final](#projet-final)
10. [Dépannage](#dépannage)

---

## Introduction

Ce workshop vous guide pas à pas dans l'utilisation de Node-RED pour créer des applications IoT avec un ESP32. Vous apprendrez à :
- Installer et configurer Node-RED
- Communiquer via MQTT et HTTP
- Visualiser des données sur ThingSpeak
- Contrôler des dispositifs avec Blynk

---

## Prérequis

### Matériel
- ESP32 (DevKit ou équivalent)
- Capteur DHT11/DHT22 (température et humidité)
- LED
- Câbles de connexion
- Résistance 220Ω pour LED

### Logiciels

<div align="center">

![Node.js](https://img.shields.io/badge/Node.js-18.x+-339933?style=flat-square&logo=node.js&logoColor=white)
![Arduino IDE](https://img.shields.io/badge/Arduino_IDE-00979D?style=flat-square&logo=arduino&logoColor=white)
![PlatformIO](https://img.shields.io/badge/PlatformIO-FF7F00?style=flat-square&logo=platformio&logoColor=white)

</div>

- Node.js (version 18.x ou supérieure)
- Arduino IDE ou PlatformIO
- Navigateur web moderne

---

## Installation de Node-RED

### Étape 1 : Installer Node.js

![Node.js](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=node.js&logoColor=white)

#### Windows
1. Téléchargez Node.js depuis [nodejs.org](https://nodejs.org/)
2. Exécutez l'installeur
3. Vérifiez l'installation :
```bash
node --version
npm --version
```

#### macOS
```bash
# Avec Homebrew
brew install node
```

#### Linux (Ubuntu/Debian)
```bash
sudo apt update
sudo apt install nodejs npm
```

### Étape 2 : Installer Node-RED

![Node-RED](https://img.shields.io/badge/Node--RED-8F0000?style=for-the-badge&logo=node-red&logoColor=white)

```bash
# Installation globale
npm install -g --unsafe-perm node-red

# Vérifier l'installation
node-red --version
```

### Étape 3 : Lancer Node-RED

```bash
node-red
```

Accédez à Node-RED via votre navigateur : `http://localhost:1880`

### Étape 4 : Installer les nodes complémentaires

Dans Node-RED, allez dans **Menu → Manage palette → Install** et installez :

| Package | Description | Badge |
|---------|-------------|-------|
| `node-red-dashboard` | Interface utilisateur | ![Dashboard](https://img.shields.io/badge/Dashboard-8F0000?style=flat-square&logo=node-red) |
| `node-red-contrib-mqtt-broker` | Broker MQTT local | ![MQTT](https://img.shields.io/badge/MQTT-660066?style=flat-square&logo=mqtt) |
| `node-red-contrib-thingspeak42` | Intégration ThingSpeak | ![ThingSpeak](https://img.shields.io/badge/ThingSpeak-00629B?style=flat-square) |

Ou via ligne de commande :
```bash
cd ~/.node-red
npm install node-red-dashboard node-red-contrib-mqtt-broker node-red-contrib-thingspeak42
```

---

## Configuration ESP32

### Installation des bibliothèques Arduino

![Arduino](https://img.shields.io/badge/Arduino-00979D?style=for-the-badge&logo=arduino&logoColor=white)

Dans Arduino IDE, installez :

| Bibliothèque | Usage | Badge |
|--------------|-------|-------|
| **ESP32 Board** | Support matériel ESP32 | ![ESP32](https://img.shields.io/badge/ESP32-000000?style=flat-square&logo=espressif) |
| **PubSubClient** | Communication MQTT | ![MQTT](https://img.shields.io/badge/MQTT-660066?style=flat-square&logo=mqtt) |
| **DHT sensor library** | Capteur température/humidité | ![DHT](https://img.shields.io/badge/DHT-Sensor-green?style=flat-square) |
| **ArduinoJson** | Manipulation JSON | ![JSON](https://img.shields.io/badge/JSON-000000?style=flat-square&logo=json) |
| **HTTPClient** | Requêtes HTTP | ![HTTP](https://img.shields.io/badge/HTTP-005571?style=flat-square) |
| **WiFi** | Connexion réseau | ![WiFi](https://img.shields.io/badge/WiFi-0078D4?style=flat-square) |
| **Blynk** (optionnel) | Contrôle via Blynk | ![Blynk](https://img.shields.io/badge/Blynk-04C3FF?style=flat-square&logo=blynk) |

### Code ESP32 de base

```cpp
#include <WiFi.h>

// Configuration WiFi
const char* ssid = "VOTRE_SSID";
const char* password = "VOTRE_MOT_DE_PASSE";

void setup() {
  Serial.begin(115200);
  
  // Connexion WiFi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nConnecté au WiFi!");
  Serial.print("Adresse IP: ");
  Serial.println(WiFi.localIP());
}

void loop() {
  // Code principal
}
```

## Manipulation 1 : MQTT avec Node-RED

![MQTT](https://img.shields.io/badge/MQTT-660066?style=for-the-badge&logo=mqtt&logoColor=white)
![Mosquitto](https://img.shields.io/badge/Mosquitto-3C5280?style=for-the-badge&logo=eclipsemosquitto&logoColor=white)

### Objectif
Envoyer des données de température depuis l'ESP32 vers Node-RED via MQTT et afficher sur un dashboard.

### Étape 1 : Installation du broker MQTT (Mosquitto)

#### Option A : Installation locale de Mosquitto

**Windows**

![Windows](https://img.shields.io/badge/Windows-0078D6?style=flat-square&logo=windows&logoColor=white)

1. Téléchargez Mosquitto depuis [mosquitto.org/download](https://mosquitto.org/download/)
2. Exécutez l'installeur `mosquitto-xxx-install-windows-x64.exe`
3. Ajoutez Mosquitto au PATH système
4. Créez le fichier de configuration `C:\Program Files\mosquitto\mosquitto.conf` :
```conf
listener 1883
allow_anonymous true
```
5. Démarrez le service :
```cmd
net start mosquitto
```

**macOS**

![macOS](https://img.shields.io/badge/macOS-000000?style=flat-square&logo=apple&logoColor=white)
```bash
brew install mosquitto
brew services start mosquitto
```

**Linux**

![Linux](https://img.shields.io/badge/Linux-FCC624?style=flat-square&logo=linux&logoColor=black)
```bash
sudo apt update
sudo apt install mosquitto mosquitto-clients
sudo systemctl start mosquitto
sudo systemctl enable mosquitto
```

#### Option B : Brokers MQTT en ligne (Sans installation)

| Broker | Adresse | Port | Badge |
|--------|---------|------|-------|
| Eclipse Mosquitto | `test.mosquitto.org` | 1883 | ![Mosquitto](https://img.shields.io/badge/Mosquitto-3C5280?style=flat-square&logo=eclipsemosquitto) |
| HiveMQ | `broker.hivemq.com` | 1883 | ![HiveMQ](https://img.shields.io/badge/HiveMQ-FFC000?style=flat-square) |
| EMQX | `broker.emqx.io` | 1883 | ![EMQX](https://img.shields.io/badge/EMQX-00B17E?style=flat-square) |

⚠️ **Attention** : Ces brokers sont publics, n'envoyez pas de données sensibles !

### Étape 2 : Choix de la carte microcontrôleur

#### Option A : ESP32

![ESP32](https://img.shields.io/badge/ESP32-000000?style=flat-square&logo=espressif&logoColor=white)

**Bibliothèques requises :**
```cpp
#include <WiFi.h>          // Communication WiFi
#include <PubSubClient.h>  // Client MQTT
#include <DHT.h>           // Capteur DHT
```

**Code complet :**
```cpp
#include <WiFi.h>
#include <PubSubClient.h>
#include <DHT.h>

// Configuration WiFi
const char* ssid = "VOTRE_SSID";
const char* password = "VOTRE_MOT_DE_PASSE";

// Configuration MQTT
const char* mqtt_server = "192.168.1.100"; // IP de votre PC
const int mqtt_port = 1883;
const char* mqtt_topic_temp = "esp32/temperature";
const char* mqtt_topic_hum = "esp32/humidite";
const char* mqtt_topic_led = "esp32/led";

// Configuration DHT
#define DHTPIN 4
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);

// Configuration LED
#define LED_PIN 2

WiFiClient espClient;
PubSubClient client(espClient);

void setup_wifi() {
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nConnecté!");
}

void callback(char* topic, byte* payload, unsigned int length) {
  String message = "";
  for (int i = 0; i < length; i++) {
    message += (char)payload[i];
  }
  
  if (String(topic) == mqtt_topic_led) {
    if (message == "ON") {
      digitalWrite(LED_PIN, HIGH);
    } else {
      digitalWrite(LED_PIN, LOW);
    }
  }
}

void reconnect() {
  while (!client.connected()) {
    if (client.connect("ESP32Client")) {
      client.subscribe(mqtt_topic_led);
    } else {
      delay(5000);
    }
  }
}

void setup() {
  Serial.begin(115200);
  pinMode(LED_PIN, OUTPUT);
  dht.begin();
  setup_wifi();
  client.setServer(mqtt_server, mqtt_port);
  client.setCallback(callback);
}

void loop() {
  if (!client.connected()) {
    reconnect();
  }
  client.loop();
  
  static unsigned long lastMsg = 0;
  unsigned long now = millis();
  if (now - lastMsg > 5000) {
    lastMsg = now;
    
    float temp = dht.readTemperature();
    float hum = dht.readHumidity();
    
    if (!isnan(temp) && !isnan(hum)) {
      client.publish(mqtt_topic_temp, String(temp).c_str());
      client.publish(mqtt_topic_hum, String(hum).c_str());
      Serial.println("Données envoyées!");
    }
  }
}
```

#### Option B : LOLIN(WEMOS) D1 R2 & mini

![WEMOS](https://img.shields.io/badge/WEMOS-00979D?style=flat-square&logo=arduino&logoColor=white)

**Bibliothèques requises :**
```cpp
#include <ESP8266WiFi.h>   // Communication WiFi pour ESP8266
#include <PubSubClient.h>  // Client MQTT
#include <DHT.h>           // Capteur DHT
```

**Code complet :**
```cpp
#include <ESP8266WiFi.h>
#include <PubSubClient.h>
#include <DHT.h>

// Configuration WiFi
const char* ssid = "VOTRE_SSID";
const char* password = "VOTRE_MOT_DE_PASSE";

// Configuration MQTT
const char* mqtt_server = "192.168.1.100"; // IP de votre PC
const int mqtt_port = 1883;
const char* mqtt_topic_temp = "wemos/temperature";
const char* mqtt_topic_hum = "wemos/humidite";
const char* mqtt_topic_led = "wemos/led";

// Configuration DHT
#define DHTPIN D4
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);

// Configuration LED
#define LED_PIN D2

WiFiClient espClient;
PubSubClient client(espClient);

void setup_wifi() {
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nConnecté!");
}

void callback(char* topic, byte* payload, unsigned int length) {
  String message = "";
  for (int i = 0; i < length; i++) {
    message += (char)payload[i];
  }
  
  if (String(topic) == mqtt_topic_led) {
    if (message == "ON") {
      digitalWrite(LED_PIN, HIGH);
    } else {
      digitalWrite(LED_PIN, LOW);
    }
  }
}

void reconnect() {
  while (!client.connected()) {
    if (client.connect("WemosClient")) {
      client.subscribe(mqtt_topic_led);
    } else {
      delay(5000);
    }
  }
}

void setup() {
  Serial.begin(115200);
  pinMode(LED_PIN, OUTPUT);
  dht.begin();
  setup_wifi();
  client.setServer(mqtt_server, mqtt_port);
  client.setCallback(callback);
}

void loop() {
  if (!client.connected()) {
    reconnect();
  }
  client.loop();
  
  static unsigned long lastMsg = 0;
  unsigned long now = millis();
  if (now - lastMsg > 5000) {
    lastMsg = now;
    
    float temp = dht.readTemperature();
    float hum = dht.readHumidity();
    
    if (!isnan(temp) && !isnan(hum)) {
      client.publish(mqtt_topic_temp, String(temp).c_str());
      client.publish(mqtt_topic_hum, String(hum).c_str());
      Serial.println("Données envoyées!");
    }
  }
}
```

### Étape 3 : Installation et configuration de Node-RED

#### Installation de Node-RED

**Windows**

![Windows](https://img.shields.io/badge/Windows-0078D6?style=flat-square&logo=windows&logoColor=white)
```cmd
npm install -g --unsafe-perm node-red
```

**macOS / Linux**

![macOS](https://img.shields.io/badge/macOS-000000?style=flat-square&logo=apple&logoColor=white) ![Linux](https://img.shields.io/badge/Linux-FCC624?style=flat-square&logo=linux&logoColor=black)
```bash
sudo npm install -g --unsafe-perm node-red
```

#### Démarrage de Node-RED
```bash
node-red
```

Ouvrez votre navigateur et accédez à : `http://localhost:1880`

#### Installation du dashboard

Dans Node-RED, cliquez sur le menu (☰) → Manage palette → Install, puis recherchez et installez :
```
node-red-dashboard
```

### Étape 4 : Configuration du flux Node-RED

#### Création du flux MQTT

1. Glissez les nœuds suivants depuis la palette vers le workspace :
   - 2x `mqtt in` (pour température et humidité)
   - 2x `gauge` (jauge d'affichage)
   - 2x `chart` (graphique)
   - 1x `switch` (interrupteur pour LED)
   - 1x `mqtt out` (pour contrôler la LED)

2. **Configuration du nœud MQTT In (Température)** :
   - Double-cliquez sur le nœud `mqtt in`
   - Server : Cliquez sur l'icône crayon pour ajouter un nouveau broker
   - Server : `localhost` (ou IP de votre broker)
   - Port : `1883`
   - Topic : `esp32/temperature` (ou `wemos/temperature`)
   - QoS : `0`
   - Name : `Température`

3. **Configuration du nœud MQTT In (Humidité)** :
   - Server : Utilisez le même broker configuré précédemment
   - Topic : `esp32/humidite` (ou `wemos/humidite`)
   - Name : `Humidité`

4. **Configuration du nœud Gauge (Température)** :
   - Group : Créez un nouveau groupe `[Home] Capteurs`
   - Label : `Température`
   - Units : `°C`
   - Range : min `0`, max `50`
   - Sectors : `0-20-30-50`

5. **Configuration du nœud Gauge (Humidité)** :
   - Group : `[Home] Capteurs`
   - Label : `Humidité`
   - Units : `%`
   - Range : min `0`, max `100`

6. **Configuration du nœud Chart (Température)** :
   - Group : `[Home] Capteurs`
   - Label : `Historique Température`
   - X-axis : dernières `1` heure
   - Y-axis : min `0`, max `50`

7. **Configuration du nœud Chart (Humidité)** :
   - Group : `[Home] Capteurs`
   - Label : `Historique Humidité`
   - X-axis : dernières `1` heure
   - Y-axis : min `0`, max `100`

8. **Configuration du nœud Switch (LED)** :
   - Group : Créez un nouveau groupe `[Home] Contrôle`
   - Label : `LED`
   - On Payload : `ON`
   - Off Payload : `OFF`

9. **Configuration du nœud MQTT Out (LED)** :
   - Server : Même broker
   - Topic : `esp32/led` (ou `wemos/led`)
   - QoS : `0`
   - Name : `Contrôle LED`

#### Connexion des nœuds

Connectez les nœuds dans cet ordre :
```
mqtt in (Température) → gauge (Température)
                     → chart (Température)

mqtt in (Humidité) → gauge (Humidité)
                   → chart (Humidité)

switch (LED) → mqtt out (LED)
```

#### Déploiement

Cliquez sur le bouton rouge `Deploy` en haut à droite.

### Étape 5 : Accès au Dashboard

Accédez au dashboard via : `http://localhost:1880/ui`

Vous devriez voir :
- Une jauge de température en temps réel
- Une jauge d'humidité en temps réel
- Des graphiques historiques
- Un interrupteur pour contrôler la LED

### Vérification

1. Ouvrez le moniteur série de l'Arduino IDE (115200 baud)
2. Vérifiez la connexion WiFi et MQTT
3. Observez l'envoi des données toutes les 5 secondes
4. Testez l'interrupteur LED depuis le dashboard
## Manipulation 2 : HTTP avec Node-RED

![HTTP](https://img.shields.io/badge/HTTP-005571?style=for-the-badge&logo=http&logoColor=white)
![JSON](https://img.shields.io/badge/JSON-000000?style=for-the-badge&logo=json&logoColor=white)

### Objectif
Créer une API HTTP pour recevoir et envoyer des données à l'ESP32.

### Étape 1 : Choix de la carte microcontrôleur

#### Option A : ESP32

![ESP32](https://img.shields.io/badge/ESP32-000000?style=flat-square&logo=espressif&logoColor=white)

**Bibliothèques requises :**
```cpp
#include           // Communication WiFi
#include     // Client HTTP
#include            // Capteur DHT
#include    // Manipulation JSON (optionnel)
```

**Code complet :**
```cpp
#include 
#include 
#include 

const char* ssid = "VOTRE_SSID";
const char* password = "VOTRE_MOT_DE_PASSE";

// URL de votre Node-RED
const char* serverURL = "http://192.168.1.100:1880/data";

#define DHTPIN 4
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(115200);
  dht.begin();
  
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nConnecté!");
}

void loop() {
  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;
    
    float temp = dht.readTemperature();
    float hum = dht.readHumidity();
    
    if (!isnan(temp) && !isnan(hum)) {
      String jsonData = "{\"temperature\":" + String(temp) + 
                       ",\"humidite\":" + String(hum) + "}";
      
      http.begin(serverURL);
      http.addHeader("Content-Type", "application/json");
      
      int httpCode = http.POST(jsonData);
      
      if (httpCode > 0) {
        String response = http.getString();
        Serial.println("Réponse: " + response);
      } else {
        Serial.println("Erreur HTTP");
      }
      
      http.end();
    }
  }
  
  delay(5000);
}
```

#### Option B : LOLIN(WEMOS) D1 R2 & mini

![WEMOS](https://img.shields.io/badge/WEMOS-00979D?style=flat-square&logo=arduino&logoColor=white)

**Bibliothèques requises :**
```cpp
#include        // Communication WiFi pour ESP8266
#include  // Client HTTP pour ESP8266
#include         // Client WiFi
#include                // Capteur DHT
#include        // Manipulation JSON (optionnel)
```

**Code complet :**
```cpp
#include 
#include 
#include 
#include 

const char* ssid = "VOTRE_SSID";
const char* password = "VOTRE_MOT_DE_PASSE";

// URL de votre Node-RED
const char* serverURL = "http://192.168.1.100:1880/data";

#define DHTPIN D4
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);

WiFiClient wifiClient;

void setup() {
  Serial.begin(115200);
  dht.begin();
  
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nConnecté!");
}

void loop() {
  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;
    
    float temp = dht.readTemperature();
    float hum = dht.readHumidity();
    
    if (!isnan(temp) && !isnan(hum)) {
      String jsonData = "{\"temperature\":" + String(temp) + 
                       ",\"humidite\":" + String(hum) + "}";
      
      http.begin(wifiClient, serverURL);
      http.addHeader("Content-Type", "application/json");
      
      int httpCode = http.POST(jsonData);
      
      if (httpCode > 0) {
        String response = http.getString();
        Serial.println("Réponse: " + response);
      } else {
        Serial.println("Erreur HTTP");
      }
      
      http.end();
    }
  }
  
  delay(5000);
}
```

### Étape 2 : Configuration de Node-RED pour HTTP

#### Installation de Node-RED

Si vous n'avez pas encore installé Node-RED, suivez ces étapes :

**Windows**

![Windows](https://img.shields.io/badge/Windows-0078D6?style=flat-square&logo=windows&logoColor=white)
```cmd
npm install -g --unsafe-perm node-red
```

**macOS / Linux**

![macOS](https://img.shields.io/badge/macOS-000000?style=flat-square&logo=apple&logoColor=white) ![Linux](https://img.shields.io/badge/Linux-FCC624?style=flat-square&logo=linux&logoColor=black)
```bash
sudo npm install -g --unsafe-perm node-red
```

#### Démarrage de Node-RED
```bash
node-red
```

Ouvrez votre navigateur et accédez à : `http://localhost:1880`

#### Installation du dashboard

Dans Node-RED, cliquez sur le menu (☰) → Manage palette → Install, puis recherchez et installez :
```
node-red-dashboard
```

### Étape 3 : Création du flux Node-RED HTTP

#### Configuration de l'API de réception

1. Glissez les nœuds suivants depuis la palette vers le workspace :
   - 1x `http in` (pour recevoir les données)
   - 1x `json` (pour parser le JSON)
   - 1x `function` (pour séparer température et humidité)
   - 2x `gauge` (jauge d'affichage)
   - 2x `chart` (graphique)
   - 1x `http response` (pour répondre à l'ESP32)
   - 1x `debug` (pour afficher les données reçues)

2. **Configuration du nœud HTTP In** :
   - Double-cliquez sur le nœud `http in`
   - Method : `POST`
   - URL : `/data`
   - Name : `Réception données`

3. **Configuration du nœud JSON** :
   - Action : `Convert between JSON String & Object`
   - Name : `Parser JSON`

4. **Configuration du nœud Function** :
   - Name : `Extraire données`
   - Code :
```javascript
   // Extraire température et humidité
   var temp = msg.payload.temperature;
   var hum = msg.payload.humidite;
   
   // Créer deux messages de sortie
   var msg1 = {payload: temp, topic: "temperature"};
   var msg2 = {payload: hum, topic: "humidite"};
   
   return [msg1, msg2];
```
   - Outputs : `2`

5. **Configuration du nœud Gauge (Température)** :
   - Group : Créez un nouveau groupe `[Home] Capteurs HTTP`
   - Label : `Température`
   - Units : `°C`
   - Range : min `0`, max `50`
   - Sectors : `0-20-30-50`

6. **Configuration du nœud Gauge (Humidité)** :
   - Group : `[Home] Capteurs HTTP`
   - Label : `Humidité`
   - Units : `%`
   - Range : min `0`, max `100`

7. **Configuration du nœud Chart (Température)** :
   - Group : `[Home] Capteurs HTTP`
   - Label : `Historique Température`
   - X-axis : dernières `1` heure
   - Y-axis : min `0`, max `50`

8. **Configuration du nœud Chart (Humidité)** :
   - Group : `[Home] Capteurs HTTP`
   - Label : `Historique Humidité`
   - X-axis : dernières `1` heure
   - Y-axis : min `0`, max `100`

9. **Configuration du nœud HTTP Response** :
   - Status code : `200`
   - Name : `Réponse OK`

10. **Configuration du nœud Debug** :
    - Output : `complete msg object`
    - Name : `Afficher données`

#### Connexion des nœuds

Connectez les nœuds dans cet ordre :
```
http in → json → function → [sortie 1] → gauge (Température)
                          |            → chart (Température)
                          |
                          → [sortie 2] → gauge (Humidité)
                                      → chart (Humidité)

http in → json → debug
              → http response
```

#### Configuration API de contrôle LED (Optionnel)

Pour contrôler une LED depuis Node-RED :

1. Ajoutez ces nœuds :
   - 1x `switch` (interrupteur dashboard)
   - 1x `function` (pour créer l'URL)
   - 1x `http request` (pour envoyer la commande)

2. **Configuration du nœud Switch** :
   - Group : `[Home] Contrôle HTTP`
   - Label : `LED`
   - On Payload : `1`
   - Off Payload : `0`

3. **Configuration du nœud Function** :
   - Name : `Créer URL LED`
   - Code :
```javascript
   msg.url = "http://192.168.1.X/led?state=" + msg.payload;
   return msg;
```

4. **Configuration du nœud HTTP Request** :
   - Method : `GET`
   - Return : `a UTF-8 string`
   - Name : `Envoyer commande`

5. Connectez : `switch → function → http request`

#### Code ESP32/WEMOS pour le serveur LED (Optionnel)

**ESP32 :**
```cpp
#include 
#include 

WebServer server(80);
#define LED_PIN 2

void handleLED() {
  String state = server.arg("state");
  if (state == "1") {
    digitalWrite(LED_PIN, HIGH);
  } else {
    digitalWrite(LED_PIN, LOW);
  }
  server.send(200, "text/plain", "OK");
}

void setup() {
  pinMode(LED_PIN, OUTPUT);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) delay(500);
  
  server.on("/led", handleLED);
  server.begin();
}

void loop() {
  server.handleClient();
}
```

**WEMOS :**
```cpp
#include 
#include 

ESP8266WebServer server(80);
#define LED_PIN D2

void handleLED() {
  String state = server.arg("state");
  if (state == "1") {
    digitalWrite(LED_PIN, HIGH);
  } else {
    digitalWrite(LED_PIN, LOW);
  }
  server.send(200, "text/plain", "OK");
}

void setup() {
  pinMode(LED_PIN, OUTPUT);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) delay(500);
  
  server.on("/led", handleLED);
  server.begin();
}

void loop() {
  server.handleClient();
}
```

#### Déploiement

Cliquez sur le bouton rouge `Deploy` en haut à droite.

### Étape 4 : Trouver l'adresse IP de votre PC

Pour que l'ESP32/WEMOS puisse envoyer des données à Node-RED, vous devez utiliser l'adresse IP de votre PC.

**Windows**
```cmd
ipconfig
```

Cherchez `IPv4 Address` dans la section WiFi ou Ethernet.

**macOS / Linux**
```bash
ifconfig
```

ou
```bash
ip addr show
```

Remplacez `192.168.1.100` dans le code par votre adresse IP réelle.

### Étape 5 : Accès au Dashboard

Accédez au dashboard via : `http://localhost:1880/ui`

Vous devriez voir :
- Une jauge de température en temps réel
- Une jauge d'humidité en temps réel
- Des graphiques historiques
- Un interrupteur pour contrôler la LED (si configuré)

### Vérification

1. Ouvrez le moniteur série de l'Arduino IDE (115200 baud)
2. Vérifiez la connexion WiFi
3. Observez l'envoi des requêtes HTTP toutes les 5 secondes
4. Vérifiez les réponses du serveur Node-RED
5. Consultez l'onglet Debug dans Node-RED pour voir les données reçues

## Manipulation 3 : Intégration ThingSpeak

![ThingSpeak](https://img.shields.io/badge/ThingSpeak-00629B?style=for-the-badge&logo=mathworks&logoColor=white)
![MATLAB](https://img.shields.io/badge/MATLAB-0076A8?style=for-the-badge&logo=mathworks&logoColor=white)

### Étape 1 : Créer un canal ThingSpeak

1. Créez un compte sur [thingspeak.com](https://thingspeak.com/)
2. Créez un nouveau canal avec 2 champs :
   - Field 1 : Temperature
   - Field 2 : Humidite
3. Notez votre **Write API Key**

### Code ESP32 vers ThingSpeak

**Bibliothèques requises :**

```cpp
#include <WiFi.h>          // Communication WiFi
#include <HTTPClient.h>    // Client HTTP pour API ThingSpeak
#include <DHT.h>           // Capteur DHT
```

**Code complet :**

```cpp
#include <WiFi.h>
#include <HTTPClient.h>
#include <DHT.h>

const char* ssid = "VOTRE_SSID";
const char* password = "VOTRE_MOT_DE_PASSE";

String apiKey = "VOTRE_WRITE_API_KEY";
const char* server = "http://api.thingspeak.com/update";

#define DHTPIN 4
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);

void sendToThingSpeak(float temp, float hum) {
  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;
    
    String url = String(server) + "?api_key=" + apiKey + 
                 "&field1=" + String(temp) + 
                 "&field2=" + String(hum);
    
    http.begin(url);
    int httpCode = http.GET();
    
    if (httpCode > 0) {
      Serial.println("Données envoyées à ThingSpeak!");
    }
    
    http.end();
  }
}

void setup() {
  Serial.begin(115200);
  dht.begin();
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
  }
}

void loop() {
  float temp = dht.readTemperature();
  float hum = dht.readHumidity();
  
  if (!isnan(temp) && !isnan(hum)) {
    sendToThingSpeak(temp, hum);
  }
  
  delay(20000); // ThingSpeak limite : 15 secondes minimum
}
```

---

## Manipulation 4 : Intégration Blynk

![Blynk](https://img.shields.io/badge/Blynk-04C3FF?style=for-the-badge&logo=blynk&logoColor=white)

### Étape 1 : Configuration Blynk

1. Téléchargez l'application **Blynk IoT** (iOS/Android)
2. Créez un nouveau template avec :
   - Datastream V0 : Temperature (Double, 0-50)
   - Datastream V1 : Humidite (Double, 0-100)
   - Datastream V2 : LED (Integer, 0-1)
3. Notez votre **Template ID** et **Auth Token**

### Code ESP32 avec Blynk

**Bibliothèques requises :**

```cpp
#include <WiFi.h>              // Communication WiFi
#include <BlynkSimpleEsp32.h>  // Bibliothèque Blynk pour ESP32
#include <DHT.h>               // Capteur DHT
```

**Code complet :**

```cpp
#define BLYNK_TEMPLATE_ID "VOTRE_TEMPLATE_ID"
#define BLYNK_TEMPLATE_NAME "ESP32 IoT"
#define BLYNK_AUTH_TOKEN "VOTRE_AUTH_TOKEN"

#include <WiFi.h>
#include <BlynkSimpleEsp32.h>
#include <DHT.h>

char auth[] = BLYNK_AUTH_TOKEN;
char ssid[] = "VOTRE_SSID";
char pass[] = "VOTRE_MOT_DE_PASSE";

#define DHTPIN 4
#define DHTTYPE DHT11
#define LED_PIN 2

DHT dht(DHTPIN, DHTTYPE);
BlynkTimer timer;

BLYNK_WRITE(V2) {
  int ledState = param.asInt();
  digitalWrite(LED_PIN, ledState);
}

void sendSensorData() {
  float temp = dht.readTemperature();
  float hum = dht.readHumidity();
  
  if (!isnan(temp) && !isnan(hum)) {
    Blynk.virtualWrite(V0, temp);
    Blynk.virtualWrite(V1, hum);
  }
}

void setup() {
  Serial.begin(115200);
  pinMode(LED_PIN, OUTPUT);
  dht.begin();
  
  Blynk.begin(auth, ssid, pass);
  
  timer.setInterval(5000L, sendSensorData);
}

void loop() {
  Blynk.run();
  timer.run();
}
```

---

## Projet Final

### Système de Monitoring Complet

![Architecture](https://img.shields.io/badge/Architecture-IoT-blue?style=for-the-badge)

**Stack technologique complet :**

<div align="center">

| Composant | Technologie | Badge |
|-----------|-------------|-------|
| Microcontrôleur | ESP32 | ![ESP32](https://img.shields.io/badge/ESP32-000000?style=flat-square&logo=espressif) |
| Protocole temps réel | MQTT | ![MQTT](https://img.shields.io/badge/MQTT-660066?style=flat-square&logo=mqtt) |
| API REST | HTTP/JSON | ![HTTP](https://img.shields.io/badge/HTTP-005571?style=flat-square) |
| Middleware | Node-RED | ![Node-RED](https://img.shields.io/badge/Node--RED-8F0000?style=flat-square&logo=node-red) |
| Visualisation Cloud | ThingSpeak | ![ThingSpeak](https://img.shields.io/badge/ThingSpeak-00629B?style=flat-square) |
| Contrôle Mobile | Blynk | ![Blynk](https://img.shields.io/badge/Blynk-04C3FF?style=flat-square&logo=blynk) |

</div>

**Architecture système :**

```
ESP32 (WiFi + DHT + LED)
  │
  ├── MQTT ──────────► Node-RED Dashboard
  │                      │
  ├── HTTP ──────────────┤
  │                      │
  ├── ThingSpeak API ────┴──► Graphiques Cloud
  │
  └── Blynk IoT ─────────────► Application Mobile
```

---

## Dépannage

### Problèmes courants et solutions

| Problème | Solution | Badge |
|----------|----------|-------|
| Node-RED ne démarre pas | `node-red --verbose` | ![Debug](https://img.shields.io/badge/Debug-FF0000?style=flat-square) |
| ESP32 WiFi échoue | Vérifier SSID 2.4GHz | ![WiFi](https://img.shields.io/badge/WiFi-0078D4?style=flat-square) |
| MQTT non connecté | Tester avec `mosquitto_sub` | ![MQTT](https://img.shields.io/badge/MQTT-660066?style=flat-square&logo=mqtt) |
| HTTP timeout | Vérifier IP et firewall | ![Network](https://img.shields.io/badge/Network-005571?style=flat-square) |

---

## Récapitulatif des bibliothèques

### Pour ESP32 (Arduino IDE)

![Arduino](https://img.shields.io/badge/Arduino_Libraries-00979D?style=for-the-badge&logo=arduino&logoColor=white)

| Bibliothèque | Utilisation | Installation |
|--------------|-------------|--------------|
| ![](https://img.shields.io/badge/WiFi-0078D4?style=flat-square) | Connexion réseau | Incluse avec ESP32 |
| ![](https://img.shields.io/badge/PubSubClient-660066?style=flat-square) | MQTT | Library Manager |
| ![](https://img.shields.io/badge/HTTPClient-005571?style=flat-square) | Requêtes HTTP | Incluse avec ESP32 |
| ![](https://img.shields.io/badge/DHT-green?style=flat-square) | Capteur DHT11/22 | Library Manager |
| ![](https://img.shields.io/badge/ArduinoJson-000000?style=flat-square) | JSON | Library Manager |
| ![](https://img.shields.io/badge/Blynk-04C3FF?style=flat-square) | Blynk IoT | Library Manager |

### Pour Node-RED

![Node-RED](https://img.shields.io/badge/Node--RED_Packages-8F0000?style=for-the-badge&logo=node-red&logoColor=white)

| Package | Description | Commande |
|---------|-------------|----------|
| ![](https://img.shields.io/badge/Dashboard-8F0000?style=flat-square) | Interface UI | `npm install node-red-dashboard` |
| ![](https://img.shields.io/badge/MQTT-660066?style=flat-square) | Broker MQTT | `npm install node-red-contrib-mqtt-broker` |
| ![](https://img.shields.io/badge/ThingSpeak-00629B?style=flat-square) | ThingSpeak | `npm install node-red-contrib-thingspeak42` |

---

## Ressources

<div align="center">

[![Node-RED Docs](https://img.shields.io/badge/Docs-Node--RED-8F0000?style=for-the-badge&logo=node-red)](https://nodered.org/docs/)
[![ESP32 Docs](https://img.shields.io/badge/Docs-ESP32-000000?style=for-the-badge&logo=espressif)](https://docs.espressif.com/)
[![MQTT Tutorial](https://img.shields.io/badge/Tutorial-MQTT-660066?style=for-the-badge&logo=mqtt)](https://mqtt.org/getting-started/)
[![ThingSpeak](https://img.shields.io/badge/Docs-ThingSpeak-00629B?style=for-the-badge)](https://www.mathworks.com/help/thingspeak/)
[![Blynk](https://img.shields.io/badge/Docs-Blynk-04C3FF?style=for-the-badge&logo=blynk)](https://docs.blynk.io/)

</div>

---


<div align="center">

**Made with** MOUMAD Abdelghafour , EL HILALI ASMAE , Bado jean pierre oloma **for IoT enthusiasts**

![ESP32](https://img.shields.io/badge/ESP32-Powered-000000?style=flat-square&logo=espressif)
![Node-RED](https://img.shields.io/badge/Built_with-Node--RED-8F0000?style=flat-square&logo=node-red)
![MQTT](https://img.shields.io/badge/Protocol-MQTT-660066?style=flat-square&logo=mqtt)

</div>
