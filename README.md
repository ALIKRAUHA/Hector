# Hector - PathLED - Design Info 





## PathLED un code d'animation de Smart LED WS2813 complet


Le programme PathLED est un code embarqué permettant de gérer des animations lumineuses basé sur les Smart-LED WS281x/xx  multi-clients  piloté à l'aide des µC à très faible coût les ESP8266 - ESP-01 

PathLED est une solution d’amination lumineuse très bon marché contrôlable par Internet basée sur le célèbre microcontrôleur ESP8266 dans une version simplifié, épurer dans sa version ESP-01 en interaction avec les LED intelligente sur bus de commande WS2813. 
PathLED dispose d'une multitude d'entrées de contrôle pour piloter plus de 60 effets d'animations dont :

 - Une interface web, 
 - Une interface REST-API 
 - Un connecteur websocket.

Grâce à son architecture ouverte et ses API, il est facile de créer de nouveaux clients pour différentes plates-formes (iOS, Android, Windows Universal Apps, intégration Siri/Cortana,...).


### Installation du logiciel
Vous pouvez lire comment commencer à nouveau du côté logiciel de ce projet dans notre wiki : Installation du logiciel

### Les bibliothèques utilisées
Ce projet utilise des bibliothèques et du code de différents auteurs :
Absolument nécessaires : 

 - [WiFiManager](https://github.com/tzapu/WiFiManager) de tzapu (testé avec la version 0.12.0)
 - [Voir egalement le branche `Developpement` de la Bib `WifiManager` compatible avec  l'ESP32](https://github.com/tzapu/WiFiManager/tree/development)

 - [WS2812FX](https://github.com/kitesurfer1404/WS2812FX) par kitesurfer1404 (testé avec la version téléchargée 2017-02-05)

 - [WebSockets](https://github.com/Links2004/arduinoWebSockets) par Links2004 (testé avec la version 2.0.6)

 - [Adafruit NeoPixel](https://github.com/adafruit/Adafruit_NeoPixel) par adafruit (testé avec 1.1.2)

Facultatif : 
 - [PubSubClient](https://github.com/knolleary/pubsubclient/) par collinaire (testé avec 2.6.0) Nécessaire uniquement si l'on activé MQTT dans les paramétrages de PathLED 



Certaines parties du code ont été prises ou inspirées par les sources suivantes :

 - [HSB3RGB conversion](https://blog.adafruit.com/2012/03/14/constant-brightness-hsb-to-rgb-algorithm/)

 - [TV simulator](https://github.com/BulldogLowell/PhoneyTV) Logique de simulateur TV inspirée de BulldogLowell

 - [SPIFFS Webserver](https://github.com/esp8266/Arduino/tree/master/libraries/ESP8266WebServer/examples/FSBrowser) Serveur Web SPIFFS par Hristo Gochkov




### Intégration avec l'EDI `Platformio` sous `Visual Studio Code` sur Linux

Installation et compilation de PathLED avec PlatformIO, puis téléversement du projet/firmware/code sur l'ESP8266 - ESP-01. 
Dans mon cas j'utilise EDI [`Visual Studio Code`](https://code.visualstudio.com/) sous Linux ([Opensuse Tumbleweed](https://fr.opensuse.org/Portal:Tumbleweed)) comme environnement de développement.
[`PlateformIO`](https://platformio.org/) est un écosystème open source pour le développement de l'IdO (IoT) et prend en charge toutes les dépendances logicielles de PathLED `Visual Studio Code` est un environnement de développement extrêmement puissant et rapide pour de nombreuses applications. Il est plus confortable que l'EDI Arduino classique, il assiste considérablement le développement de code des objets connecter, si complété du fabuleux module [`PlateformIO`](https://platformio.org/). 
L'ensemble `Visual Studio Code` + `PlateformIO` est libre.


### Installation et intégration de MQTT


 - [Comment installer le serveur MQTT Mosquitto](https://www.acmesystems.it/mosquitto)
 - [Bien débuter avec le Broker MQTT Mosquitto sur Raspberry Pi (fr)](https://projetsdiy.fr/mosquitto-broker-mqtt-raspberry-pi/)
 - [Tutoriel : IOT / Installation et test du Mosquitto MQTT sur le Raspberry Pi](https://www.switchdoc.com/2018/02/tutorial-installing-and-testing-mosquitto-mqtt-on-raspberry-pi/)

 - [Authentification par nom d'utilisateur et mot de passe sur Mosquitto - Configuration et test](http://www.steves-internet-guide.com/mqtt-username-password-example/)
 - [Utilisation de MQTT sur WebSockets avec Mosquitto](http://www.steves-internet-guide.com/mqtt-websockets/)
 - [Activer WebSockets pour un serveur Mosquitto](https://subscription.packtpub.com/book/application_development/9781787287815/5/ch05lvl1sec64/enabling-websockets-for-a-mosquitto-server)

#### Client MQTT 

 - [MQTT.fx](https://mqttfx.jensd.de/index.php)
   MQTT.fx est un client MQTT écrit en Java basé sur [Eclipse Paho](http://www.eclipse.org/paho/) .

### Installation et intégration de Home Assistant - HA

 - [Installation de Home Assistant](https://www.home-assistant.io/getting-started/)
    - [Installation manuelle sur un Raspberry Pi](https://www.home-assistant.io/docs/installation/raspberry-pi/)
    - [Installation on a Armbian system](https://www.home-assistant.io/docs/installation/armbian/) sur mes Orange-Pi
    - [Installation dans un environnement virtuel Python](https://www.home-assistant.io/docs/installation/virtualenv/)



#### Intégration de Home Assistant dans le code ESP-01 / LedPath-Hector


Dans le code actuel l'inter-co pour le support du projet de domotique `Home Assistant` est pré-défini et activé par défaut. Via les variables globales définie dans le fichier de configuration `config.h`  

```cpp

#define ENABLE_HOMEASSISTANT

```
On ajuste au besoin le support ou non de `Home Assistant`
Pour ce faire on passe les instructions via la bibliothèque [ArduinoJSON](https://arduinojson.org/), A installer dans votre EDI si ce n'est pas PlatformIO/Visual-Code (détection et installation automatique dans ce cas) 

#### Interactions Segment de LED's <-> Messagerie `MQTT` <-> Serveur de domotique `Home Assistant`
Les interactions entre chaque segment lumineux (le µControleur ESP-01 de chaque segment) et le serveur domtique `Home Assistant' se font via des canaux (topics) de communication MQTT. 

On utilise dans cette situation deux canaux (Topics) MQTT (Commande (Cmd) / Etat (Eta) pour dialoguer avec l'application `Home Assistant`
 - **hector/ledpath_Sxx_ha/cmd**
 - **hector/ledpath_Sxx_ha/eta**
 
 Où  dans ledpath_xx_ha ; 
   - xx désigne le numéro du segment de LED
   - ha est le post-fixe indiquant un canal dédié au message de correspondance avec le service de domotique `Home Assistant` 

Exemple du message JSON adapter au canal de commande (Cmd)

```json
{
	"Animation": "Arc-en-ciel"
	"Brillance": 155,
	"Couleur": {
		"r":  250,
		"v": 120,
		"b": 50
	},
	"Vitesse": 200,
	"Mired": 22,
	"pilot": "ON"
}
```

Définition des paramètres du canal de Commande (Cmd)

- Animation :  Le nom de l'animation sollicité. La liste des animations est définie dans le fichier Anim.yaml  
 - Brillance : puissance lumineuse ; plage de valeur de 0 à 255
 - Couleur : Définie la couleur de l'animation pour chaque teinte 
	 - Rouge ( r ) ; plage de valeur : 0-255
	 - Vert ( v ) ; plage de valeur : 0-255
	 - Bleu ( b ) ; plage de valeur : 0-255
 - vitesse : défini la vitesse de l'animation
 - mired : Le mired est une unité de mesure de la température de couleur. Il est aussi souvent noté mégakelvin inverse. Sa relation avec le kelvin est la suivante : , avec M en mired ou MK⁻¹, et T en kelvins. [Pour + d'info voir Wikipédia](https://fr.wikipedia.org/wiki/Mired) . Plage de valeur : 0-255
- Pilot : Bascule l'animation activé (ON) / Désactivé (OFF)


Exemple du message JSON adapter au canal d'Etat (Eta)

```json
{
	"etat": "OFF",
	"animation": "Arc-en-ciel"
	"Couleur": {
		"r": 155,
		"v": 20,
		"b": 75
	},
	"vitesse": 200,
	"mired": 22,
}
```

Définition des paramètres du canal d'entrée (Ent)
- Etat : Situation de l'animation (ON/OFF)
 - Brillance : puissance lumineuse ; plage de valeur de 0 à 255
 - Couleur : Définie la couleur de l'animation pour chaque teinte 
	 - Rouge ( r ) ; plage de valeur : 0-255
	 - Vert ( v ) ; plage de valeur : 0-255
	 - Bleu ( b ) ; plage de valeur : 0-255
 - vitesse : défini la vitesse de l'animation
 - mired : Le mired est une unité de mesure de la température de couleur. Il est aussi souvent noté mégakelvin inverse. Sa relation avec le kelvin est la suivante : , avec M en mired ou MK⁻¹, et T en kelvins. [Pour + d'info voir Wikipédia](https://fr.wikipedia.org/wiki/Mired) . Plage de valeur : 0-255
- Animation :  Le nom de l'animation sollicité. La liste des animations est définie dans le fichier Anim.yaml  
- Pilot : Bascule l'animation activé (ON) / Désactivé (OFF)

#### Fichier de configuration des animations `Anim.yaml`


#### Variables de configuration pour l'auto-détection du serveur MQTT 
Le serveur de domotique `Home Assistant` dispose d'un mode de détection automatique sur serveur de messagerie MQTT. Ce mode qui est implémenté dans le code LedPath d'Hector. Cela nécessite la déclaration de plusieurs variables dans le fichier de `config.h` 

 ```cpp
#define MQTT_HOME_ASSISTANT_SUPPORT
#define ENABLE_AMQTT
```

Mais également dans la déclaration sur le poste (Orange-Pi dans notre cas) de déclarer dans le fichier de configuration sur serveur `Home Assistant`
```yaml
mqtt:
  broker: 172.1.24.xxx #a ajuster
  port: 1883
  client_id: home-assistant
  keepalive: 60
  discovery: true
  discovery_prefix: homeassistant
```	 

Après le redémarrage du serveur `Home Assistant` et les segments le Led's `LedPath` d'Hector. Chaque segment `LedPath_Sxx`  apparaîtront automatiquement dans le tableau de bord du serveur et des clients `Home Assistant`.

Voir [Home Assistant - MQTT Discovery](https://www.home-assistant.io/docs/mqtt/discovery/) pour plus de détail.


### Connexion en mode Websocket

**WebSocket** est un [standard du Web](https://fr.wikipedia.org/wiki/Standard_du_Web "Standard du Web") désignant un [protocole réseau](https://fr.wikipedia.org/wiki/Protocole_r%C3%A9seau "Protocole réseau")[1](https://fr.wikipedia.org/wiki/WebSocket#cite_note-1) de la [couche application](https://fr.wikipedia.org/wiki/Couche_application "Couche application") et une [interface de programmation](https://fr.wikipedia.org/wiki/Interface_de_programmation "Interface de programmation") du [World Wide Web](https://fr.wikipedia.org/wiki/World_Wide_Web "World Wide Web") visant à créer des canaux de communication full-duplex par-dessus une connexion TCP pour les navigateurs web. Le protocole a été normalisé par l'[IETF](https://fr.wikipedia.org/wiki/IETF "IETF") dans la RFC 6455[2](https://fr.wikipedia.org/wiki/WebSocket#cite_note-RFC-6455-t-d-2) en 2011 et l'interface de programmation[3](https://fr.wikipedia.org/wiki/WebSocket#cite_note-3) par le [W3C](https://fr.wikipedia.org/wiki/W3C "W3C").
Lire la suite sur [Wikipédia](https://fr.wikipedia.org/wiki/WebSocket)

#### Petit Tutorial WebSocket pour l'ESP8266

a produire

#### Bouton de contrôle manuel
##### Câblage

Connectez votre bouton entre la broche que vous avez configurée dans les `définitions.h`et la masse.

##### Configuration

Décommentez la ligne suivante pour activer le bouton de contrôle:

```C++
//#define ENABLE_BUTTON // Si defini, valide button de control manuel des animations voir la doc projet

```

Pour sélectionner le mode qui doit être lancé sur les différentes touches, définissez les définitions suivantes:

```cpp
#define BTN_MODE_SHORT "STA| 1|  0|245|196|255|255|255"   // Static white
#define BTN_MODE_MEDIUM "STA| 1| 48|245|196|255|102|  0"  // Fire flicker
#define BTN_MODE_LONG "STA| 1| 46|253|196|255|102|  0"    // Fireworks random

```

Le format est (séparé par des barres verticales "tube"):

-   STA: valeur statique
-   Mode: mode LedPath tel que défini 
- Définissez la valeur sur 1 si vous souhaitez définir un mode WS2812FX.
-   Mode WS2812FX: mode de l'animation WS2812FX (voir le numéro dans l'interface LedPath).
-   Vitesse: de 0 à 255
-   Luminosité: de 0 à 255
-   Rouge: valeur de couleur de 0 à 255
-   Vert: valeur de couleur de 0 à 255
-   Bleu: valeur de couleur de 0 à 255



### Spécification pour l'utilisation avec Node-Red

L'application PathLED propose une interface d’interconnexion sur les nœuds Node-Red. 
Un exemple :
en cours....


## Ressources

 - [QUELS PROTOCOLES APPLICATIFS POUR L'INTERNET DES OBJETS ?](https://www.frugalprototype.com/quels-protocoles-applicatifs-pour-linternet-des-objets/)
 - [Youtube - Installer Mosquitto dans Docker !!!](https://www.youtube.com/watch?v=1msiFQT_flo)
 
 <iframe width="1618" height="557" src="https://www.youtube.com/embed/1msiFQT_flo" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
 

### Bibliothèques complémentaires

 - [Bibliothèque Async TCP pour ESP8266 - **ESPAsyncTCP**](https://github.com/me-no-dev/ESPAsyncTCP)
 - [Implémentation d'un client MQTT asynchrone Arduino pour ESP8266 - **async-mqtt-client**](https://github.com/marvinroger/async-mqtt-client)



