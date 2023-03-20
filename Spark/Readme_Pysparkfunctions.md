- Spark Functions 
    - https://medium.com/@djpy5898/most-useful-pyspark-functions-7028139e9d74
- Read csv data
df_patient = spark.read.format("csv").option("header", "true").load("PatientInfo.csv")
- printSchema
df_patient.printSchema()
- filter
df_patient.filter("province='Seoul'")       (SQL Syntax)
df_patient.filter(col("province")=="Seoul")     (Using col function)
- GroupBy
dataframe.groupBY(['column1','column2']).agg(agg_function('column_to_aggregate'))
Ex. 
df_patient.groupBy(["province","city"]).agg(countDistinct("infection_case").alias("infection_count"))
- Join 
Join function has three parameters:1. dataframe to which your joining the original dataframe 2. ‘on’ — this parameter takes a list of column to be joined on 3. ‘how’ — this parameter takes joining type (inner, right , left etc). 
Ex.
df_seoul_inf=df_patient.groupBy(["province","city"]).agg(countDistinct("infection_case").alias("infection_count"))
df_seoul_inf=df_seoul_inf.join(df_region, on=['province','city'],how='inner' )
df_seoul_inf=df_seoul_inf.filter("province='Seoul'").select("province","city","infection_count","university_count").distinct()
- Window functions
Window functions in PySpark are useful to partition data by a particular column to rank, aggregate, or analyze them. 
There are three kinds of window functions in PySpark : 
1. Ranking functions, 
2. Analytic functions and 
3. Aggregate functions. 
Ex. 
windowPart= Window.partitionBy("province").orderBy("infection_count")
df_seoul_inf=df_seoul_inf.withColumn("Infection_Rank",dense_rank().over(windowPart))
- User Defined Functions
UDF’s are reusable functions that can help in doing some manipulations to data using primitive functions of Python.
Let's create a UDF to generate a location column which would be a combination of latitude and longitude.
    def generate_locate(lat,lon):
        return lat +","+lon
    genLocUdf=udf(lambda x,y: generate_locate(x,y))
    df_case=df_case.withColumn("Location", genLocUdf(col('latitude'),col('longitude')))