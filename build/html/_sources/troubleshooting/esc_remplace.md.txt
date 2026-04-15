# Si vous avez changé l'ESC

Si vous avez remplacé l'ESC, il est nécessaire de le recalibrer afin qu'il fonctionne correctement avec le STM32.  
Cette calibration permet de définir précisément les positions **neutre**, **pleine accélération** et **frein**.

## Calibration de l'ESC Tamiya TBLE-04S

Suivez les étapes suivantes :

1. **Allumer l'ESC**  
   Vérifiez que :
   - le switch de sécurité est sur **ON** ;
   - l'ESC est correctement alimenté.

2. **Définir le neutre**
   - Mettre l'accélérateur au neutre (`cmd_vel = 0.0`)
   - Appuyer sur le bouton **SET**
   - Relâcher lorsque la LED devient rouge pour la deuxième fois

   Le neutre est alors enregistré (LED rouge clignotante).

3. **Définir la pleine accélération**
   - Mettre l'accélérateur à fond (`cmd_vel = 1.0`)
   - Appuyer sur **SET**

   Le point haut est alors enregistré (double clignotement rouge).

4. **Définir le frein**
   - Mettre l'accélérateur en frein complet (`cmd_vel = -1.0`)
   - Appuyer sur **SET**

   La calibration est terminée lorsque la LED s'éteint.

```{note}
- `cmd_vel` correspond au topic ROS `/cmd_vel`, utilisé pour piloter la vitesse de la voiture.
- Vérifiez que le STM32 envoie correctement le signal PWM à l'ESC après calibration.
- En cas d'échec, redémarrez l'ESC et recommencez la procédure.
```

## Active la marche arrière

Sur certains ESC, la marche arrière peut être désactivée par défaut.

1. Allumer l'ESC en maintenant le bouton SET appuyé
2. Appuyer brièvement sur SET pour entrer en mode programmation (LED rouge clignotante)
3. Maintenir l'accélérateur en marche arrière (`cmd_vel = -1.0`) pendant environ 2 à 3 secondes
4. Attendre un changement de clignotement de la LED ou un bip

```{note}
- Certains ESC désactivent la marche arrière pour des raisons de sécurité.
- Après activation, des valeurs négatives sur `/cmd_vel` permettent de faire reculer la voiture.
- Si la marche arrière ne fonctionne pas, vérifiez la calibration et les connexions.
```

## Plus sur l'ESC Tamiya TBLE-04S

- Tout sur l'initialisation ESC : <https://www.thercracer.com/2021/05/tamiya-tble-04s-esc-speed-controller.html>
- Vidéo : Setting Up Your New TBLE-04S ESC (rechercher sur YouTube)
