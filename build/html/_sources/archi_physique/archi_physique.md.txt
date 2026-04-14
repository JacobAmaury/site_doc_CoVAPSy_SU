%TODO: marquer quels éléments sont dans troubleshooting 
%TODO: angle de braquage max
%TODO: ajouter la doc de l'ultrason 

# Architecture physique de la voiture

L’architecture physique de la voiture peut être divisée en trois étages :

- l’étage supérieur
- l’étage intermédiaire
- l’étage inférieur.

Cette organisation permet de séparer les éléments de supervision, de calcul embarqué, de puissance et de capteurs.

## L’étage supérieur

L’étage supérieur est le plus simple à identifier. Il est composé de 4 éléments principaux, ou 5 lorsque la caméra 3D est installée.

![Vue de l’étage supérieur](images/voiture_top.jpeg)

### 1. Switch de sécurité

Le switch de sécurité permet d’activer ou de couper l’alimentation des moteurs, du STM32 et des capteurs.

Toute cette partie est alimentée par la batterie moteur. En cas de problème, ce switch permet donc d’arrêter rapidement les éléments liés à l’actionnement et aux capteurs.

### 2. Mezzanine et écran de controle

La mezzanine est la carte de la partie supérieure, reliant l'écran, l'IMU, le buzzer et 3 boutons poussoirs au Hat.

