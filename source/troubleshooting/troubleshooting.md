# Troubleshooting

Cette section regroupe les problèmes courants et ceux que nous avons rencontrés et les solutions si elles sont connues.

## Odométrie incohérente

Vous avez deux cas distincts : soit le yaw est incohérent, soit les positions x et y sont incohérentes ou encore les deux en même temps.

### Yaw incohérent

Ce cas est le plus simple à résoudre, cela est du au stm32 il suffit de le redémarrer. Pour cela, il faut appuyer le bouton encoutré sur la photo suivante :

```{image} images/reset_yaw.jpeg
:width: 50%
```

Vous devriez entendre un bip, cela signifie que le stm32 a redémarré et que le yaw est à nouveau cohérent.

### Positions x et y incohérentes

Ce cas est facile à résoudre il faut juste faire avancer la voiture avec les roues au sol. Cela est du au fait que la fourche optique est entre deux troues du disque codeur, il suffit de faire avancer la voiture pour que la fourche optique puisse se positionner correctement et que les positions x et y soient cohérentes.


## Moteur de direction qui ne répond pas

Ici aussi il y a deux causes possibles : le bauderate n'est pas le bon ou le moteur est juste débranché.
### Bauderate incorrect

Il faut changer le paramètre dans le launchfile de sensor. Sur bolide1 il est à 115200 et sur bolide2 il est à 1000000. Si vous avez des doutes vous pouvez utiliser le logiciel : [dynamixel wizard 2](https://emanual.robotis.com/docs/en/software/dynamixel/dynamixel_wizard2/). Voir aussi le <a href="tuto_add/tuto_dynamixel_wyzard2.html">tuto dynamixel wizard 2</a> pour utiliser le logiciel. 

### Moteur de direction débranché

Il faut que le cable du haut soit branché à la carte d'alimentation comme sur la photo suivante :

```{image} images/alim_direction.jpeg
:width: 50%

et le cable du bas soit branché à l'u2d2 comme sur la photo suivante :

```{image} images/u2d2_direction.jpeg
:width: 50%
```