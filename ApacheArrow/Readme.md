- A gentle introduction to Apache Arrow with Apache Spark and Pandas
    - https://towardsdatascience.com/a-gentle-introduction-to-apache-arrow-with-apache-spark-and-pandas-bb19ffe0ddae
- Apache Arrow is a cross-language development platform for in-memory data. It specifies a standardized language-independent columnar memory format for flat and hierarchical data, organized for efficient analytic operations on modern hardware.
- It facilitates communication between many components, for example, reading a parquet file with Python (pandas) and transforming to a Spark dataframe, Falcon Data Visualization or Cassandra without worrying about conversion.
- Apache Arrow takes advantage of a columnar buffer to reduce IO and accelerate analytical processing performance.
- pyarrow
conda install -c conda-forge pyarrow
pip install pyarrow
- pache Arrow with Pandas (Local File System)
- convert pandas dataframe to apache arrow table 
            import numpy as np
            import pandas as pd
            import pyarrow as pa
            df = pd.DataFrame({'one': [20, np.nan, 2.5],'two': ['january', 'february', 'march'],'three': [True, False, True]},index=list('abc'))
            table = pa.Table.from_pandas(df)
- Pyarrow Table to Pandas Data Frame
            df_new = table.to_pandas()
- Read CSV
            from pyarrow import csv
            fn = ‘data/demo.csv’
            table = csv.read_csv(fn)
            df = table.to_pandas()
- Writing a parquet file from Apache Arrow
            import pyarrow.parquet as pq
            pq.write_table(table, 'example.parquet')
- Reading a parquet file
            table2 = pq.read_table(‘example.parquet’)
            table2
- Reading some columns from a parquet file
            table2 = pq.read_table('example.parquet', columns=['one', 'three'])
- Reading from Partitioned Datasets
            dataset = pq.ParquetDataset(‘dataset_name_directory/’)
            table = dataset.read()
            table
- Transforming Parquet file into a Pandas DataFrame
            pdf = pq.read_pandas('example.parquet', columns=['two']).to_pandas()
            pdf
- Avoiding pandas index
            table = pa.Table.from_pandas(df, preserve_index=False)
            pq.write_table(table, 'example_noindex.parquet')
            t = pq.read_table('example_noindex.parquet')
            t.to_pandas()
- Check metadata
            parquet_file = pq.ParquetFile(‘example.parquet’)
            parquet_file.metadata
- See data schema
            parquet_file.schema
- Timestamp
            pq.write_table(table, where, coerce_timestamps='ms')
            pq.write_table(table, where, coerce_timestamps='ms', allow_truncated_timestamps=True)
- Compression
By default, Apache arrow uses snappy compression (not so compressed but easier access), although other codecs are allowed.
            pq.write_table(table, where, compression='snappy')
            pq.write_table(table, where, compression='gzip')
            pq.write_table(table, where, compression='brotli')
            pq.write_table(table, where, compression='none')
            pq.write_table(table, ‘example_diffcompr.parquet’, compression={b’one’: ‘snappy’, b’two’: ‘gzip’})
- Partitioned Parquet Table
            df = pd.DataFrame({‘one’: [1, 2.5, 3],
                            ‘two’: [‘Peru’, ‘Brasil’, ‘Canada’],
                            ‘three’: [True, False, True]},
                            index=list(‘abc’))
            table = pa.Table.from_pandas(df)
            pq.write_to_dataset(table, root_path=’dataset_name’,partition_cols=[‘one’, ‘two’])

- Apache Arrow with HDFS (Remote file-system)
- we can read or download all files from HDFS and interpret directly with Python.
- Connection
Host is the namenode, port is usually RPC or WEBHDFS more parameters like user, kerberos ticket are allow. It’s strongly recommended to read about the environmental variables needed.

            import pyarrow as pa
            host = '1970.x.x.x'
            port = 8022
            fs = pa.hdfs.connect(host, port)
Optional if your connection is made front a data or edge node is possible to use just
            fs = pa.hdfs.connect()

- Write Parquet files to HDFS

            pq.write_to_dataset(table, root_path=’dataset_name’, partition_cols=[‘one’, ‘two’], filesystem=fs)

- Read CSV from HDFS

            import pandas as pd
            from pyarrow import csv
            import pyarrow as pa
            fs = pa.hdfs.connect()
            with fs.open(‘iris.csv’, ‘rb’) as f:
                df = pd.read_csv(f, nrows = 10)
            df.head()

- Read Parquet File from HDFS

There is two forms to read a parquet file from HDFS

    - Using pandas and Pyarrow engine

            import pandas as pd
            pdIris = pd.read_parquet(‘hdfs:///iris/part-00000–27c8e2d3-fcc9–47ff-8fd1–6ef0b079f30e-c000.snappy.parquet’, engine=’pyarrow’)
            pdTrain.head()
    - Pyarrow.parquet

            import pyarrow.parquet as pq
            path = ‘hdfs:///iris/part-00000–71c8h2d3-fcc9–47ff-8fd1–6ef0b079f30e-c000.snappy.parquet’
            table = pq.read_table(path)
            table.schema
            df = table.to_pandas()
            df.head()

- Other files extensions

To accomplish reading any extension we’ll use the open function that returns a buffer object that many pandas function like read_sas, read_json could receive as input instead of a string URL.

    - SAS

            import pandas as pd
            import pyarrow as pa
            fs = pa.hdfs.connect()
            with fs.open(‘/datalake/airplane.sas7bdat’, ‘rb’) as f:
            sas_df = pd.read_sas(f, format='sas7bdat')
            sas_df.head()
            
    - Excel

            import pandas as pd
            import pyarrow as pa
            fs = pa.hdfs.connect()
            with fs.open(‘/datalake/airplane.xlsx’, ‘rb’) as f:
            g.download('airplane.xlsx')
            ex_df = pd.read_excel('airplane.xlsx')

    - JSON

            import pandas as pd
            import pyarrow as pa
            fs = pa.hdfs.connect()
            with fs.open(‘/datalake/airplane.json’, ‘rb’) as f:
            g.download('airplane.json')
            js_df = pd.read_json('airplane.json')

- Download files from HDFS

            import pandas as pd
            import pyarrow as pa
            fs = pa.hdfs.connect()
            with fs.open(‘/datalake/airplane.cs’, ‘rb’) as f:
                g.download('airplane.cs')

- Upload files to HDFS

            import pyarrow as pa
            fs = pa.hdfs.connect()
            with open(‘settings.xml’) as f:
                pa.hdfs.HadoopFileSystem.upload(fs, ‘/datalake/settings.xml’, f)

- Apache Arrow with Apache Spark
    - Speeding upconversion from Pandas Data Frame to Spark Data Frame
    - Speeding upconversion from Spark Data Frame to Pandas Data Frame
    - Using with Pandas UDF (a.k.a Vectorized UDFs)
- Ex.
from pyspark.sql import SparkSession
warehouseLocation = “/antonio”
spark = SparkSession\
.builder.appName(“demoMedium”)\
.config(“spark.sql.warehouse.dir”, warehouseLocation)\
.enableHiveSupport()\
.getOrCreate()
#Create test Spark DataFrame
from pyspark.sql.functions import rand
df = spark.range(1 << 22).toDF(“id”).withColumn(“x”, rand())
df.printSchema()
#Benchmark time
%time pdf = df.toPandas()
spark.conf.set(“spark.sql.execution.arrow.enabled”, “true”)
%time pdf = df.toPandas()
pdf.describe()















