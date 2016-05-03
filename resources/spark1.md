# SparkGreaterThan

Auteur : [Bastien Doreau](mailto:bdoreau@isima.fr)

# Création
Ce fichier jar a été compilé et packagé par Maven

	mvn compile
	mvn package

# Exécution
Lancé grâce au plugin EDP ou bien manuellement, avec la commande :

<div class="command-line"><span class="command">./bin/spark-submit \
	--class sparkjava.SparkGreaterThan2 \
	--master local sparkGT2.jar \
	swift://datastore.sahara/obj.csv \
	swift://outputObj.sahara/ </span></div>

# Principe
Ce JAR récupère un fichier csv et opére plusieurs traitements : Récupération de certaines valeurs sur chaque ligne (tuple), filtrage de ces valeurs sur des critères précis, décompte des résultats obtenus, enregistrement d'un JavaRDD en base.

Ce JAR est appliqué sur obj.csv (voir conteneur Swift sur Openstack), une base de type 'lsst' de 4 millions de tuples. 
Des marqueurs de temps permettent de quantifier le temps pris pour ces opérations. Comme attendu, les opérations de mapping sont négligeables (qq millisecondes). Les traitements sont effectués lors des count().

Opérations :

	map1job1 : récupération du champ 1 (ID) → JavaRDD<Long>
	map1job2 : récupération du champ 55 →  JavaRDD<Long>
	map1job3.: récupération du champ 3 → JavaRDD<Long> 
	map2job1 : filtre champ 1 - valeur > 433327840000000 → JavaRDD<Long>
	map2job2 : filtre champ 3 - valeurs > 90 → JavaRDD<Long>
	map3job2 : filtre champ 55 - valeurs >0,17 et < 0,18 → JavaRDD<Long>
	nbVal1.: compte le nb de lignes de map2job1 → Long
	nbVal3.: compte le nb de lignes de map2job2 → Long
	nbVal55.: compte le nb de lignes de map3job2 → Long
	+ saveAsTextFile.: enregistre le JavaRDD<Long> map2job1 dans l'argument donné en sortie.

Résultats attendus : 

	Valeur 1 Objectid  > 433327840000000 : 801171
	Valeur 3 ra_ps > 90° : 752629
	Valeur 55 ue1_sg > 0.17 & < 0.18 : 17360
	
# Code

