# Odométrie incohérente

Vous avez deux cas distincts : soit le yaw est incohérent, soit les positions x et y sont incohérentes ou encore les deux en même temps.

## Yaw incohérent

Ce cas est le plus simple à résoudre, cela est du au stm32 il suffit de le redémarrer. Pour cela, il faut appuyer le bouton encoutré sur la photo suivante :

```{image} images/reset_yaw.jpeg
:width: 50%
```

Vous devriez entendre un bip, cela signifie que le stm32 a redémarré et que le yaw est à nouveau cohérent.

## Positions x et y incohérentes

Ce cas est facile à résoudre il faut juste faire avancer la voiture avec les roues au sol. Cela est du au fait que la fourche optique est entre deux troues du disque codeur, il suffit de faire avancer la voiture pour que la fourche optique puisse se positionner correctement et que les positions x et y soient cohérentes.
