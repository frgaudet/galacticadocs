# Introduction

Le service d'orchestration - nom de code : Heat - permet aux utilisateurs de gérer le cycle de vie d'une infrastructure complète.

# Principe

L'orchestration se base sur un fichier descriptif - un template - qui décrit l'ensemble des ressources que vous souhaitez exploiter : le nombre d'instances, leur taille, les disques virtuels, les réseaux, mais également les relations entre chacune de ses ressources : par exemple un disque virtuel est attaché à un serveur.

Si vous souhaitez changer une élément de votre infrastructure, il suffit de modifier le fichier descriptif, puis d'appliquer les changements. Heat se chargera de détruire les ressources obsolètes et ajoutera les nouvelles le cas échéant.

Heat utilise un format de fichier de tempalte (en constante évolution...) appelé HOT, mais il est également compatible avec le format CloudFormation d'AWS. 

# Intégration

 L'orchestration gère principalement l'infrastructure, cependant il est possible de l'intégrer avec des outils comme puppet ou chef.

# Lien

 [Documentation officielle](https://wiki.openstack.org/wiki/Heat "Heat Wiki")