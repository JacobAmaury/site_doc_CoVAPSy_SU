# Quick Start
## I/ Allumer la voiture

### 1/ Vérifier que tout est éteint

Dans un premier temps, mettre la voiture sur les supports.
  

Photo_1
  

Vérifier que le gros bouton sur le haut de la voiture est en position OFF (0) et que le bouton du contrôle des moteurs en bas de la voiture est également sur OFF.

Vérifier que les deux batteries sont bien présentes et chargées. Pour vérifier que la batterie des moteur est chargée il faut la mesurer avec un voltmètre, elle doit être à 7.4V ou plus.

Photo_2

  

---

  

### 2/ Allumer la voiture

  

Dans un premier temps, allumer l’alimentation de laboratoire, régler la tension à 8V et 2A.

Brancher les câbles rouges avec les câbles rouges et les noirs avec les noirs, puis les relier au bas de la voiture.

Photo_3

  
Brancher la Raspberry Pi avec une batterie en USB. Normalement, la Raspberry Pi s'allume en vert.
  

Photo_4


Vous pouvez maintenant allumer le gros bouton sur le haut de la voiture (vous devriez entendre un long bip), puis le bouton du contrôle des moteurs (vous devriez entendre un autre bip plus court et plus aigu).


Photo_5

  

---

  

## II/ Connexion à la voiture
### 1/ Connexion au Wi-Fi de la voiture

  

Au bout de 2 à 3 minutes, sur votre ordinateur, vous devriez voir un réseau Wi-Fi nommé : bolide1 ou 2.

Connectez-vous au Wi-Fi de la voiture. le mot de passe est : setup1234.
  

> Attention : ne pas le faire dans l’image Docker fournie pour l’UE de ROS.

  

---

  

### 2/ Connexion en SSH

  

Ouvrir un terminal puis taper la commande suivante :

  

```bash

ssh  voiture@10.42.0.1

```


votre permission sera demandé pour la première connexion, tapez yes et appuyez sur Entrée. Ensuite, tapez le mot de passe : ros (il est normale qu'aucun caractère ne s'affiche à l'écran, votre mot de passe est écrit même si il est invisible). Appuyez à nouveau sur Entrée.

  

Normalement, vous devriez être connecté à la voiture.

  


---

  

### 3/ Téléopérer la voiture

  

Taper la commande :

  

```bash

ros2  launch  bolide_teleop  teleop_keyboard.launch.py

```

Vous pouvez la teleopérer avec les touches indiquées sur le terminal.

  

Photo_8

  

Bravo, normalement la voiture roule !