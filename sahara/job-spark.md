# Introduction

Maintenant que notre cluster est opérationnel, nous allons y lancer un traitement. Le processus de création des jobs est le suivant :

{% mermaid %}
graph LR;
  A(Stockage du binaire)-->B;
  B(Définition du modèle de job)-->C;
  C(Lancement du job);
{% endmermaid %}

# Stockage des binaires

Ouvrez le menu EDP, puis 'Job binaries'. Cliquez sur 'Create Job binary'

![Local Image](./images/jobs-12.jpg)

Choisissez soit un binaire à uploader, soit une URL swift. Cliquez sur 'Create'.

![Local Image](./images/jobs-03.jpg)

Notre binaire est maintenant enregistré :

![Local Image](./images/jobs-10.jpg)

# Définition du modèle de job

Le modèle de job spécifie les binaires à utiliser. Cliquez sur 'Create Job Template'

![Local Image](./images/jobs-11.jpg)

 Le binaire principal est à renseigner dans l'onglet 'Create job template', et éventuellement des librairies supplémentaires à définir dans l'onglet 'Libs'.

Par exemple pour un job Spark :

![Local Image](./images/jobs-04.jpg)

Cliquez sur 'Create'.

# Lancement du job

A partir du job template, cliquez sur 'Launch on existing cluster'. 

![Local Image](./images/jobs-08.jpg)

![Local Image](./images/jobs-09.jpg)

Les champs à renseigner ensuite dépendent du type d'exécutable que vous souhaitez utiliser. Dans l'exemple donné ci-dessous, j'utilise un jar Spark qui nécessite 2 arguments en entrée : un chemin vers les données et un chemin vers lequel le résultat sera écrit. Les données sont stockées sur l'Object Store.

![Local Image](./images/jobs-06.jpg)

 Quelques explications :

 * Main class : classe principale qui sera appelée,

**Section configuration :**

 * Username Swift : notez que le nom de la variable 'fs.swift.service.sahara.username' est immuable
 * Password Swift : le nom de la variable doit également être respecté.

<div class="alert alert-warning">Le format d'accès aux containers swift est de la forme suivante swift://container.sahara/<br>N'oubliez pas le trailing slash.</div>

<div class="alert alert-notice">Pour information, tous les couples name/value de la section configuration vont être placés dans le fichier core-site.xml du master.</div>

**Section Arguments :**

* Un champs pour le premier argument à fournir au jar,
* Un champs pour le second argument à fournir au jar.

<div class="alert alert-warning">Attention, le container de sortie ne doit pas exister avant de lancer le job.</div>

Si les données se trouvent sur HDFS, il suffit de changer l'URI correspondante :

![Local Image](./images/jobs-05.jpg)

Cliquez sur 'Launch'. Le job va s'exécuter sur le cluster :

![Local Image](./images/jobs-07.jpg)
