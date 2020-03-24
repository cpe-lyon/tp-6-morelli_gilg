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
