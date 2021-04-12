# Projet Esp32 Vroom - ESPHome - HomeAssistant - Servo

<em>Voici un petit tutoriel concernant la configuration d'un 'Esp32 Vroom dev kit' avec EspHome et intégration dans HomeAssistant.
Nous verrons également comment brancher un servo-moteur et l'intégrer sous HomeAssistant.
Enfin, nous procèderons à la modification du servo-moteur pour obtenir une rotation continue. L'objetcif étant de pouvoir commander le servo depuis HomeAssistant, de le faire tourner dans un sens puis dans l'autre et de pouvoir bien évidemment l'arrêter.</em>



<ins>Matériel requis :</ins>
><em>Je tiens à préciser que je ne gagne rien via les liens affichés, je me suis concentré principalement sur amazon par soucis de praticité mais vous pouvez sans doute trouver mois cher ailleurs ;)</em>
1. Raspberry (pour ma part un pi3b+ acheté d'occasion sur ebay)
2. Carte mémoire [SanDisk HIGH ENDURANCE 32Go](https://www.amazon.fr/dp/B07P14QHB7/?coliid=IRLOSYWRA0KGU&colid=7P6Z05FCENOM&psc=1&ref_=lv_ov_lig_dp_it) (11€) pour l'installation de HomeAssistant
3. Une carte [ESP32 Vroom DevKit](https://www.amazon.fr/gp/product/B08H2F3N9B/ref=ppx_yo_dt_b_asin_title_o03_s00?ie=UTF8&psc=1) (9€99)
4. Câbles [Dupont Breadboard](https://www.amazon.fr/gp/product/B01JD5WCG2/ref=ppx_yo_dt_b_asin_title_o02_s00?ie=UTF8&psc=1) (7€99) pour branchement du servo
5. Un lot de 2 servo modèle [MG996R](https://www.amazon.fr/gp/product/B07XC6VBSZ/ref=ppx_yo_dt_b_asin_title_o04_s00?ie=UTF8&psc=1) couple 12Kg (12€)

<ins>ESPHome & ~~HomeAssistant~~ Ubuntu :</ins>
><em>Je n'ai jamais réussi à faire fonctionner EspHome correctement sur HomeAssistant, plantage systèmatique au niveau de la compilation du programme à injecter dans l'ESP32 malgré une tentative d'utilisation de versions stable, bêta... Du coup je me suis tourné vers une version utilisable depuis un pc Linux (ubuntu) dans une VM sous virtualBox. Un excellent tutoriel est accessible ici : [Tutoriel EspHome Ubuntu](https://www.programmersought.com/article/46824697468/).</em>
>> Commandes installation EspHome sous terminal Ubuntu :
```
1.sudo apt install python3.8
2.sudo apt install python3-pip
3.sudo pip3 install esphome -i https://pypi.tuna.tsinghua.edu.cn/simple
4.sudo esphome config/ dashboard
5. Visit http://ip:6052
Note that esp8266 should be plugged into the Linux running system, not on the host that opens the webpage.
```
<ins>Configuration ESP32 Vroom dans ESPHome :</ins>

Une fois l'installation terminée, rendez-vous sur votre navigateur et tapez l'adresse locale ***127.0.0.1:6052*** pour accéder à l'interface ESPHome:

<p align="center">
  <img width="727" height="572" src="https://user-images.githubusercontent.com/64536764/114371132-7bcbb900-9b80-11eb-9a3c-3082c76a56a3.PNG">
</p>

Cliquer ensuite sur l'icone **+** en bas à droite, puis **BEGIN**. Dans Node Name mettez le nom que vous souhaitez donner (pour mon exemple *vroom*). Dans **Device Type** sélectionner **ESP32** **NodeMCU-32S**. Dans les paramètres WIFI entrez le **SSID** et le **mot de passe** de votre réseau WIFI. Vous pouvez définir un mot de passe OTA popur pemettre les mises à jour du programme via WIFI histoire déviter de repasser en mode filaire après... Pour terminer cliquer sur **SUBMIT**. Votre premier noeud apparaît sur la page !

<p align="center">
  <img width="1816" height="325" src="https://user-images.githubusercontent.com/64536764/114374241-b97e1100-9b83-11eb-8c7c-678f01d47fc0.PNG">
</p>

Cliquer ensuite sur **EDIT** pour afficher le code généré suite à la création de votre premier noeud. Pour l'instant l'ESP32 ne pourra que se connecter à votre réseau WIFI, seuls ces paramètres sont configurés...  vous obtenez un code qui doit ressembler à cela :

```YAML
esphome:
  name: wroom
  platform: ESP32
  board: nodemcu-32s

wifi:
  ssid: "Freebox-XXX" # Nom de votre SSID
  password: "VotreMotdeP@sse" # Mot de passe WIFI

  # Enable fallback hotspot (captive portal) in case wifi connection fails
  ap:
    ssid: "Wroom Fallback Hotspot"
    password: "wxJckgcAxh8j"

captive_portal:

# Enable logging
logger:

# Enable Home Assistant API
api:

ota:
```
<ins>Modification du code de l'ESP32 pour intéragir avec un servomoteur :</ins>

Nous allons maintenant pouvoir modifier le code afin que l'ESP32 puisse intéragir avec le servomoteur. J'ai arpenter pas mal de tutoriel et malheureusement tous étaient axés sur l'utilisation d'un *Wemos Mini* et non utilisable avec un **ESP32 vroom**. Les modifications sont minimalistes certes, mais pas évidentes à trouver lorsque l'on débute. Voici le code à ajouter/modifier avec quelques explications :

```YAML
esphome:
  name: vroom
  platform: ESP32
  board: nodemcu-32s

wifi:
  ssid: "Freebox-XXX"
  password: "MonMotdeP@sse"

  # Enable fallback hotspot (captive portal) in case wifi connection fails
  ap:
    ssid: "Vroom Fallback Hotspot"
    password: "wxJckgcAxh8j"

captive_portal:

# Enable logging
logger:

api:
  services:
    - service: control_servo
      variables:
        level: float
      then:
        - servo.write:
            id: my_servo
            level: !lambda 'return level / 100.0;'

output:
  - platform: ledc # Ici, s'agissant d'un esp32 Vroom il faut utiliser la platform *ledc* 
    id: gpio_18 # GPIO utilisé pour envoyer la commande vers le servo (ici GPIO18)
    pin: GPIO18 # GPIO utilisé pour envoyé la commande vers le servo (ici GPIO18)
    frequency: 50 Hz

servo:
  - id: my_servo
    output: gpio_18 # GPIO utilisé (ici le 18)
ota:
  password: "XXXXXXXX"
 ```
Une fois le code sauvegardé, cliquer que le bouton **VALIDATE**. Si tout est correct vous avez une petite notification en haut à droite indiquant **wroom.yaml is valid**.
Avant de fermer cette page vous pouvez l'uploader vers votre ESP32 en cliquant sur le bouton **UPLOAD**. Avant de lancer **L'UPLOAD** laisser votre doigt appuyé sur le boutton **BOOT** (en bas à droite) et <ins>garder le appuyé jusqu'a la fin</ins> sinon ca ne fonctionnera pas 🤦.

![Capture_vroom](https://user-images.githubusercontent.com/64536764/114370114-65712d80-9b7f-11eb-9621-c058902d5e31.PNG)

Vous pouvez maintenant connecter votre servomoteur à la carte ESP. 
1. Le fil rouge sur le PIN 5V car l'ESP est alimenté chez moi par un cable mirco-usb2
2. Le fil marron (GND) à brancher sur le PIN GND à côté du PIN 5V
3. Le fil orange (signal) à brancher sur le PIN GPIO18.

![telecommande_servomoteur](https://user-images.githubusercontent.com/64536764/114406466-daf0f400-9ba7-11eb-8e68-24247a0eaff6.jpg)
