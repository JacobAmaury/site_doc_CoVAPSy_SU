# Voiture qui avance en saccadé

Il arrive que la voiture avance et s'arrête très vite. Cela est très probablement du à un noeud qui publie du neutre dans le topic `/cmd_vel`. Par exemple vous avez peut-être lancé 2 noeud téléop un va réster au neutre et l'autre va lui dire d'avancer ce qui produit ce mouvement. Vous pouvez l'observer sur le gif suivant : 

```{image} images/sacade.gif
:width: 50%
```
