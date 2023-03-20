- Mastering JSON Handling in Apache Spark: A Guide to MapType, ArrayType, and Custom Schemas
    - https://siraj-deen.medium.com/mastering-json-handling-in-apache-spark-a-guide-to-maptype-arraytype-and-custom-schemas-7e679f65de05

- get_json_object
- The get_json_object function is used to extract a specific field from a JSON string. The function takes two arguments: the first argument is the JSON string and the second argument is the field name to extract.
- Ex. code -> 
from pyspark.sql.functions import get_json_object

data = [("{\"name\":\"John\",\"age\":30}",),
        ("{\"name\":\"Jane\",\"age\":35}",)]
df = spark.createDataFrame(data, ["json_data"])

df = df.select("json_data", 
               get_json_object(df.json_data, "$.name").alias("name"),
               get_json_object(df.json_data, "$.age").alias("age"))

df.show()

- from_json
- The from_json function is used to parse a JSON string into a Spark DataFrame. The function takes two arguments: the first argument is the JSON string and the second argument is the schema that defines the structure of the JSON data. The schema is defined using the StructType class
Ex.
from pyspark.sql.functions import from_json
from pyspark.sql.types import StructType, StructField, StringType, IntegerType

data = [("{\"name\":\"John\",\"age\":30}",),
        ("{\"name\":\"Jane\",\"age\":35}",)]
df = spark.createDataFrame(data, ["json_data"])

schema = StructType([
  StructField("name", StringType(), True),
  StructField("age", IntegerType(), True)
])

df = df.select("json_data", from_json(df.json_data, schema).alias("data"))

df = df.select("data.*")

df.show()

- Custom schema
- we can create objects usingStructType, MapType and ArrayType that define the structure of our data

- MapType: It is a type of column that represents a map of key-value pairs. The MapType takes two arguments: the data type of the keys and the data type of the values.
from pyspark.sql.types import StructType, StructField, IntegerType, StringType, MapType

# Define the schema for the dataframe
schema = StructType([
  StructField("id", IntegerType(), True),
  StructField("name", StringType(), True),
  StructField("age", IntegerType(), True),
  StructField("address", MapType(StringType(), StringType()), True)
])

# Create a dataframe with the specified schema
data = [  (1, "John Doe", 25, {"city": "New York", "country": "USA"}),  (2, "Jane Doe", 30, {"city": "London", "country": "UK"})]

df = spark.createDataFrame(data, schema=schema)

df.show()

- ArrayType: It is a type of column that represents an array of values. The ArrayType takes one argument: the data type of the values.
from pyspark.sql.types import StructType, StructField, IntegerType, StringType, ArrayType

# Define the schema for the dataframe
schema = StructType([
  StructField("id", IntegerType(), True),
  StructField("name", StringType(), True),
  StructField("age", IntegerType(), True),
  StructField("hobbies", ArrayType(StringType()), True)
])

# Create a dataframe with the specified schema
data = [
  (1, "John Doe", 25, ["Reading", "Travelling"]),
  (2, "Jane Doe", 30, ["Music", "Dancing"])
]

df = spark.createDataFrame(data, schema=schema)

df.show()

- 