Vous pouvez trouver le layout de la Mezzanine [ici](https://github.com/SU-Bolides/Course_2025_ros2/blob/main/ressources/Electronic_ressources/Vehicle/Mezzanine_CoVASPSy_v1re2_Schema.pdf)

L’écran affiche le nom de la voiture, il est commandé par le STM32.

Le bouton jaune situé en haut à droite permet de réinitialiser le STM32. Cela peut être utile lorsque :

- l’IMU renvoie des données incohérentes
- la voiture ne répond plus correctement

Le buzzer est activé lors d'un reset du STM32. Les boutons 2 et 3 sont reliés (par défaut) au STM32, mais ne font actuellement rien.

```{note}
Le STM32 peut reset involontairement si sa tension d'alimentation (batterie moteur) chute, faisant crasher le STM32. (cf STM32)
```

### 3. IMU

L’IMU utilisée est un [Bosch BNO055](https://www.bosch-sensortec.com/products/smart-sensor-systems/bno055/).

Il permet de mesurer :

- l’accélération (acc_x) par l'accéléromètre
- la vitesse angulaire (yaw_rate) par le gyroscope
- l’orientation (yaw) estimée par fusion interne des capteurs

Il est utilisé pour estimer l’orientation de la voiture, notamment le yaw. Les données IMU sont publiées avec le type standard `sensor_msgs/msg/IMU` sur le topic `/raw_imu_data`.

Le topic IMU est utilisé pour le noeud d'odométrie qui publie le topic `/odom`de type standard `nav_msgs/msg/Odometry` (compatible Nav2).

```{note}
L'accélération est publiée mais n'a pas été utilisée, et peut nécessiter une calibration.
```

```{warning}
L’odométrie de la voiture est principalement basée sur une estimation de l’orientation (yaw) issue de l’IMU, puis dérivée pour obtenir la vitesse angulaire.

Cette approche présente une dérive en rotation, principalement due au bruit et à la sensibilité du signal de yaw.

En pratique, cela peut entraîner :
- des erreurs cumulées d’orientation,
- des déformations de la trajectoire lors de fortes rotations,
- des incohérences dans les cartes SLAM (rotation drift),
- des pertes ponctuelles de localisation.

Pour cette raison, la localisation et la cartographie (réalisé par Nav2) reposent principalement sur le LiDAR.

Voir la section <a href="../troubleshooting/troubleshooting.html#odometrie-incoherente">Odométrie incohérente</a> pour les solutions aux problèmes courants.
```

### 4. Batterie de l’ordinateur embarqué

Cette batterie 5V alimente l’ordinateur embarqué (Raspberry Pi) indépendamment de la batterie moteur.

Cette séparation est pratique pour éviter les coupures/chutes de tension qui pourraient éteindre la Raspberry Pi.

La Raspberry Pi 5 consommant en moyenne beaucoup plus que le moteur, cette séparation a aussi permis une nette amélioration de l'autonomie de la batterie moteur qui était très limitée avant (15 min).

## L’étage intermédiaire

L’étage intermédiaire est divisé en deux parties :

- la partie supérieure
- la partie inférieure

## La partie supérieure de l’étage intermédiaire

Cette partie est composée de 4 éléments visibles sur la photo suivante.

![Vue de la partie supérieure de l’étage intermédiaire](images/voiture_milieu_dessus.jpeg)

### 1. LiDAR

Le LiDAR utilisé est un [SLAMTEC RPLIDAR A2M12](https://www.slamtec.com/en/lidar/a2).

Il permet de mesurer la distance entre la voiture et les obstacles autour d’elle. Il est utilisé pour la perception de l’environnement et les algorithmes de navigation réactive. Les données du LiDAR sont publiées sur le topic `/scan`.

```{note}
On utilise ce capteur avec le nœud `sllidar_node` dans le package `sllidar_ros2`. Des paramètres dans le launch file permettent de limiter l'angle de vue publié.

Son baudrate est à 256000.
```

### 2. Adaptateur Slamtec

Cet adaptateur fait l’interface entre le LiDAR et l’ordinateur embarqué.

Il convertit la liaison du LiDAR vers une interface exploitable par la Raspberry Pi. Il est relié :

- en micro-USB à la Raspberry Pi
- aux lignes d’alimentation nécessaires au fonctionnement du LiDAR

```{warning}
L'adaptateur a un défaut de conception : l'alimentation du LiDAR doit être la même que celle de l'USB pour éviter des retours de courant. Il faut donc utiliser l'alimentation 5V utilisée par la Raspberry Pi (montage actuel).
```

### 3. STM32

La [STM32-L432KC](https://www.st.com/en/evaluation-tools/nucleo-l432kc.html) est le microcontrôleur chargé des fonctions bas niveau.

Son rôle principal est de :

- piloter le moteur de propulsion via l'ESC
- lire et relayer les données des capteurs

La communication avec la Raspberry Pi se fait en SPI. Le code de cette carte n’est pas entièrement disponible dans le Git actuel car l'étudiant qui s’en occupait n'a pas mis son code sur github il y a plusieurs années. Si vous avez besoin de reflasher un STM32 allez voir la partie STM32 de troubleshooting.

```{note}
Le STM32 est actuellement alimenté directement (via le fusible) par la batterie moteur.
C'est le fil jaune qui l'alimente (sur la broche Vin du STM32), et c'est aussi sur ce fil que la tension publiée dans /battery_voltage est mesurée (par un pont diviseur de tension branché à la broche A4 du STM32).

La tension d'alimentation Vin du STM32 doit être supérieure à 5.5V (idéalement au-dessus de 6V).
Si le moteur demande trop de couple et fait chuter la tension, le STM32 peut s'éteindre.
```

### 4. HAT / carte d’interface

Le HAT sert d’interface matérielle entre plusieurs sous-systèmes :

- la Raspberry Pi
- le STM32
- les capteurs
- les moteurs

Son rôle est de centraliser les connexions et certaines liaisons de commande. C’est une carte de jonction importante dans l’architecture de la voiture.

Vous pouvez trouver le layout de la carte [ici](https://github.com/SU-Bolides/Course_2025_ros2/blob/main/ressources/Electronic_ressources/Vehicle/Hat_CoVASPSy_v1re2_Schema.pdf). Le hat et la Raspberry Pi étaient initialement prévus pour être les uns sur les autres, mais pour éviter des retours de courant dans la Raspberry Pi, ils sont désormais séparés et reliés par des câbles pour la communication SPI.

Des cavaliers permettent de choisir si le PWM et/ou l'UART sont reliés à la Raspberry Pi ou au STM32. De même on peut choisir la source du 3V3 (STM32 ou Raspberry Pi) pour le réseau 3V3 (Mezzanine et capteurs). La documentation sur ces branchements se trouve [ici](https://github.com/ajuton-ens/CourseVoituresAutonomesSaclay/blob/main/Hardware/Connecteurs_Cavaliers_CarteHat.pdf)

```{note}
Actuellement, seul le SPI est branché à la Raspberry Pi.
De plus, pour éviter les retours de courants, on préfère utiliser le 3V3 du STM32.

Il est peut-être possible d'enlever le TTL branché entre l'AX-12A et l'U2D2, et de brancher l'U2D2 par UART au Hat qui enverrait le Tx/Rx sur le TTL branché à la carte alim (qui alimente l'AX-12A). Il faut peut-être un autre cavalier sur la carte alim pour ça (nommé J-AX-12A). Ça n'a pas été testé, mais ça éviterait le retour de courant (très faible) dans l'U2D2 et surtout le TTL gênant quand on ouvre la voiture.
```

---

## La partie inférieure de l’étage intermédiaire

Cette partie est composée de 2 éléments visibles sur la photo suivante.

![Vue de la partie inférieure de l’étage intermédiaire](images/voiture_milieu_bas.jpeg)

### 1. Raspberry Pi

L’ordinateur embarqué est une **Raspberry Pi 5** avec **8 Go de RAM**.

Elle tourne sous **Ubuntu 24.04** avec **ROS 2 Jazzy**. C’est elle qui exécute les nœuds ROS 2 de la voiture et orchestre la partie haut niveau.


### 2. U2D2

L’U2D2 est un convertisseur **USB vers UART** utilisé pour communiquer avec le moteur de direction.

Il est relié :

- côté moteur de direction via la liaison TTL 3 broches
- côté Raspberry Pi en USB

```{note}
L'U2D2 a un défaut de conception : il y a un faible retour de courant entre le 7V d'alimentation du moteur (TTL) et le 5V USB.
L'U2D2 est conçu pour interfacer le servo avec le PC pour le programmer, pas pour le commander, même si il est souvent utilisé comme ça.
Tant que ce retour de courant reste faible (ce qui est notre cas), ce n'est pas gênant.
Mais il peut-être envisageable d'avoir une autre installation (cf HAT), voire même de ne pas utiliser l'U2D2 (utiliser l'UART du STM32).
```

---

## L’étage inférieur

L’étage inférieur est le plus chargé. Il est séparé en deux sous-parties :

- la partie **puissance**
- la partie **capteurs**

## La partie puissance

Cette partie est composée des éléments liés à l’alimentation et à l’actionnement.

![Vue de la partie puissance](images/voiture_bottom_puissance.jpeg)

### 1. Batterie moteur

La batterie moteur alimente la partie puissance de la voiture, notamment :

- l’ESC
- le moteur de propulsion
- la STM32
- une partie des capteurs et actionneurs associés à la chaîne de puissance

Elle est distincte de la batterie qui alimente l’ordinateur embarqué. Ce sont des batteries NiMH de 7.2-8.4 V, avec une capacité d’environ 5000 mAh.

### 2. Mécanisme de direction

Le mécanisme de direction transforme la commande du moteur de direction en angle de braquage au niveau du train avant.

```{note}
L'angle de braquage est actuellement assez limité (rayon de braquage de 1m). Il peut-être intéressant de remplacer le jeu de direction par un jeu de type drift, en allu, et si possible avec des vis de réglage pour le parallélisme.

Le code de control de la direction ne correspond pas exactement à la géométrie pour les angles plus élevés, il peut-être à revoir si on veut faire de la vitesse.

En pratique la voiture ne roule pas vraiment droit, et c'est encore pire en marche arrière.
```

Les années précédentes avaient écris [ceci](https://github.com/SU-Bolides/Course_2025_ros2/blob/main/docs/Hardware/upgrades.md) au sujet de la direction quand ils ont remplacé le kit de direction du bolide 1.

### 3. Moteur de direction

Le moteur de direction pilote l’orientation des roues avant. C'est un [AX-12A](https://emanual.robotis.com/docs/en/dxl/ax/ax-12a/).

Il reçoit ses commandes via la chaîne Raspberry Pi → U2D2 → liaison série TTL → actionneur de direction (cf U2D2).

Il est aussi relié à la carte ou sont branchés les capteurs de proximité pour être alimenté par la batterie moteur (7V).

```{note}
Son baudrate est à 1000000 sur notre bolide 2. Il est à 115200 sur bolide 1. C'est programmable en utilisant le logiciel [Dynamixel Wizard 2.0](https://emanual.robotis.com/docs/en/software/dynamixel/dynamixel_wizard2/) et en connectant le servo au PC via l'U2D2.
```

### 4. Moteur de propulsion

Le moteur de propulsion est un [Mabuchimotor RS540SH Ref:BD109829](https://github.com/SU-Bolides/Course_2025_ros2/blob/main/ressources/Electronic_ressources/Vehicle/RS-540SH%20datasheet.pdf) sur notre bolide 2.

```{note}
C'est un [Tamiya 540 Torque 25T Motor](https://www.rcteam.com/en/products/tamiya-540-torque-25t-motor-54358) sur bolide 1.
```

Il est commandé par l’ESC, lui-même piloté par le STM32 lui même commandé par la Raspberry Pi.

### 5. ESC

L’ESC (Electronic Speed Controller) pilote le moteur de propulsion c'est un [Tamiya ESC TBLE-04S](https://www.rcteam.com/products/tamiya-variateur-brushless-sensored-tble-04s-45069).

Il reçoit la commande de vitesse en PWM. Ce PWM correspond au standard pour les ESC. Vous pouvez trouver la documentation de ce composant [ici](https://github.com/SU-Bolides/Course_2025_ros2/blob/main/ressources/Electronic_ressources/doc_ESC.pdf).
 
L'ESC a un switch on off pour l'allumer ou non. Quand il passe de off à on il émet un bip court et une led vert s'allume pendant un court instant.

```{note}
L'ESC est conçu pour être commandé par une radio. La documentation sur son comportement exact est inexistante. Il a été décrit expérimentalement dans le nœud de machine d'état de l'ESC cmd_vel_node.
```

Le noeud `cmd_vel_node` gère une machie à états synchronisée avec la machine à états interne de l'ESC. Les états sont publiés sur le topic `/esc_state`. Il prend des valeurs >0 si il est en Forward ou freine en avant, et <0 s'il est en Reverse ou freine en arrière. C'est utile pour connaître le sens de déplacement de la voiture (car `/raw_fork_data` publie une valeur absolue)

```{note}
Le signe de `cmd_speed_target` ne suffit pas pour l'odométrie, celà créé des décalages lors d'un SLAM.
L'état de l'ESC ne suffit pas pour connaître le sens de déplacement si la voiture est bougée par une perturbation extérieure (autre voiture). Il peut-être intéressant d'utiliser l'accéléromètre, ou une relocalisation par LiDAR.
```

### 6. Fourche optique

La fourche optique sert à mesurer la rotation ou la vitesse. C'est une [TT Electronics OPB815L](https://www.ttelectronics.com/TTElectronics/media/ProductFiles/Datasheet/OPB815.pdf). Elle est branchée au STM32 via la carte alim par un connecteur 4 broches.

Les valeurs de ce capteur sont publiées sur le topic `/raw_fork_data`.

```{note}
La voiture peut aller de 0.4 m/s à 3 m/s voire plus.
```

```{warning}
La fourche optique donne une valeur absolue. Elle ne fournit pas le sens de déplacement de la voiture.

À l'arrêt, la fourche optique peut, des fois, donner une vitesse comme si la voiture avançait. Ces vitesses sont entre 0.3 et 0.5 m/s. On pense que c'est parce que la roue peut s'arrêter à cheval sur un trou et donne des fausses valeurs à cause du bruit.
```

---

## La partie capteurs

Cette partie regroupe les capteurs de proximité installés sous la voiture.

![Vue de la partie capteurs](images/voiture_bottom_capteur.jpeg)

### 1. Télémètre ultrason

Le télémètre ultrason mesure la distance derrière la voiture. C'est un [Robot Electronics SRF10](https://www.robot-electronics.co.uk/htm/srf10tech.htm) branché par I2C au STM32.

Il est publié sur le topic `/range/sonar_rear` type standard `sensor_msgs/msg/Range`, compatible avec Nav2.

### 2. Capteurs infrarouges

Les capteurs infrarouges mesurent des distances de proximité latérale. Ils sont placés à l’arrière (gauche et droit). Ce sont des [Sharp GP2Y0A41SK0F](https://github.com/SU-Bolides/Course_2025_ros2/blob/main/ressources/Electronic_ressources/CapteurIR_SHARP0A41SKF07.pdf).
Ils sont également branchés au STM32, via la carte alim par un connecteur analogique 3 broches.

Ils sont publiés sur des topics séparés `/range/ir_left` et `/range/ir_right` de type standard `sensor_msgs/msg/Range` (compatible avec Nav2).

```{note}
Les fils de connexion des capteurs (ultrason et IR) sont très fragiles. Si vos capteurs ne répondent plus c'est très probablement les fils qu'il faut resouder/remplacer.
```

### 3. Convertisseur DC-DC 5 V (buck)

La carte alim comprend un module step-down (buck) [Texas Instrument PTH08T231WAD](https://www.ti.com/lit/ds/symlink/pth08t230w.pdf?ts=1776153202488&ref_url=https%253A%252F%252Fwww.mouser.fr%252F) assez solide qui peut prendre en entrée entre 6V à 12V (5.5V-14V max) et sort du 5V stabilisé (4-5A max).

À l'origine il servait à alimenter tout le réseau 5V (y compris la Raspberry PI) depuis la batterie moteur. Actuellement il alimente le réseau 5V hors Raspberry Pi ni LiDAR. Il prend son entrée sur la batterie moteur après le fusible (comme le fil jaune -alim STM32-)

Il permet de fournir une alimentation stable en 5V aux capteurs.

```{note}
Le DC-DC ne supporte pas le courant en sens inverse (il n'y a pas de diode de protection). Si une tension est présente sur sa sortie, il faut une tension à au moins 5.5V sur l'entrée.
```

### 4. La carte d'alimentation (interface)

Cette carte présente à l'arrière, gérait à l'origine toute l'alimentation, et les capteurs arrières ainsi que le branchement aux moteurs.

Vous pouvez trouver le layout de la carte [ici](https://github.com/SU-Bolides/Course_2025_ros2/blob/main/ressources/Electronic_ressources/Vehicle/Interface_CoVASPSy_v1re2_Schema.pdf).

```{note}
Le fusible ne protège que le STM32 et le convertisseur DC-DC (donc l'électronique). L'alimentation des moteurs (propulsion et direction) est branchée directement après l'interrupteur.
```
