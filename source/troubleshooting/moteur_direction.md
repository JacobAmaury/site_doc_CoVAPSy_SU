# Moteur de direction qui ne répond pas

Ici aussi il y a deux causes possibles : le bauderate n'est pas le bon ou le moteur est juste débranché.

## Bauderate incorrect

Il faut changer le paramètre dans le launchfile de sensor. Sur bolide1 il est à 115200 et sur bolide2 il est à 1000000. Si vous avez des doutes vous pouvez utiliser le logiciel : [dynamixel wizard 2](https://emanual.robotis.com/docs/en/software/dynamixel/dynamixel_wizard2/). Voir aussi le <a href="tuto_add/tuto_dynamixel_wyzard2.html" style="color:red;">tuto dynamixel wizard 2</a> pour utiliser le logiciel. 

## Moteur de direction débranché

Il faut que le cable du haut soit branché à la carte d'alimentation comme sur la photo suivante :

```{image} images/alim_direction.jpeg
:width: 50%
```

et le cable du bas soit branché à l'u2d2 comme sur la photo suivante :

```{image} images/u2d2_direction.jpeg
:width: 50%
```
