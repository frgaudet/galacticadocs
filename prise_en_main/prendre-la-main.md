# Introduction

Vous pouvez prendre la main sur une instance de deux façons différentes.

* Par SSH
* Par la console

Si vous n'avez rien spécifié de particulier lors de l'instantiation, alors la seule manière dans un premier temps de prendre la main sur la VM est la méthode SSH. Pour prendre la main à partir de la console, il faut au préalable avoir créé un user local à cet effet.

Le username utilisé depend de la distribution. Sur ubuntu, le user est 'ubuntu'. Pour les distributions centos il s'agit de 'cloud-user' ou bien de 'centos' selon les versions. Si vous utilisez d'autres types d'images, il faudra alors rechercher quel est le user par défaut.

# Accès SSH

Pour se connecter en SSH, il faut tout d'abord associer une floating IP à la VM.

##Mise en oeuvre côté OpenStack

A partir du menu déroulant choisir l'option 'Associate Floating IP'.

![Local Image](./images/ajouter-une-floating-ip-01.jpg)

Choisir l'IP à associer, cliquez sur 'Associate'.

![Local Image](./images/ajouter-une-floating-ip-02.jpg)

La VM est désormais accessible via la floating IP depuis l'extérieur.

![Local Image](./images/ajouter-une-floating-ip-03.jpg)

Vous pouvez dès lors créer un user local qui soit autorisé à se connecter à cette VM. Petit rappel : pour des raisons de sécurité il est absolument interdit d'utiliser le compte 'root' pour se connecter à distance. Vous pouvez alors libérer la floating IP.

##Client Linux

Il suffit d'indiquer à SSH la localisation de la clé privée grâce à l'option `-i`.

<div class="command-line"><span class="command">ssh -i fgaudet-id_rsa ubuntu@193.55.95.226</span></div>

##Client Windows

Un bon client SSH sous windows est le programme [putty](http://www.putty.org). Vous aurez besoin de 2 exécutables : `putty` et `puttygen`. En effet, putty utilise son propre format de clé privé imposant de ce fait la conversion de la clé SSH téléchargée depuis la plateforme grâce à `puttygen`.

###Conversion de la clé

Ouvrez l'utilitaire `puttygen.exe` et cliquez sur le bouton 'Load',

![small](./images/putty-01.jpg)

et sélectionnez votre clé privée SSH. Vous recevez lors la confirmation que votre clé a été chargée :

![tiny](./images/putty-02.jpg)

Ensuite, sauvegardez la clé en cliquant sur le bouton 'Save private key', 

![small](./images/putty-03.jpg)

répondez 'oui' à la question.

![tiny](./images/putty-04.jpg)

Choississez un nom approprié, et cliquez sur 'Enregistrer'.

![small](./images/putty-05.jpg)

###Configuration de putty

Ouvrez à présent putty, cliquez sur le menu 'Connection/data' et placez le username désiré dans le champ 'Auto-login username' :

![small](./images/putty-07.jpg)

Ensuite, ouvrez à partir du menu la section 'Auth', et remplissez le champ 'Private key file for authentication' :

![small](./images/putty-08.jpg)

Maintenant que les options sont définies, vous pouvez placer l'adresse IP de connection dans la section 'Session' et cliquez sur 'Open'

![small](./images/putty-06.jpg)

La première fois, il vous est proposé d'accepter la clé publique :

![tiny](./images/putty-09.jpg)

Cliquez sur 'Oui', vous avez maintenant un accès SSH fonctionnel :

![small](./images/putty-10.jpg)

# Accès par la console

**Après avoir créé un user local**, à partir du navigateur web, cliquez sur l'instance afin d'ouvrir l'écran qui détaille les caractéristiques de votre VM. Cliquez ensuite sur l'onglet 'Console'.

![Local Image](./images/console-01.jpg)