```java
package sparkjava;

import java.util.Arrays;

import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import org.apache.spark.api.java.function.FlatMapFunction;
import org.apache.spark.api.java.function.Function;

import scala.Tuple2;


public class SparkGreaterThan2 {
	private static JavaSparkContext context;

	@SuppressWarnings("serial")
	public static void main(String[] args) {
		
		
	    SparkConf conf = new SparkConf().setAppName("sparkgreaterthan"); //.setMaster("local");

	    context = new JavaSparkContext(conf);
	    
	    //////////////////////////////////////////////////////////////////////////////////////
	    // Recuperation des arguments  arg 1 : fichier à ouvrir   ,  arg2 : fichier à sauver
	    String inputFile = args[0];
	    String outputDirectory = args[1];
	    
	    // Récupération de l'utilisateur
	    String user = context.sparkUser();

	    ///////////////////////////////////////////////////////////////////////////////////
	    // Récupération du fichier et enregistrement dans un JavaRDD
	    // Chaque ligne du fichier correspond à un String
	    //JavaRDD<String> file = context.textFile("hdfs://clusterspark-masterspark-001:8020/user/ubuntu/src/obj.csv");
	    JavaRDD<String> file = context.textFile(inputFile);
	    
	    long start = System.currentTimeMillis();

////////////// MAPPING //////////////

	    //////////////////////////////////////////////////////////////
	    // Récupération de le 55e valeur de chaque ligne (Long) et 
	    // enregistrement dans un JavaRDD<Long>
		JavaRDD<Double> map1job2 = file.map(new Function<String, Double>() {
			  public Double call(String s) { 
				  String[] fields=s.split(",");
				  if (fields[54].equals(""))
				  {fields[54]="-1";}
				  Double goodVal=Double.parseDouble(fields[54]);
				  
				  return goodVal; 
				  }
			});
		map1job2.cache();
		// Le résultat JavaRDD<Long> map1job2 est mis en cache

	    //////////////////////////////////////////////////////////////
	    // Récupération de le 1e valeur de chaque ligne (Long) et 
	    // enregistrement dans un JavaRDD<Long>
		JavaRDD<Long> map1job1=file.map(new Function<String, Long>(){
			@Override
			public Long call(String s) throws Exception {
				s=s.substring(0,s.indexOf(","));
				return Long.parseLong(s);
			}			
		});
		
	    //////////////////////////////////////////////////////////////
	    // Récupération de le 3e valeur de chaque ligne (Long) et 
	    // enregistrement dans un JavaRDD<Long>
		JavaRDD<Double> map1job3 = file.map(new Function<String, Double>() {
			  public Double call(String s) { 
				  String[] fields=s.split(",");
				  if (fields[2].equals(""))
				  {fields[2]="-1";}
				  Double goodVal=Double.parseDouble(fields[2]);
				  
				  return goodVal; 
				  }
			});

		long endmaps=System.currentTimeMillis();
		long timeEndMaps= endmaps-start;
		
////////////// FILTRAGE //////////////
		
		///////////////////////////////////////////////////
		// Filtrage de la 1e valeur -> toutes les valeurs supérieures
		// à une valeur précise sont récupérées
		JavaRDD<Long> map2job1=map1job1.filter(new Function<Long,Boolean>(){
			@Override
			public Boolean call (Long i){
				if (i>433327840000000L) return true;
				else return false;
			}
		});
		
		///////////////////////////////////////////////////
		// Filtrage de la 3e valeur -> toutes les valeurs > à 90
		// sont récupérées 
		JavaRDD<Double> map2job2=map1job3.filter(new Function<Double,Boolean>(){
			@Override
			public Boolean call (Double i){
				if (i>90) return true;
				else return false;
			}
		});
		
		///////////////////////////////////////////////////
		// Filtrage de la 55e valeur -> toutes les valeurs entre une 
		// valeur min et une valeur max sont récupérées
		JavaRDD<Double> map3job2=map1job2.filter(new Function<Double, Boolean>() {
			@Override
			public Boolean call(Double i) throws Exception {
				if (i>0.17 && i<0.18) return true;
				else return false;
			}
		});
		
		long endfilter=System.currentTimeMillis();
		long timeFilter= endfilter-endmaps;
		
		
////////////// COUNT //////////////		
		
		// Compte le resultat du filtre de la 1e valeur
		long nbVal1=map2job1.count();	
		
		long endcount1=System.currentTimeMillis();
		long timeCount1=endcount1-endfilter;
		
		// Compte le resultat du filtre de la 4e valeur
		long nbVal3=map2job2.count();
		
		long endcount3=System.currentTimeMillis();
		long timeCount3=endcount3-endcount1;
		
		// Compte le resultat du filtre de la 55e valeur
		long nbVal55=map3job2.count();
		
		long endcount55=System.currentTimeMillis();
		long timeCount55=endcount55-endcount3;
		
		///////////////////////////////////////////
		// Enregistrement du resultat de filtrage
		// de la 1e valeur dans le fichier donné en argument	
		map2job1.saveAsTextFile(outputDirectory);

		long saveJavaRdd=System.currentTimeMillis();
		long timeSaveJavaRDD=saveJavaRdd-endcount55;
		
		// Calcule le temps total
		long timeTotal = System.currentTimeMillis() - start;

	   // Affichage
	   System.out.println("Nb lines Objectid > 433327840000000 :" +nbVal1);
	   System.out.println("Nb lines ra_ps > 90° :" +nbVal3);
	   System.out.println("Nb lines ue1_sg >0.17 & <0.18:" +nbVal55);
	   System.out.println("Spark user :" +user); 
	   System.out.println("\n"); 
	   System.out.println("timeTotal " +timeTotal/1000+" sec"); 
	   System.out.println("time Maps "+timeEndMaps+" millisec");
	   System.out.println("time Filters "+timeFilter+" millisec");
	   System.out.println("timeCount1 "+timeCount1/1000+" sec");
	   System.out.println("timeCount4 "+timeCount3/1000+" sec");
	   System.out.println("timeCount55 "+timeCount55/1000+" sec");
	   System.out.println("timeSaveFile "+timeSaveJavaRDD/1000+" sec");

	   context.stop();
	}
}
```
