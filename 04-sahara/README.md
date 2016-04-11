# Introduction

L'Elastic Data Processing (EDP) permet de lancer des jobs sur des clusters de traitement de données. Les clusters sont créés grâce à la définition de templates. Le plugin EDP permet ensuite de lancer les jobs sur l'infrastructure qui aura été construite. 

Les types de jobs pris en charge sont les suivants :

* Hive, Pig, MapReduce, MapReduce.Streaming, Java
* Shell job sur les clusters Hadoop

Les clusters pris en charge sont les suivants :

* Hadoop Map Reduce
* Hortonworks Data Platform
* Cloudera
* Spark

Les interfaces de stockages disponibles sont :

* HDFS
* Swift (sauf pour les jobs Hive)
