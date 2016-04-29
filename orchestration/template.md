# Création du template

Le Heat Orchestration Template (HOT) est un fichier descriptif en yaml structuré comme ceci :

	heat_template_version: 2015-04-30

	description:
	  # a description of the template

	parameter_groups:
	  # a declaration of input parameter groups and order

	parameters:
	  # declaration of input parameters

	resources:
	  # declaration of template resources

	outputs:
	  # declaration of output parameters

* heat_template_version:

	Indique que le présent fichier obéit à cette version particulière. En effet, HOT a fait l'objet de plusieurs versions, s'enrichissant au fur et à mesure de nouveaux paramètres ou de nouvelles fonctions. Toutes les versions sont backward compatible.

* description:

	Section optionnelle, dont le nom suffit à en expliquer l'usage :)

* parameter_groups:

	Cette section permet de grouper des paramètres. Optionnelle.

* parameters:

	Cette section permet de paramétrer le template. Optionnelle également.

* resources:

	Cette section contient la définition des resources à créer. Il faut à minima une ressource, sans quoi votre template de fera pas grand chose. 

* outputs:

	Cette section permet d'afficher un feedback à l'utilisateur à l'issue du processus d'exécution du template. Optionnelle.

# Exemple

Voici un template très simple qui créé une instance :

	heat_template_version: 2014-10-16
	description: A first exemple.

	resources:
	  server:
	    type: OS::Nova::Server
	    properties:
	      image: cirros-0.3.4-x86_64
	      flavor: m1.tiny
	      networks:
	      - network: dev-net

# Liens

* [Heat Orchestration Template (HOT) Guide](http://docs.openstack.org/developer/heat/template_guide/hot_guide.html "HOT Guide")
* [Heat Orchestration Template (HOT) specification](http://docs.openstack.org/developer/heat/template_guide/hot_spec.html "HOT Specification")