# solid-waste-datawarehouse
# Data Warehouse for Solid Waste Management – Final Project

## Overview
This project was completed as part of the IBM BI Analyst Professional Certificate on Coursera "Data Warehouse Fundamentals". As a data engineer for a solid waste management company in Brazil, I designed and implemented a data warehouse to analyze waste collection data across major cities. The data warehouse enables reporting on:

- Total waste collected per year, per month, and per quarter per city
- Total waste collected per year per truck type
- Total waste collected per truck type per station per city

The project involved:

- Designing dimension and fact tables using a star schema model
- Creating the schema in PostgreSQL via pgAdmin4
- loading CSV data into the tables
- Writing advanced SQL aggregation queries using GROUPING SETS, ROLLUP, and CUBE
- Creating a materialized view for performance optimization

## Repository Structure

```
FinalProject/
├── csv_exports/
│   ├── DimDate.csv
│   ├── DimTruck.csv
│   ├── DimStation.csv
│   └── FactTrips.csv
├── sql/
│   ├── Create_DimDate.sql
│   ├── Create_DimTruck.sql
│   ├── Create_DimStation.sql
│   ├── Create_FactTrips.sql
│   ├── Import_DimDate.sql
│   ├── Import_DimTruck.sql
│   ├── Import_DimStation.sql
│   └── Import_FactTrips.sql
├── screenshots/
│   ├── MyDimDate.jpg
│   ├── MyDimWaste.jpg
│   ├── MyDimZone.jpg
│   ├── MyFactTrips.jpg
│   ├── DimDate.jpg
│   ├── DimTruck.jpg
│   ├── DimStation.jpg
│   ├── FactTrips.jpg
│   ├── groupingsets.jpg
│   ├── rollup.jpg
│   ├── cube.jpg
│   └── mv.jpg
└── README.md
```

## Exercises and Tasks

### Exercise 1 – Design a Data Warehouse Schema

Task 1: Design the Dimension Table MyDimDate
Field List:
```
dateid
year
month
monthname
day
weekday
weekdayname
quarter
quartername
```
Screenshot: MyDimDate.jpg

Task 2: Design the Dimension Table MyDimWaste
Field List:
```
wasteid
wastetype
```
Screenshot: MyDimWaste.jpg

Task 3: Design the Dimension Table MyDimZone
Field List:
```
zoneid
zonename
```
Screenshot: MyDimZone.jpg

Task 4: Design the Fact Table MyFactTrips
Field List:
```
tripid
dateid
wasteid
zoneid
tons_collected
```
Screenshot: MyFactTrips.jpg

### Exercise 2 – Create Schema for Data Warehouse on PostgreSQL
Create a new database named Project in pgAdmin4 and run the following SQL statements:
Task 5: Create the Dimension Table MyDimDate
```sql
CREATE TABLE MyDimDate (
    dateid INT PRIMARY KEY,
    date DATE NOT NULL,
    year INT NOT NULL,
    quarter INT NOT NULL,
    quartername VARCHAR(2) NOT NULL,
    month INT NOT NULL,
    monthname VARCHAR(255) NOT NULL,
    day INT NOT NULL,
    weekday INT NOT NULL,
    weekdayname VARCHAR(255) NOT NULL
);
```
Screenshot: MyDimDate.jpg

Task 6: Create the Dimension Table MyDimWaste
```sql
CREATE TABLE MyDimWaste (
    wasteid INT PRIMARY KEY,
    wastetype VARCHAR(255) NOT NULL
);
```
Screenshot: MyDimWaste.jpg

Task 7: Create the Dimension Table MyDimZone
```sql
CREATE TABLE MyDimZone (
    zoneid INT PRIMARY KEY,
    zonename VARCHAR(255) NOT NULL
);
```
Screenshot: MyDimZone.jpg

