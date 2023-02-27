# data-lake-aws
___
## Overview

Handle user base and song database data for a music streaming company, Sparkify. 

* Data resides in S3
* Directory of JSON logs on user activity on the app
* JSON metadata on the songs in their appa

Objective: build an ETL pipeline that extracts their data from S3, processes it with Apache Spark, and loads the data back into S3 as a set of dimensional tables


* s3://udacity-dend/song_data: static data about artists and songs Song-data example: ```{"num_songs": 1, "artist_id": "ARJIE2Y1187B994AB7", "artist_latitude": null, "artist_longitude": null, "artist_location": "", "artist_name": "Line Renaud", "song_id": "SOUPIRU12A6D4FA1E1", "title": "Der Kleine Dompfaff", "duration": 152.92036, "year": 0}```

* s3://udacity-dend/log_data: event data of service usage e.g. who listened what song, when, where, and with which client

Below, some figures about the example data set (results after running the etl.py):

* s3://udacity-dend/song_data: 14897 files
* s3://udacity-dend/log_data: 31 files

Project uses Python, AWS S3, and Apache Spark
- ETL pipeline to Extract data from JSON files stored in AWS S3, process (Transform) data with Apache Spark, and write (Load) data back to AWS S3 as Spark parquet files

___
## About Database
Sparkify analytics database (sparkifydb) schema has a star design, meaning it has one Fact Table containing business data, and supporting Dimension Tables. Star Database design a popular schema used in ETL pipelines. The Fact Table -- collecting business critical data -- answers a key question: what songs have users of the platform listened to

### Advantages and Disadvantages of this Approach
The Sparkify data-lake ETL provides a flexible solution for processing data. Advantages include:
* S3 is the lake storage. Cheaper than running an EMR cluster and easier to manage. No SysAdmin needed to keep a big data EMR cluster running
* No intermediate database
* Cheaper: S3 cheaper than powered-on EC2s. AWS resources are needed only during the processing step, not for collection or storage
* Spark is fast and processes data in RAM
* Schema on the fly means ease of use when leveraging Spark

Disadvantages:
* Data loading from S3 back to S3 is less performant than a solution that utilises a dedicated Database, since network is the slowest part of the ETL pipeline (compared to loading data from RAM or processing data in CPU)
* Is S3 the best object store for Spark parquet files...
* Common advice among Spark users: read data from S3, process with Spark, write output data to local parquet files, upload generated parquet files to S3 as a patch procedure

### JSON Data Structures

* *log_data*: log_data contains data about what users have done (columns: event_id, artist, auth, firstName, gender, itemInSession, lastName, length, level, location, method, page, registration, sessionId, song, status, ts, userAgent, userId)
* *song_data*: song_data contains data about songs and artists (columns: num_songs, artist_id, artist_latitude, artist_longitude, artist_location, artist_name, song_id, title, duration, year)


### Fact Table
songplays: song play data together with user, artist, and song info (songplay_id, start_time, user_id, level, song_id, artist_id, session_id, location, user_agent)

### Dimension Tables
users: user info (columns: user_id, first_name, last_name, gender, level)
songs: song info (columns: song_id, title, artist_id, year, duration)
artists: artist info (columns: artist_id, name, location, latitude, longitude)
time: detailed time info about song plays (columns: start_time, hour, day, week, month, year, weekday)
---
## How To
*Project has one script: *
* *etl.py*: Script runs Apache Spark SQL commands.
* Reads source data (JSON files) from S3 as Spark DataFrames
* Spark Dataframes are use to manipulate data
* Analytics are written back to S3 as Spark parquet files
* Output: JSON data -- the input -- is processed and analysed before being writeen back to S3 as Spark parquet files.