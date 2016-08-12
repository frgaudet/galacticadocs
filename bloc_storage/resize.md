# Avertissement
Pensez avant toute opération sur les volumes à démonter le disque depuis votre VM.

Exemple : `sudo umount /dev/vdb`

<div class="alert alert-warning">Cette commande ne peut fonctionner que si aucun processus n'utilise cette ressource.</div>

# Détacher le volume

Après avoir démonté votre volume depuis votre VM, procédez au détachement via le menu contextuel.

![Local Image](./images/volume-04.jpg)
![Local Image](./images/volume-16.jpg)

# Augmenter la taille

Puis, cliquez sur 'Extend Volume' toujours depuis le menu contextuel.

![Local Image](./images/volume-17.jpg)

Indiquez la nouvelle taille du disque, et cliquez sur 'Extend Volume'.

![Local Image](./images/volume-18.jpg)

Après un léger temps de traitement, le volume est disponible avec la nouvelle taille spécifiée.

![Local Image](./images/volume-19.jpg)

Vous pouvez maintenant attacher le volume, le monter et l'utiliser.