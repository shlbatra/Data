- Creating a Data Pipeline
    - https://towardsdatascience.com/creating-a-data-pipeline-with-spark-google-cloud-storage-and-big-query-a72ede294f4c
- github repo
    - https://github.com/jaumpedro214/posts
- The pipeline idea is simple, download the CSV files to the local machine, convert them into a Delta-Lake table stored in a GCS bucket, do the transformations needed over this delta table, and save the results in a Big Query Table that can be easily consumed by other downstream tasks.