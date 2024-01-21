# spark_demonstration
Spark demonstration on facebook dataset

# Adding requirements installing java
!apt-get install openjdk-8-jdk-headless -qq > /dev/null
# installing spark and hadoop combined
!wget -q https://archive.apache.org/dist/spark/spark-3.0.1/spark-3.0.1-bin-hadoop2.7.tgz
!tar xf spark-3.0.1-bin-hadoop2.7.tgz
# Installed findspark to integrate python with spark
!pip install -q findspark

# Environment Setup for java and spark
import os
os.environ["JAVA_HOME"] = "/usr/lib/jvm/java-8-openjdk-amd64"
os.environ["SPARK_HOME"] = "/content/spark-3.0.1-bin-hadoop2.7"

import findspark
# Initializing the module to connect python with spark
findspark.init()
# Needed module
from pyspark.sql import SparkSession
# Initializing spark session
spark = SparkSession.builder.master("local[*]").getOrCreate()

# Uploading dataset with the help of google colab implementation
from google.colab import files
files.upload()

# Creating Dataframe out of the dataset
df = spark.read.format("csv").option("header","true").option("inferSchema","true").option("mode","failfast").load("pseudo_facebook.csv")

# Creating temporary view out of data
# df will be considered as a virtual SQL table and called as fb
df.createOrReplaceTempView("fb")

# Using spark to write sql query to show dataframe
spark.sql("select * from fb").show(10)

# Evaluating average age of a person on facebook
spark.sql("select count(*) from fb").show()

# Average age of a person on facebook
spark.sql("select avg(age) from fb").show()

# Average age by gender
spark.sql("select avg(age), gender from fb group by gender").show()

# Storing in python variable
a = spark.sql("select avg(age) from fb").collect()[0][0]
print(a) 

# Checking which gender recieved most likes 
spark.sql("select avg(likes_received) as average_like, gender from fb group by gender order by average_like").show()

# average friend count of peoples in facebook with age between 20 to 30
spark.sql("select avg(friend_count) from fb where age>=20 AND age<=30").show()

# Mobile and Website users
spark.sql("select avg(mobile_likes), avg(www_likes) from fb where age>=20 AND age<=25").show()

# Calculating average friend count by age
df.groupBy("age").avg("friend_count").orderBy("age", ascending = True).show(10)


