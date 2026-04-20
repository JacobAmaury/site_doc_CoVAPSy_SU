# Moteurs qui ne répondent pas

Voici la marche à suivre pour résoudre ce problème :

## 1/ Vérifier l'ESC

Dans un premier temps, vérifier que le switch de sécurité est bien en position ON et que le bouton de l'esc est en off.
Allumer le switch de l'ESC et vérifier que la led verte s'allume et que vous entendez un bip court. Si ce n'est pas le cas, il y a un problème soit un problème d'alimentation soit un problème sur l'ESC.

```{warning}
Attention : L'ESC est le composant qui brule en premier généralement, il est donc important de vérifier son bon fonctionnement avant de chercher une autre cause.
```

## 2/ ESC qui bip bip

Cela veut dire que l'ESC ne ressoit pas de signal de neutre de la part du stm32. Il y a plusieurs causes à se problème : soit le stm32 ne fonctionne pas, soit il est mal branché, soit il est mal configuré.

### STM32 qui ne fonctionne pas

Il faut regarder les led du stm32, une doit s'allumer en rouge et l'autre doit clignoter en rouge comme sur la photo suivante :

```{image} images/stm32_led.jpeg
:width: 50%
```

Si elle ne s'allume pas prenez un autre stm32 si sur la boite il y a un scotch c'est quelles sont déjà flashées, sinon il faut le flasher. Suivez suivant pour flasher le stm32 : <a href="tuto_add/tuto_flash_stm32.html">tuto flashing stm32</a>.

### STM32 mal branché

Il faut vérifier que le cable sur la photo suivante est bien branché entre le hat et la carte d'alimentation :

```{image} images/alim_stm32.jpeg
:width: 10% 
```
