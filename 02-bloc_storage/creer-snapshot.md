# Avertissement
Pensez avant toute opération sur les volumes à démonter le disque depuis votre VM.

Exemple : `sudo umount /dev/vdb`

{% em type="red" %}Cette commande ne peut fonctionner que si aucun processus n'utilise cette ressource.{% endem %}

# Détacher le volume

Après avoir démonté votre volume depuis votre VM, procédez au détachement via le menu contextuel.

![Local Image](./images/volume-20.jpg)
![Local Image](./images/volume-16.jpg)

# Créer le snapshot

Puis, cliquez sur 'Create snapshot' toujours depuis le menu contextuel.

![Local Image](./images/volume-17.jpg)

Indiquez le nom du snapshot, et cliquez sur 'Create Volume Snapshot'

![Local Image](./images/volume-21.jpg)

Le temps de traitement peut être important selon la taille du volume. Soyez patient :) A l'issue du transfert, le volume apparait dans l'onglet 'Snapshot'.

![Local Image](./images/volume-22.jpg)

Vous pouvez maintenant attacher le volume, le monter et l'utiliser de nouveau.