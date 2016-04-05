# Créer un clone

Le clonage d'une VM est assez simple. 2 étapes sont nécessaires :

* création d'un snapshot,
* puis, lancement d'une instance.

Voyons maintenant un cas pratique. Sélectionnons une VM, et créons un snapshot.

![Local Image](./images/clone-01.jpg)

![Local Image](./images/clone-02.jpg)

Le processus peut prendre un peu de temps selon la taille de la VM. Le snapshot résultant de cette opération est désormais accessible dans l'onglet 'Image'. Notez que l'image se trouve en cliquant sur le bouton 'Project', c'est à dire crée et accessible seulement par les membres du projet, et que son type est 'Snapshot'. Néanmoins, techniquement, il s'agit d'une image. Ni plus ni moins.

![Local Image](./images/clone-03.jpg)

# Lancer une instance

Enfin, lancez une instance à partir du snapshot.

![Local Image](./images/clone-04.jpg)