Task 8: Create the Fact Table MyFactTrips
```sql
CREATE TABLE MyFactTrips (
    tripid INT PRIMARY KEY,
    dateid INT NOT NULL,
    wasteid INT NOT NULL,
    zoneid INT NOT NULL,
    tons_collected DECIMAL(10,2) NOT NULL,
    FOREIGN KEY (dateid) REFERENCES MyDimDate(dateid),
    FOREIGN KEY (wasteid) REFERENCES MyDimWaste(wasteid),
    FOREIGN KEY (zoneid) REFERENCES MyDimZone(zoneid)
);
```
Screenshot: MyFactTrips.jpg

### Exercise 3 – Load Data into the Data Warehouse

Task 9: Load Data into DimDate
```sql
SELECT * FROM DimDate LIMIT 5;
```
Screenshot: DimDate.jpg

Task 10: Load Data into DimTruck
```sql
SELECT * FROM DimTruck LIMIT 5;
```
Screenshot: DimTruck.jpg

Task 11: Load Data into DimStation
```sql
SELECT * FROM DimStation LIMIT 5;
```
Screenshot: DimStation.jpg

Task 12: Load Data into FactTrips
```sql
SELECT * FROM FactTrips LIMIT 5;
```
Screenshot: FactTrips.jpg

### Exercise 4 – Write Aggregation Queries and Create Materialized View

Task 13: Create a Grouping Sets Query
```sql
SELECT 
    Dimstation.stationid, 
    Dimtruck.trucktype, 
    SUM(Facttrip.wastecollectedmonth) AS total_waste_collected
FROM 
    Facttrip
JOIN 
    Dimtruck ON Facttrip.Truckid = Dimtruck.Truckid
JOIN 
    Dimstation ON Facttrip.Stationid = Dimstation.Stationid
GROUP BY 
    GROUPING SETS (
        (Dimstation.stationid, Dimtruck.trucktype),
        (Dimstation.stationid),
        (Dimtruck.trucktype),
        ()
    );
```
Screenshot: groupingsets.jpg

Task 14: Create a Rollup Query
```sql
SELECT 
    Dimdate.year, 
    Dimstation.city, 
    Dimstation.stationid, 
    SUM(Facttrips.wastecollected) AS total_waste_collected
FROM 
    Facttrips
JOIN 
    Dimstation ON Facttrips.Stationid = Dimstation.Stationid
JOIN 
    Dimdate ON Facttrips.DateId = Dimdate.dateID
GROUP BY 
    ROLLUP(Dimdate.year, Dimstation.city, Dimstation.stationid);
```
Screenshot: rollup.jpg

Task 15: Create a Cube Query
```sql
SELECT 
    Dimdate.year, 
    Dimstation.city, 
    Dimstation.stationid, 
    AVG(Facttrips.wastecollected) AS avg_waste_collected
FROM 
    Facttrips
JOIN 
    Dimstation ON Facttrips.Stationid = Dimstation.Stationid
JOIN 
    Dimdate ON Facttrips.DateId = Dimdate.dateID
GROUP BY 
    CUBE(Dimdate.year, Dimstation.city, Dimstation.stationid);
```
Screenshot: cube.jpg

Task 16: Create a Materialized View
```sql
CREATE MATERIALIZED VIEW max_waste_stats AS
SELECT 
    Dimstation.city, 
    Dimstation.stationid, 
    Dimtruck.trucktype, 
    MAX(Facttrips.wastecollected) AS max_waste_collected
FROM 
    Facttrips
JOIN 
    Dimstation ON Facttrips.Stationid = Dimstation.Stationid
JOIN 
    Dimtruck ON Facttrips.Truckid = Dimtruck.Truckid
GROUP BY 
    Dimstation.city, Dimstation.stationid, Dimtruck.trucktype;
```
Screenshot: mv.jpg

License and Acknowledgments
This project was completed as part of the Data Warehousing and BI Analyst Professional Certificate on Coursera. It demonstrates a comprehensive understanding of data warehousing concepts, from schema design to advanced SQL queries and materialized views.

Author: Chinelo Lydia Nweke
Contact: LinkedIn: https://www.linkedin.com/in/chinelo-nweke-62883516a?utm_source=share&utm_campaign=share_via&utm_content=profile&utm_medium=android_app
```
