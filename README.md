# logs_analysis_project
## About
This project was created as part of my coursework for the Udacity [Full Stack Web Developer Nanodegree](https://www.udacity.com/course/full-stack-web-developer-nanodegree--nd004). The goal was to create a reporting tool that prints out reports (in plain text) based on the data in the database. This reporting tool consists of a Python program using the [psycopg2](http://initd.org/psycopg/docs/) module to connect to the PostgreSQL database. 

After initializing the database with the sample data (see database setup below), creating a few custom views and cloning this repo, you will be able to run the python code. The code will run a few queries to create a report that will print to the console and create a new plain text file in you log_reports directory. This example uses a preconfigured Linux Ubuntu VM running on VirtualBox with Vagrant.
There are there questions answered by the code:
1. What are the most popular three articles of all time?
2. Who are the most popular article authors of all time?
3. On which days did more than 1% of requests lead to errors? 

## Getting Started

### Prerequisites
[VirtualBox](https://www.virtualbox.org/wiki/Downloads),<br>
[Vagrant](https://www.vagrantup.com/downloads.html),<br>
Virtual Machine Config [FSND-Virtual-Machine.zip ](https://d17h27t6h515a5.cloudfront.net/topher/2017/August/59822701_fsnd-virtual-machine/fsnd-virtual-machine.zip)<br>

## Setup
Once you have the Vagrant VM running on VirtualBox with the FSDN-Virtual-Machine running, you are ready to setup the database with the sample data.

### Setup the database
Download the sample data [here](https://d17h27t6h515a5.cloudfront.net/topher/2016/August/57b5f748_newsdata/newsdata.zip). You will need to unzip this file after downloading it. The file inside is called newsdata.sql. 
To build the reporting tool, you'll need to load the site's data into your local database. 
To load the data, cd into the directory and use the command psql -d news -f newsdata.sql.
Here's what this command does:
```
    psql — the PostgreSQL command line program
    -d news — connect to the database named news which has been set up for you
    -f newsdata.sql — run the SQL statements in the file newsdata.sql
```
Running this command will connect to your installed database server and execute the SQL commands in the downloaded file, creating tables and populating them with data.
The database contains three tables: articles, authors and log. These tables are a representation of a log file that stores web request for the related articles.
```          List of relations
 Schema |   Name   | Type  |  Owner  
--------+----------+-------+---------
 public | articles | table | vagrant
 public | authors  | table | vagrant
 public | log      | table | vagrant
 ```

### Create custom database views
Important! You must create the following views! The python code queries these database views and wll not function without!


Run this query to create a new view called requests which returns the total article request counts per day.

In your /vagrant directory, enter:
```
psql news
```


This will take you to the database command line. Here, run the following statements:

#### Create database view called "article_views"
```
CREATE OR REPLACE view article_views AS SELECT title, count(title) AS views from articles, log WHERE log.path = concat('/article/',articles.slug) GROUP BY title ORDER BY views DESC;
```
#### Create database view called "author_views"
```
CREATE OR REPLACE view author_views AS SELECT authors.name, count(articles.author) AS views from articles, log, authors WHERE log.path = concat('/article/',articles.slug) and articles.author = authors.id GROUP BY authors.name ORDER BY views DESC;
```
#### Create database view called "requests"
```
CREATE OR REPLACE view requests AS SELECT Count(*) AS count, Date(time) AS date FROM log GROUP BY date ORDER  BY count DESC;
```
#### Create database view called "errors"
Run this query to create a new view called errors which returns the number of failed article request counts per day.
```
CREATE OR REPLACE view errors AS SELECT Count(*) AS count, Date(time) AS date FROM log WHERE status != '200 OK' GROUP BY date ORDER BY count DESC;
```
#### Create error_percent view
Run this query to create a new view called error_percent which will get the data from the requests view and the errors view to calculate the percentage of article request errors per day.
```
CREATE OR REPLACE view error_percent AS SELECT requests.date, Round(( 100.0 * errors.count / requests.count ), 2) AS error_percent FROM requests, errors WHERE errors.date = requests.date;
 ``` 
 ## Run this code
 open you terminal and enter:
 ```
 git clone https://github.com/sigwaveLabs/logs_analysis_project.git
 ```
 cd to the  project ``` cd log_analysis_project``` and run ```python logsreport.py``` 
 
 After running the code, you will see the report print to the terminal and create a new plain text file in the /log_reports directory.
 
 That's it:)
