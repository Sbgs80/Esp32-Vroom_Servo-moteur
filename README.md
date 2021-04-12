# Projet Esp32 Vroom - EspHome - HomeAssistant - Servo

<em>Voici un petit tutoriel concernant la configuration d'un 'Esp32 Vroom dev kit' avec EspHome et intégration dans HomeAssistant.
Nous verrons également comment brancher un servo-moteur et l'intégrer sous HomeAssistant.
Enfin, nous procèderons à la modification du servo-moteur pour obtenir une rotation continue.</em>



<ins>Matériel requis :</ins>
><em>Je tiens à préciser que je ne gagne rien via les liens affichés, je me suis concentré principalement sur amazon par soucis de praticité mais vous pouvez sans doute trouver mois cher ailleurs ;)</em>
1. Raspberry (pour ma part un pi3b+ acheté d'occasion sur ebay)
2. Carte mémoire [SanDisk HIGH ENDURANCE 32Go](https://www.amazon.fr/dp/B07P14QHB7/?coliid=IRLOSYWRA0KGU&colid=7P6Z05FCENOM&psc=1&ref_=lv_ov_lig_dp_it) (11€) pour l'installation de HomeAssistant
3. Une carte [ESP32 Vroom DevKit](https://www.amazon.fr/gp/product/B08H2F3N9B/ref=ppx_yo_dt_b_asin_title_o03_s00?ie=UTF8&psc=1) (9€99)
4. Câbles [Dupont Breadboard](https://www.amazon.fr/gp/product/B01JD5WCG2/ref=ppx_yo_dt_b_asin_title_o02_s00?ie=UTF8&psc=1) (7€99) pour branchement du servo
5. Un lot de 2 servo modèle [MG996R](https://www.amazon.fr/gp/product/B07XC6VBSZ/ref=ppx_yo_dt_b_asin_title_o04_s00?ie=UTF8&psc=1) couple 12Kg (12€)

<ins>EspHome & ~~HomeAssistant~~ Ubuntu :</ins>
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
Une fois l'installation terminée depuis la VM Ubuntu, rendez-vous sur votre navigateur et tapez l'adresse locale ***127.0.0.1:6052*** pour accéder à l'interface ESPHome:

![esph-1](https://user-images.githubusercontent.com/64536764/114371132-7bcbb900-9b80-11eb-9a3c-3082c76a56a3.PNG)


![Capture_vroom](https://user-images.githubusercontent.com/64536764/114370114-65712d80-9b7f-11eb-9621-c058902d5e31.PNG)
