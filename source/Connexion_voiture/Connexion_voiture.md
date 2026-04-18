# Connexion avancée à la voiture 

## Accès bureau à distance au Raspberry Pi

1. Téléchargez et installez **TigerVNC Viewer** sur votre PC.
2. Connectez votre PC au réseau Wi-Fi du Raspberry Pi (mot de passe : `setup1234`).
3. Lancez une connexion SSH depuis votre PC (mot de passe : `ros`) :

```bash
ssh -L 5901:localhost:5901 voiture@10.42.0.1
```

4. Démarrez le serveur VNC sur le Raspberry Pi (dans le terminal SSH) :

```bash
vncserver
```

5. Lancez TigerVNC Viewer sur votre PC :
   Connectez-vous à `localhost:5901` avec le mot de passe `setup1234`.

6. Si nécessaire, le mot de passe du Raspberry Pi est : `ros`.

---

## Accès terminal à distance via SSH

Pour se connecter directement au Raspberry Pi en SSH :

1. Vous devez être sur le même réseau (Wi-Fi) que le Raspberry Pi.

2. Dans un terminal :

```bash
ssh voiture@10.42.0.1
```

Mot de passe : **ros**

Lors de la première connexion ou en cas de problème avec la clé SSH :

```bash
ssh-keygen -R 10.42.0.1
```

Puis réessayez de vous connecter.

---

## Accès aux fichiers à distance via SFTP

Vous pouvez explorer les fichiers du Raspberry Pi depuis votre explorateur de fichiers (et utiliser VSCode sur votre PC) en ouvrant une connexion SFTP.

1. SFTP utilise SSH : vérifiez que la connexion SSH fonctionne.

2. Cherchez comment vous connecter à un serveur SFTP (SSH-FTP) selon votre explorateur de fichiers.

3. Configurez le client avec :

```text
Protocole : SFTP
Serveur : 10.42.0.1
Utilisateur : voiture
Port : 22 (par défaut)
Mot de passe : ros
```

Si plusieurs personnes modifient les fichiers en même temps, pensez à **rafraîchir manuellement** l’explorateur de fichiers.

Le Raspberry Pi apparaîtra comme un disque monté (comme une clé USB).
Si vous souhaitez un vrai montage de ce type, utilisez **SSHFS** (voir documentation en ligne).
