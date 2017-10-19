# logs_analysis_project

### Create requests view
Enter this query to create a new view called requests which returns the total article request counts per day.
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
Enter this query to create a new view called errors which returns the number of failed article request counts per day.
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
Enter this query to create a new view called error_percent which will get the data from the requests view and the errors view to calculate the percentage of article request errors per day.
CREATE OR REPLACE view error_percent
AS
  SELECT requests.date,
         Round(( 100.0 * errors.count / requests.count ), 2) AS error_percent
  FROM   requests,
         errors
  WHERE  errors.date = requests.date; 
