# logs_analysis_project

# Create requsts view
```
CREATE OR REPLACE view requests
AS
  SELECT Count(*)   AS count,
         Date(time) AS date
  FROM   log
  GROUP  BY date
  ORDER  BY count DESC;
```
CREATE OR REPLACE view errors
AS
  SELECT Count(*)   AS count,
         Date(time) AS date
  FROM   log
  WHERE  status != '200 OK'
  GROUP  BY date
  ORDER  BY count DESC;

CREATE OR REPLACE view error_percent
AS
  SELECT requests.date,
         Round(( 100.0 * errors.count / requests.count ), 2) AS error_percent
  FROM   requests,
         errors
  WHERE  errors.date = requests.date; 
