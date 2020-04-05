# Compte Rendu - TP 2 - Binôme 22

## Auteurs et date
Clément MORELLI

Thibault GILG

Date : 07/02/2020
## Exercice 1 : Disques et partitions

1. On commence par aller dans la configuration du client. Ensuite, il faut aller dans stockage puis on crée un disque dur que l’on ajoute à la partie contrôleur SATA. Après avoir lancer la VM on tape ll /dev/sd* et on voit bien que l’on a crée un second disque dur sdb.
2.  Après avoir lancer la VM on tape ll /dev/sd* et on voit bien que l’on a crée un second disque dur sdb.
3. Pour le partitionner on utilise fdisk. on commence par crée les deux partition de respectivement 2gb et 3gb. On utilise la commande sudo fdisk /dev/sdb. Puis, on nous ouvre un menu ou l'on tape la commande n pour dire crée une partition. Ensuite, on tape la commande p pour crée une partition primaire. Puis, on donne un numéro 1 pour celle de 2gb et 2 pour celle de 3gb. On laisse les premiers secteur par défaut et on rentre les derniers afin d'avoir des partitions de la taille désiré. Ainsi, nous avons crée les deux partition de type Linux. Il nous faut modifier celle de 3gb pour la passer en NTFS. Pour cela, lorsque que l'on est a nouveau sur le menu de la commande  sudo fdisk /dev/sdb on tape la commande t, on choisit la partition de 3gb et on dois entrer le code hexagonal correspondant à NTFS ( numéro 7). Ainsi, on à réussi 1 à créer une première partition de 2 Go de type Linux (n°83), et une seconde partition de 3 Go en NTFS (n°7).On oublie pas de tapé w pour sauvegarder.
4. Maintenant, il nous faut formater ces deux partition. En choisissant un format de formatage, on choisit le dernier c'est à dire le ext4. Ainsi, on a juste a tapé la commande mkfs -t ext4 /dev/sdb1 et mkfs -t ext4 /dev/sdb2.
5. Avec la commande df -T on remue que les partition n'apparaisse pas. C'est du au faite qu'on les à crées et formatées mais elles ne sont pas montées.
6. La commande Mount seulement ne permet pas de les monté automatiquement. Il faut le modifier dans le ficher de démarrage de la machine. Ainsi, on le modifie en faisant sudo nano /etc/fstab. On écrit dans le fichier les deux ligne suivantes. /dev/sdb1 /data ext4 default 0 2 et /dev/sdb2 /win ext4 default 0 2.
7. On utilise la commande mount, on voit qu'ils sont bien monté. On peut redémarrer le système. On voit qu'ils sont toujours monté car ils sont présent dans le fichier de redémarrage.

## Exercice 2 : Personnalisation de GRUB

1. Dans notre cas, le fichier ```/etc/default/grub.d/50-curtin-settings.cfg``` n'existe pas.

2. Ainsi, pour activer l'affichage du menu GRUB, on assigne la valeur "menu" à la variable ```GRUB_TIMEOUT_STYLE```, car par défaut le menu GRUB est caché ("hidden"). Pour le temps d'affichage maximale du menu GRUB, on modifie la variable ```GRUB_TIMEOUT``` en lui attribuant la valeur 10, au sein du fichier ```/etc/default/grub```. Enfin, pour lancer par défaut le 1er OS, il faut laisser la valeur 0 à la variable ```GRUB_DEFAULT``` qui correspond à la première option du menu.

3. On valide toujours la configuration de GRUB avec la commande ```update-grub```.

4. On redémarre la VM avec la commande ```sudo reboot``` et on tape le mot de passe.

5. Pour altérer la résolution de GRUB, dans le fichier ```/etc/default/grub```, on décommente la variable ```GRUB_GFXMODE``` et on lui assigne une valeur. Dans notre cas, on a choisi : 1280x1024,1024x768x32. Afin de savoir les formats supportés par notre version de GRUB, il faut taper ```c``` lorsqu'on est au menu GRUB pour afficher le terminal de GRUB. Puis, on tape la commande ```vbeinfo``` qui nous renseigne les formats supportés. Ensuite, dans le fichier de configuration,  on écrit la ligne de code suivante : ```GRUB_GFXPAYLOAD_LINUX=keep``` afin de conserver le mode graphique imposé par la variable ```GRUB_GFXMODE```.

6. On télécharge ainsi le paquet comportant des fonds d'écran de GRUB. Toujours dans le même fichier, on rajoute le code suivant : ```GRUB_BACKGROUND="/usr/share/images/grub/Hortensia-1.tga"```, qui correspond au chemin du fichier image souhaité. Après validation et redémarrage de la VM, l'image d'Hortensia est bien en fond de notre menu GRUB. 

7. On ajoute ces lignes au fichier ```/etc/grub.d/40_custom``` :
```
menuentry 'Arrêt du système' { 
	halt 
} 
menuentry 'Redémarrage du système' { 
	reboot 
}
```

8.
 ```
sudo mkdir /boot/grub/layouts
sudo grub-kbdcomp -o /boot/grub/layouts/fr.gkb fr
```

Ajouter  `GRUB_TERMINAL_INPUT=at_keyboard`  au fichier /etc/default/grub Ajouter à /etc/grub.d/40_custom :

