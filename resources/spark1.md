# SparkGreaterThan

# Création
Ce fichier jar a été compilé et packagé par Maven

	mvn compile
	mvn package

# Exécution
<div class="command-line"><span class="command">./bin/spark-submit \
	--class sparkjava.SparkGreaterThan \
	--master local spark2jobsOutput.jar \
	hdfs://clusterspark-masterspark-0:8020/user/ubuntu/src/obj.csv \
	/user/ubuntu/test.txt</span></div>

# Principe
Ce programme récupère obj.csv dans HDFS (argument en entrée)

	map1job1 : récupération du premier champ (ID) → JavaRDD<Long>
	map2job1 : filtre des champ dont la valeur est supérieure 
	           à 433327840000000 → JavaRDD<Long>
	map1job2 : récupération du champ 55 →  JavaRDD<Long>
	map1job2 : mise en cache
	map2job2 : filtre des valeurs nulles → JavaRDD<Long>
	map3job2 : filtre des valeurs >0,17 et < 0,18 → JavaRDD<Long>

Le programme écrit dans un fichier texte les différents résultats (argument en sortie)

# Code

```java
package sparkjava;

import java.io.FileWriter;
import java.io.IOException;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;


import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import org.apache.spark.api.java.function.FilterFunction;
import org.apache.spark.api.java.function.FlatMapFunction;
import org.apache.spark.api.java.function.Function;
import org.apache.spark.api.java.function.MapFunction;
import org.apache.spark.api.java.function.PairFunction;
import org.slf4j.Logger;

import scala.Tuple2;


public class SparkGreaterThan {

	private static JavaSparkContext context;

	@SuppressWarnings("serial")
	public static void main(String[] args) {
		
	    SparkConf conf = new SparkConf().setAppName("sparkgreaterthan").setMaster("local");
	    context = new JavaSparkContext(conf);

	    String inputFile = args[0];
	    String outputDirectory = args[1];
	    
	    //Long startTime = context.startTime();
	    long start = System.currentTimeMillis();
	    
	    //JavaRDD<String> file = context.textFile("hdfs://clusterspark-masterspark-001:8020/user/ubuntu/src/obj.csv");
	    JavaRDD<String> file = context.textFile(inputFile);

		String user = context.sparkUser();
		String log="Log :";
	   
		boolean trace=conf.isTraceEnabled();
		
		JavaRDD<Long> map1job2=file.map(new Function<String, Long>() {
			@Override
			public Long call(String s) throws Exception {
				int nb_column=55;
				
				int index=0;//=s.indexOf(",");
				
				for(int i=0; i< nb_column; i++)
				{
					index=s.indexOf(",");
					s=s.substring(index, s.length());
				}
				
				int index2=s.indexOf(",");
				s=s.substring(0,index2);
				
				if (s.equals("")) {s="0";}
				
				return Long.parseLong(s);
			}		
		});
		
		map1job2.cache();
		
		JavaRDD<Long> map1job1=file.map(new Function<String, Long>(){
			@Override
			public Long call(String s) throws Exception {
				s=s.substring(0,s.indexOf(","));
				return Long.parseLong(s);
			}			
		});

		JavaRDD<Long> map2job1=map1job1.filter(new Function<Long,Boolean>(){
			@Override
			public Boolean call (Long i){
				if (i>433327840000000L) return true;
				else return false;
			}
		});
				
		JavaRDD<Long> map2job2=map1job2.filter(new Function<Long,Boolean>(){
			@Override
			public Boolean call (Long i){
				if (i==0) return true;
				else return false;
			}
		});
		
		JavaRDD<Long> map3job2=map1job2.filter(new Function<Long, Boolean>() {
			@Override
			public Boolean call(Long i) throws Exception {
				if (i>0.17 && i<0.18) return true;
				else return false;
			}
		});
		
		long endmaps=System.currentTimeMillis();
		
		long timeEndMaps= endmaps-start;
		
		long nb=map2job1.count();	
				
		long endcount1=System.currentTimeMillis();
			
		long timeCount1=endcount1-endmaps;
		//JavaRDD<String> res = String.valueOf(map2.count());
		
		long nb2=map2job2.count();
		//Long endTime = context.startTime();
		long endcount2=System.currentTimeMillis();
		long timeCount2=endcount2-endcount1;
		
		long nb3=map3job2.count();
		
		long endcount3=System.currentTimeMillis();
		long timeCount3=endcount3-endcount2;
		
		long timeTotal = System.currentTimeMillis() - start;
		
		System.out.println("Nb lines field 1 :" +nb);
		System.out.println("Nb lines field 55 =0 :" +nb2);
		System.out.println("Nb lines field 55 >0.17 :" +nb3);

		System.out.println("Trace ? :"+trace);
		System.out.println("Spark user :" +user); 
		System.out.println("\n"); 

		System.out.println("timeTotal " +timeTotal/1000+" sec"); 
		System.out.println("timeMaps "+timeEndMaps+" millisec");
		System.out.println("timeCount1 "+timeCount1/1000+" sec");
		System.out.println("timeCount2 "+timeCount2/1000+" sec");
		System.out.println("timeCount3 "+timeCount3/1000+" sec"); 
	    
		FileWriter fileResult;
		try {
		//	fileResult = new FileWriter("/user/ubuntu/results/resultSparkGreaterThan.txt");
			fileResult = new FileWriter(outputDirectory);
			
			fileResult.write(" Nb de tuples dont l'id est > 433327840000000 : "+nb);
			fileResult.write("context.sparkUser() :"+user);
			fileResult.write("context.version() :");
			fileResult.write("trace.isEnabled() :"+trace);

			fileResult.flush();
			fileResult.close();
			System.out.println("Write file !");
		} catch (IOException e) {
			e.printStackTrace();
		}
		context.stop();
	}
}
```
