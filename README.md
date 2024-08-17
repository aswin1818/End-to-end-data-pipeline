# Spotify's End-to-End Data Pipeline

This project focuses on designing and implementing a data pipeline specifically for extracting, transforming, and loading (ETL) Spotify's data to derive in-depth insights into the dynamics of artists, albums, and tracks within the Top 100 Global playlists. The pipeline initiates by retrieving data from the Spotify API, followed by processing the data to make it analysis-ready. The processed data is then loaded into Amazon S3, and Amazon Athena is utilized to query this data. AWS Glue plays a critical role in managing and cataloging metadata throughout the process.

## AWS Serverless Services Utilized:

- **AWS S3 (Simple Storage Service):** A highly scalable object storage service provided by AWS.
- **AWS Lambda:** A serverless compute service that allows running code in response to events, scaling the infrastructure automatically based on workload.
- **AWS CloudWatch:** A monitoring service that enables users to collect metrics, oversee logs, set alarms, and respond to changes in AWS resources.
- **AWS Glue:** A fully managed ETL service that simplifies the preparation and loading of data for analytics. AWS Glue Data Catalog acts as a metadata repository, facilitating data discovery and management within AWS.
- **AWS Athena:** An interactive query service that allows analyzing data directly in Amazon S3 using SQL. Being serverless, Athena scales automatically to accommodate large datasets.

## Languages/Packages Used:

- **Python (Pandas)**
- **SQL (Structured Query Language)**
- **SpotifyAPI (Spotipy Python package)**

## Architecture Overview:

The project involves a workflow where data is fetched from the Spotify API and stored in S3 using AWS Lambda. Two S3 buckets—`raw_data` and `transformed_data`—are created accordingly.

## Project Workflow:

1. **Data Extraction:** The pipeline starts by fetching data from the Spotify API and storing it in S3 via a Lambda function. Two S3 buckets are created: `raw_data` and `transformed_data`.
2. **Data Transformation:** Once the data is in S3, a transformation Lambda function is triggered. This function converts the `.json` files into tables and performs two key tasks:
   - Creating transformed data (artists, albums, songs) in the `transformed_data` S3 bucket using Pandas.
   - Moving older files to a `processed` bucket within the transformation bucket.
3. **Data Cataloging:** After the transformed data is stored in the S3 bucket, AWS Glue crawlers are set up to crawl the respective S3 buckets and ingest the data into a specified database.
4. **Data Analysis:** Finally, the data from the AWS Data Catalog tables is queried using Athena to extract meaningful insights from the songs, albums, and artists data.

## Execution Flow:

**Extract Data from Spotify API** → **Trigger Lambda Functions (Every 1 Day)** → **Run Extract Code** → **Store Raw Data in S3 Bucket** → **Trigger Transform Function on New Data Arrival** → **Transform Data and Load into AWS Data Catalog** → **Perform SQL Analysis Using Athena on Top 100 Songs**.

## Data Source:

- **Spotify API Developer Account:** [Spotify Developer](https://developer.spotify.com/) for access to Spotify Playlists.

## Key Considerations:

- **IAM Configuration:** Ensure IAM user roles and policies are properly configured for accessing AWS services and managing interactions between them.
- **AWS Glue Crawler Issues:** Issues encountered during the project included improperly formatted data fetched by AWS Glue Crawler. The default SerDe format (`"org.apache.hadoop.mapred.TextInputFormat"`) was replaced with `"org.apache.hadoop.hive.serde2.OpenCSVSerde"` to resolve comma separation issues.
- **Manual Adjustments in AWS Glue:** The AWS Glue Crawler also misread column names in the artist table, requiring manual adjustments through the Edit JSON option.
- **Date Format Conversion:** Although the Lambda function was coded to convert date-related columns into `datetime` format, AWS Glue Crawler identified them as STRING types. Use the `date_parse` function in Athena to convert these strings to date formats for SQL analysis.

## Future Enhancements:

- **Visualization:** The project can be further enhanced by importing Athena results into visualization tools like Tableau or Amazon QuickSight.