```
# Clavier fr
insmod keylayouts
keymap fr
```

## Exercice 3 : Noyau

1. On installe le paquet avec la commande ```sudo apt install build-essential```.

2. Pour gagner du temps et copier-coller le code de l'énoncé, on crée un fichier hello.c avec le code de l'énoncé sous notre machine physique (ici Windows) et avec un terminal, on exporte le fichier vers la VM avec la commande ```scp -P 2222 hello.c serveur@localhost:```. 2222 correspond au port sur lequel est connectée la VM. Le fichier se trouve désormais dans le répertoire de travail de la VM.

3. On fait de même pour le fichier ```Makefile```.

4. On compile le module à l’aide de la commande ```make```, puis on l'installe à l’aide de la commande ```make install```.

5. Le fichier se trouve au format ```.ko``` à l'emplacement ```/lib/modules/5.3.0-42-generic/kernel/drivers/misc```.

## Exercice 4 : Exécution de commandes en différé : at et cron
1. Pour programmer une tâche affichant un rappel que la réunion va démarrer dans 3 minutes, il suffit d'utiliser la commande ```at```. Ainsi, dans le cas présent, on écrit ```echo 'echo "Rappel : la réunion va commencer !"' | at now +3 minutes```. La console renvoie un numéro de tâche et l'heure à laquelle la tâche sera exécutée. Lorsqu'on liste les tâches en attente d'exécution avec la commande ```atq```, la tâche est bien présente pendant 3 minutes avant jusqu'à l'heure d'exécution, cependant, aucun message n'est affiché par la console à l'heure précisée précédemment. 

2. En affichant les logs avec la commande ```journalctl -r```, on voit l'erreur suivante : ```atd[xxxx]: Exec failed for mail command: No such file or directory```. En effet, le daemon ne trouve pas la commande ```mail``` qui n'est pas installé sur cette machine. On installe donc le paquet ```mailutils```. Puis, il faut modifier la commande en précisant le télétype sur lequel on se trouve : ```echo 'echo "Rappel : la réunion va commencer !" > /dev/tty1 ' | at now +3 minutes```, car on se trouve sur tty1.

3. Pour afficher ce message toutes les 3 minutes, on modifie la *crontab*, à l'aide de la commande ```crontab -e```, et on écrit la ligne suivante :
```
*/3 * * * * echo "Il faut réviser l'examen!" > /dev/tty1
```

4. Pour effectuer une commande tous les 15 minutes, tous les jours de l'année, on écrit :
```
*/15 * * * * commande > /dev/tty1
```

5. Une commande toutes les cinq minutes à partir de 2 (2, 7, 12, etc.) à 18 heures les 1er et 15 du mois est programmée de la façon suivante :
```
2/5 18 1,15 * * commande > /dev/tty1
```

6. L’exécution d’une commande du lundi au vendredi à 17 heures :
```
0 17 * * mon-fri commande > /dev/tty1
```

7. Pour rediriger les mails vers un fichier log de notre dossier personnel, on crée ce fichier et puis on modifie la *crontab* de la façon suivante  : 
```
*/3 * * * * echo "Il faut réviser l'examen!" >> /fichier_log
```

8. On vide la *crontab* à l'aide de la commande ```crontab -r```.

## Exercice 5 : Surveillance de l'activité du système
* La commande ```htop``` permet d'afficher tous les processus en cours d'exécution dans la console virtuelle. La commande ```w``` affiche les informations sur les autres utilisateurs (consoles) qui sont connectés et leur processus en cours d'exécution. Dans notre cas, cette dernière renvoie ```tty1``` et ```tty2```.

* L'historique des dernières connexions à la machine s'affiche par la commande ```last```. Cette dernière renvoie le nom d'utilisateur, la console utilisée et les horaires de connexion et déconnexion.

* On affiche la version du noyau avec la commande ```uname```, qui renvoie dans notre cas ```Linux```.

* Pour avoir les infos sur le processeur au format JSON, il faut exécuter la commande ```lshw -json CPU```. ```lshw``` liste le matériel de la machine, ```CPU``` permet de ne garder que les infos du processeur, et ```-json``` affiche en format JSON.

* On affiche la liste des derniers démarrages de la machine avec la commande ```journalctl --list-boots```, qui nous renvoie la date, et l'heure précise.

* On affiche tout ce qu'il s'est passé sur la machine avant-hier avec la commande `journalctl -S -2d -U -1d`  
Le -S signifie "ince" et le U "until". On remonte dans le temps avec -2d : il y a deux jours. On sélectionne les log entre il y a deux jours et il y a un jour, c'est à dire avant-hier.

* 

## Exercice 7 : Sauvegardes, tar

1. La création d'une archive en lui donnant "*" comme argument  rassemble tous les fichiers non-cachés contenu dans le répertoire courant, alors que l'argument "." permet d'archiver tous les fichiers cachés.

2. La commande ```tar cvf archive.tar * .*``` fonctionne mais archive tous les fichiers (cachés ou non) y compris des liens vers les autres archives présente dans le répertoire. 

3. Sans les droits d'exécution, il est impossible de créer une archive du fichier. La permission est refusée.
