# TP 02 UNIX Milton FIGUEIREDO

## 1.1 Connexion SSH root

Pour permettre les connexion root distante il a fallu modifier le contenu du fichier sshd_config avec la commande `cat /etc/ssh/sshd_config` et y rajouter la ligne `PermitRootLogin yes`

## 1.2 Authentification par clef / Génération de clefs

Depuis la machine hôte utiliser la commande `ssh-keygen` qui va créer un couple de clef publique et privée, dans notre cas nous n'avons pas utilisé de passphrase pour simplifier les choses mais il faudrait dans un cas réel en utilisé un car les passphrase constituent un moyen de créer des mots de passe forts grace a leur longueur et identité unique.

## 1.3 Authentification par clef / Connexion serveur

Depuis le dossier /root j'ai vérifié si le dossier `.ssh` existait, dans mon cas c'était le cas donc je n'ai pas du crée le dossier avec la commande `mkdir`.

Il a ensuite fallu que je créer le fichier `authorized_keys` avec la commande `touch authorized_keys`.

Ensuite pour mettre notre clé publique dans ce fichier j'ai fais ctrl + shift+ c  de ma clé publique depuis le terminal de la machine hôte et un ctrl+ shift + v dans le fichier `authorized_keys` du serveur.

## 1.4 Authentification par clef depuis la machine hôte 

`ssh -i id_ed25519 root@10.20.0.185`

1013769@ppti-24-308-01:/users/nfs/Etu9/1013769/.ssh$ ssh -i id_ed25519 root@10.20.0.185

## 1.5 Sécurisez 

Il faut changer la config du fichier `sshd_config` une nouvelle fois en y ajoutant le code suivant : 
```
PasswordAuthentication yes
PermitRootLogin prohibit-password
```

Les attaques de type brute-force ssh consistent tester des combinaisons de nom d'utilisateur et mot de passe jusqu'a accéder a un serveur.

Une technique afin de contrer ce type d'attaque serait par exemple de limiter les ip depuis un parefeu

## 2.1 Etude des processus UNIX

1- afficher la liste de tous les processus tournant sur ma machine : `ps aux`

Time correspond au temps CPU cumulé du processus. 

Le processus ayant le plus utilisé le processeur est systemd j'ai pu le voir grace a la commande `top`

Le 1er processus est systemd j'ai pu le voir car son pid est égale 1 avec la commande `ps aux` 

Ma machine a démarré à 14h16 j'ai pu le voir avec la commande `ps aux` mais aussi avec `who -b`

```
root@serveur-correction:~# who -b
       démarrage système 2024-10-09 14:16
```

2- trouver le ppid d'un processus : `ps auxf`

3- pstree : 

```
root@serveur-correction:~# pstree
systemd─┬─cron
        ├─dbus-daemon
        ├─dhclient
        ├─login───bash───su───bash
        ├─sshd─┬─sshd───sshd───bash───su───bash
        │      └─sshd───bash───pstree
```

4- top : 

```
    PID UTIL.     PR  NI    VIRT    RES    SHR S  %CPU  %MEM    TEMPS+ COM.
      1 root      20   0   80547  11144   9086 S   0,0   0,6   0:00.57 systemd
    228 root      20   0   17820  11096   9420 S   0,0   0,6   0:00.05 sshd
    259 root      20   0   18004  11072   9232 S   0,0   0,5   0:00.20 sshd
    204 root      20   0   49356  11068   6984 S   0,0   0,5   0:00.15 systemd-journal
```

le processus le plus gourmand est systemd

`z` pour changer la couleur


## 3 Arrêt d'un processus
 
Avec la commande `ps` et `kill` je peux identifier les processus avec leur pid et les terminer.

## 4 Les tubes

`Cat` est utilisé pour afficher le contenu d'un fichier.

`Tee` est utilisé pour écrire dans la console et dans un fichier indiqué.

`ls | cat`
ls envoie son résultat habituelle a cat et cat affiche ce qu'il recoit 
	
`ls -l | cat > liste`
met le résultat de la commande ls dans le fichier liste
	
`ls -l | tee liste`
affiche le résultat dans la console et l'écrit également dans le fichier liste
	
`ls -l | tee liste | wc -l`
reprend la commande précèdente en y ajoutant le résultat de wc
