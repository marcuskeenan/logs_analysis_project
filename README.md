# logs_analysis_project
This project was created as part of my coursework for the Udacity [Full Stack Web Developer Nanodegree](https://www.udacity.com/course/full-stack-web-developer-nanodegree--nd004). The project was to create a reporting tool that prints out reports (in plain text) based on the data in the database. This reporting tool is a Python program using the psycopg2 module to connect to the PostgreSQL database called "news". After initializing the database with the sample data and creating a few custom views, you will be able to run the python code. The code will run a few queries to create a report that will print to the console and create a new plain text file in you directory.

## Getting Started

### Prerequisites
PostgreSQL
Download the data

Next, download the data here. You will need to unzip this file after downloading it. The file inside is called newsdata.sql. Put this file into the vagrant directory, which is shared with your virtual machine.

To build the reporting tool, you'll need to load the site's data into your local database. Review how to use the psql command in this lesson.

To load the data, cd into the vagrant directory and use the command psql -d news -f newsdata.sql.
Here's what this command does:

    psql — the PostgreSQL command line program
    -d news — connect to the database named news which has been set up for you
    -f newsdata.sql — run the SQL statements in the file newsdata.sql

Running this command will connect to your installed database server and execute the SQL commands in the downloaded file, creating tables and populating them with data. 

### Create requests view
Run this query to create a new view called requests which returns the total article request counts per day.
```
CREATE OR REPLACE view requests
AS
  SELECT Count(*)   AS count,
         Date(time) AS date
  FROM   log
  GROUP  BY date
  ORDER  BY count DESC;
```
### Create errors view
Run this query to create a new view called errors which returns the number of failed article request counts per day.
```
CREATE OR REPLACE view errors
AS
  SELECT Count(*)   AS count,
         Date(time) AS date
  FROM   log
  WHERE  status != '200 OK'
  GROUP  BY date
  ORDER  BY count DESC;
```
### Create error_percent view
Run this query to create a new view called error_percent which will get the data from the requests view and the errors view to calculate the percentage of article request errors per day.
```
CREATE OR REPLACE view error_percent
AS
  SELECT requests.date,
         Round(( 100.0 * errors.count / requests.count ), 2) AS error_percent
  FROM   requests,
         errors
  WHERE  errors.date = requests.date;
 ``` 
