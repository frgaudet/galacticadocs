# SparkUseRDD

Auteur : [Bastien Doreau](mailto:bdoreau@isima.fr)

# Création
Ce fichier jar a été compilé et packagé par Maven

	mvn compile
	mvn package

# Exécution
Lancé grâce au plugin EDP ou bien manuellement, avec la commande :

<div class="command-line"><span class="command">./bin/spark-submit 
	--class sparkjava.SparkUseRDD --master local 
	sparkUseRDD.jar 
	hdfs://clusterspark-masterspark-0:8020/user/ubuntu/src/obj.csv 
	swift://output.sahara/</span></div>

# Principe

Ce programme récupère obj.csv dans HDFS (paramètre en entrée), puis :

	map1 : réalisation d'un flatMap sur obj.csv  → JavaRDD<String>
	jpr : Attribution d'une valeur de 1 à chaque mot →  JavaPairRDD<String, Integer>
	jpr2 : Transformation des variables String et int en Text et IntWritable 
		   en utilisant la fonction ConvertToWritableTypes   → JavaPairRDD<Text, IntWritable>
	saveAsHadoopFile en prenant l'argument de sortie pour écrire dans swift (ou HDFS)

# Code

```java
import java.io.FileWriter;
import java.io.IOException;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapred.SequenceFileOutputFormat;
import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import org.apache.spark.api.java.function.FilterFunction;
import org.apache.spark.api.java.function.FlatMapFunction;
import org.apache.spark.api.java.function.Function;
import org.apache.spark.api.java.function.MapFunction;
import org.apache.spark.api.java.function.PairFunction;

import scala.Tuple2;

public class SparkUseRDD {
	  public static class ConvertToWritableTypes implements PairFunction<Tuple2<String, Integer>, Text, IntWritable> {
		private static final long serialVersionUID = 1L;

		public Tuple2<Text, IntWritable> call(Tuple2<String, Integer> record) {
	      return new Tuple2(new Text(record._1), new IntWritable(record._2));
	    }
	  }
	
	
	public static void main(String[] args) {
		
	   SparkConf conf = new SparkConf().setAppName("sparkuserdd").setMaster("local");
	   JavaSparkContext context = new JavaSparkContext(conf);

	   String inputFile = args[0];
	   String outputDirectory = args[1];
	        
	    //JavaRDD<String> file = context.textFile("hdfs://clusterspark-masterspark-001:8020/user/ubuntu/src/obj.csv");
	    JavaRDD<String> file = context.textFile(inputFile);
	    
	    JavaRDD<String> map1=file.flatMap(new FlatMapFunction<String, String>() {
			private static final long serialVersionUID = 1L;

			public Iterable<String> call(String s) { return Arrays.asList(s.split(",")); }
	    });
		 
	     JavaPairRDD<String, Integer> jpr=map1.mapToPair(new PairFunction<String, String, Integer>() {
			private static final long serialVersionUID = 1L;

			public Tuple2<String, Integer> call(String s) {
                 return new Tuple2<String, Integer>(s, 1);
             }
         });
	  
	     JavaPairRDD<Text, IntWritable> jpr2=jpr.mapToPair(new ConvertToWritableTypes());
	    		 
	     jpr2.saveAsHadoopFile(outputDirectory, Text.class, IntWritable.class, SequenceFileOutputFormat.class);

	   System.out.println("\n"); 
	}
}
```

