## DEFORESTATION AMONG COUNTRIES AND THEIR INCOME_GROUPS (1990-2016) - AN SQL PROJECT.
![image](https://github.com/Cleancent26/DATA_ANALYTICS/assets/159614822/d1a9714e-9006-4216-aed3-acad2e89b933)
### TABLE OF CONTENTS.
- [Project overview](#Project Overview)
- [Data Source](#Data Source)
- [Tools](#Tools)
- [Exploratory Data Analysis](#Exploratory Data Analysis)
- [Data Analysis](#Data Analysis)
- [Results](#Results)
- [Recommendations](#Recommendations)
- [Limitations](#Limitations)
- [References](#References)

### Project Overview
#### This project work focusses on the activities of deforestation among various countries over the scope of twenty seven years (1990-2016). it tends to look at nations deforestation patterns and gain insights and trends from the deforestation activities.

### Data Source 
#### Data used for this project are sourced from secondary sources. For this project, my data are in three tables, which are; Forest_Area, Land_Area and Region.

### Tools 
#### In this project, we will be making use of SQL for carrying the various processes which are mainly Data Cleaning,Data Analysing and also giving valuables insights with recommendations.

### DATA ASSESSMENT AND CLEANING.

#### EFFECTING CHANGES TO COUNTRY NAME FROM 'BAHAMAS, THE' TO 'THE BAHAMAS' AND'GAMBIA, THE' TO 'THE GAMBIA'
```
UPDATE FOREST_AREA SET COUNTRY_NAME = 
CASE 
	WHEN COUNTRY_NAME = 'BAHAMAS, THE' THEN 'The Bahamas'
	WHEN COUNTRY_NAME = 'GAMBIA, THE' THEN 'The Gambia'
else country_name
END;
```
#### CHANGING OF COUNTRY_NAME FROM 'CABO VERDE' TO 'Cape Verde'
```
UPDATE FOREST_AREA 
SET COUNTRY_NAME = 'Cape Verde'
WHERE COUNTRY_NAME =
(SELECT DISTINCT COUNTRY_NAME FROM FOREST_AREA
WHERE COUNTRY_NAME = 'CABO VERDE'
);
```
#### COUNTRIES WITH  NULL VALUES IN THE FOREST_AREA_SQKM COLUMN FOR THE ENTIRE SCOPE(1989-2016)  USING SUBQUERY 
```
SELECT COUNTRY_NAME FROM 
(SELECT DISTINCT COUNTRY_NAME,YEAR, 
DENSE_RANK() OVER(PARTITION BY COUNTRY_NAME ORDER BY YEAR DESC ) Y FROM Forest_Area
WHERE forest_area_sqkm IS NULL AND YEAR >= 1989  ) W 
WHERE Y = 27
;
```
#### DELETED COUNTRIES WITH  NULL VALUES IN THE FOREST_AREA_SQKM COLUMN FOR THE ENTIRE SCOPE(1989-2016)  USING SUBQUERY 
```
DELETE FROM Forest_Area WHERE country_name IN 
(SELECT COUNTRY_NAME FROM 
(SELECT DISTINCT COUNTRY_NAME,YEAR, 
DENSE_RANK() OVER(PARTITION BY COUNTRY_NAME ORDER BY YEAR DESC ) Y FROM Forest_Area
WHERE forest_area_sqkm IS NULL AND YEAR >= 1989  ) W 
WHERE Y = 27)
;
```
#### DELETED COUNTRIES WHERE 21 OUT OF THE 27 forest_area_sqkm IS NULL
 ```
DELETE FROM Forest_Area WHERE forest_area_sqkm IS NULL AND country_name  IN 
 (SELECT COUNTRY_NAME FROM
 (SELECT DISTINCT country_name,YEAR,
 DENSE_RANK() OVER(PARTITION BY COUNTRY_NAME ORDER BY YEAR DESC) U FROM FOREST_AREA
 WHERE forest_area_sqkm IS NULL) H 
 WHERE U =  21)
;
```
####  DELETED COUNTRIES WHERE 21 OUT OF THE 27 forest_area_sqkm IS NULL
 ```
DELETE FROM Forest_Area WHERE country_name  =
 (SELECT COUNTRY_NAME FROM
 (SELECT DISTINCT country_name,YEAR,
 DENSE_RANK() OVER(PARTITION BY COUNTRY_NAME ORDER BY YEAR DESC) U FROM FOREST_AREA
 WHERE forest_area_sqkm IS NULL) H 
 WHERE U =  21)
;
```
#### DELETED COUNTRIES WITH ONLY 6 OUT OF THE 27 forest_area_sqkm THAT IS NOT NULL i.e SUDAN 
```
DELETE FROM Forest_Area WHERE COUNTRY_NAME =
 (SELECT DISTINCT country_name FROM Forest_Area
  WHERE  country_name =  'SUDAN'  )
;
```
#### DELETED COUNTRIES WITH ONLY 6 OUT OF THE 27 forest_area_sqkm THAT IS NOT NULL i.e SOUTH SUDAN
```
DELETE FROM Forest_Area WHERE COUNTRY_NAME =
 (SELECT DISTINCT country_name FROM Forest_Area
  WHERE  country_name =  'SOUTH SUDAN'  )
;
```
#### UPDATED forest_area_sqkm FOR ETHIOPIA BETWEEN THE YEAR 1990-1992 WHERE forest_area_sqkm IS NULL WITH THE AVERAGE VALUE(MEAN) OF forest_area_sqkm 
```
UPDATE Forest_Area 
	SET forest_area_sqkm =(SELECT
	AVG(forest_area_sqkm)  FROM FOREST_AREA WHERE country_name = 'ETHIOPIA')
WHERE COUNTRY_NAME = 'ETHIOPIA' AND YEAR BETWEEN 1990 AND 1992
;
```
#### CONVERTED AND UPDATED THE INSERTED FLOAT AVERAGE VALUE TO INTEGER FOR ETHIOPIA BETWEEN YEAR 1990-1992 
```
UPDATE FOREST_AREA SET forest_area_sqkm =(
SELECT CAST(forest_area_sqkm AS INT) GROUPED_INT FROM FOREST_AREA
WHERE country_name = 'ETHIOPIA' AND YEAR BETWEEN 1990 AND 1992
GROUP BY CAST (forest_area_sqkm AS INT))
WHERE country_name = 'ETHIOPIA' AND YEAR BETWEEN 1990 AND 1992
;
```
#### USING THE MOST OCCURING forest_area_sqkm (MODE) TO FILL IN THE VALUE FOR St. Martin (French part) FOR YEAR 2016 
```
UPDATE Forest_Area 
SET forest_area_sqkm = 
(SELECT forest_area_sqkm FROM 
(SELECT DISTINCT(forest_area_sqkm),COUNT(forest_area_sqkm) Y 
FROM Forest_Area
WHERE COUNTRY_NAME = 'St. Martin (French part)'
GROUP BY forest_area_sqkm) AS A
WHERE Y = 26)
WHERE COUNTRY_NAME = 'St. Martin (French part)' AND YEAR = 2016
;
```
#### DELETED 'WORLD' FROM COUNTRY_NAME AS OUTLYERS FROM FOREST_AREA
```
DELETE FROM FOREST_AREA WHERE COUNTRY_NAME =
(SELECT DISTINCT COUNTRY_NAME FROM  FOREST_AREA 
WHERE COUNTRY_NAME = 'WORLD')
;
```
#### START OF LAND_AREA
```
SELECT * FROM LAND_AREA 
	WHERE COUNTRY_NAME ='SOUTH SUDAN' ;

SELECT DISTINCT COUNTRY_NAME FROM LAND_AREA 
	ORDER BY COUNTRY_NAME ASC;

SELECT DISTINCT country_name FROM LAND_AREA 
	WHERE TOTAL_AREA_SQ_MI IS NULL;
```
#### DELETED 'WORLD' FROM COUNTRY_NAME AS OUTLYERS FROM LAND_AREA
```
DELETE FROM LAND_AREA WHERE COUNTRY_NAME =
(SELECT DISTINCT COUNTRY_NAME FROM  LAND_AREA 
WHERE COUNTRY_NAME = 'WORLD')
;
```
#### USING PARTITION TO GET EACH COUNTRY AND THE YEAR WHERE TOTAL_AREA_SQ_MI IS NULL
```
SELECT DISTINCT(COUNTRY_NAME),YEAR,TOTAL_AREA_SQ_MI, 
DENSE_RANK() OVER(PARTITION BY COUNTRY_NAME ORDER BY YEAR DESC ) MODE_RANK FROM LAND_AREA
WHERE TOTAL_AREA_SQ_MI IS NULL;
```
#### USING SUBQUERY TO GET DISTINCT COUNTRY_NAME AND THEIR HIGHEST COUNT(MODE_RANK) OF NULL VALUE FOR TOTAL_AREA_SQ_MI
```
SELECT DISTINCT(COUNTRY_NAME),MAX(T) FROM
(SELECT DISTINCT(COUNTRY_NAME),YEAR,TOTAL_AREA_SQ_MI, 
DENSE_RANK() OVER(PARTITION BY COUNTRY_NAME ORDER BY YEAR DESC ) T FROM LAND_AREA
WHERE TOTAL_AREA_SQ_MI IS NULL) U
GROUP BY COUNTRY_NAME;
```
#### DELETED COUNTRIES WITH  NULL VALUES IN THE LAND_AREA_SQKM COLUMN FOR THE ENTIRE SCOPE(1989-2016) i.e SUDAN AND SOUTH SUDAN USING SUBQUERY 
```
 DELETE FROM LAND_AREA WHERE COUNTRY_NAME IN
( SELECT DISTINCT COUNTRY_NAME FROM
(SELECT DISTINCT(COUNTRY_NAME),YEAR,TOTAL_AREA_SQ_MI, 
DENSE_RANK() OVER(PARTITION BY COUNTRY_NAME ORDER BY YEAR DESC ) T FROM LAND_AREA
WHERE TOTAL_AREA_SQ_MI IS NULL) U
WHERE T = 27) 
;
```
#### CROSS CHECKING FOR LAND_AREA
```
 SELECT DISTINCT country_name FROM Land_Area
 WHERE  total_area_sq_mi IS  NULL;
```
#### UPDATED NULL VALUE IN total_area_sq_mi FOR BELGIUM  FOR YEAR BETWEEN 1990-1991
```
 UPDATE Land_Area 
 SET total_area_sq_mi = 
(SELECT total_area_sq_mi FROM 
(SELECT total_area_sq_mi,COUNT(total_area_sq_mi) AS MODE_VALUE FROM LAND_AREA
WHERE country_name = 'BELGIUM'
GROUP BY total_area_sq_mi
) 
 R WHERE MODE_VALUE =  17
)  WHERE COUNTRY_NAME = 'BELGIUM' AND YEAR  <= 1999 ;
```
#### EVALUATION OF LAND_AREA
```
 SELECT * FROM Land_Area
 WHERE total_area_sq_mi IS  NULL
  ORDER BY country_name;
  ```
#### EVALUATION OF LAND_AREA
```
 SELECT DISTINCT country_name,YEAR FROM Land_Area
 WHERE  total_area_sq_mi IS  NULL AND COUNTRY_NAME = 'Palau';
```
#### UPDATED NULL VALUE FOR Luxembourg IN total_area_sq_mi FOR YEAR BETWEEN 1990-1991
```
 UPDATE Land_Area 
 SET total_area_sq_mi = 
(SELECT total_area_sq_mi FROM 
(SELECT total_area_sq_mi,COUNT(total_area_sq_mi) AS MODE_VALUE FROM LAND_AREA
	WHERE country_name = 'Luxembourg'
		GROUP BY total_area_sq_mi
) 
 R WHERE MODE_VALUE =  17
)  WHERE COUNTRY_NAME = 'Luxembourg' AND YEAR  <= 1999 ;
```
#### UPDATED NULL VALUE FOR Marshall Islands IN total_area_sq_mi FOR YEAR 1990
```
 UPDATE Land_Area 
 SET total_area_sq_mi = 
(SELECT total_area_sq_mi FROM 
(SELECT total_area_sq_mi,COUNT(total_area_sq_mi) AS MODE_VALUE FROM LAND_AREA
WHERE country_name = 'Marshall Islands'
GROUP BY total_area_sq_mi
) 
 R WHERE MODE_VALUE =  26
)  WHERE COUNTRY_NAME = 'Marshall Islands' AND YEAR  = 1990 ;
```
#### UPDATED NULL VALUE FOR Micronesia, Fed. Sts. IN total_area_sq_mi FOR YEAR 1990
```
 UPDATE Land_Area 
 SET total_area_sq_mi = 
(SELECT total_area_sq_mi FROM 
(SELECT total_area_sq_mi,COUNT(total_area_sq_mi) AS MODE_VALUE FROM LAND_AREA
WHERE country_name = 'Micronesia, Fed. Sts.'
GROUP BY total_area_sq_mi
) 
 R WHERE MODE_VALUE =  26
)  WHERE COUNTRY_NAME = 'Micronesia, Fed. Sts.' AND YEAR  = 1990 ;
```
#### UPDATED NULL VALUE FOR Northern Mariana Islands IN total_area_sq_mi FOR YEAR 1990 USING MODE
```
 UPDATE Land_Area 
 SET total_area_sq_mi = 
(SELECT total_area_sq_mi FROM 
(SELECT total_area_sq_mi,COUNT(total_area_sq_mi) AS MODE_VALUE FROM LAND_AREA
WHERE country_name = 'Northern Mariana Islands'
GROUP BY total_area_sq_mi
) 
 R WHERE MODE_VALUE =  26
)  WHERE COUNTRY_NAME = 'Northern Mariana Islands' AND YEAR  = 1990 ;
```
#### UPDATED NULL VALUE FOR Palau IN total_area_sq_mi FOR YEAR IN 1990 USING MODE
```
 UPDATE Land_Area 
 SET total_area_sq_mi = 
(SELECT total_area_sq_mi FROM 
(SELECT total_area_sq_mi,COUNT(total_area_sq_mi) AS MODE_VALUE FROM LAND_AREA
WHERE country_name = 'Palau'
GROUP BY total_area_sq_mi
) 
 R WHERE MODE_VALUE =  26
)  WHERE COUNTRY_NAME = 'Palau' AND YEAR  = 1990 ;
```
#### CHANGING OF COUNTRY_NAME FROM 'BAHAMAS, THE' TO 'THE BAHAMAS'
```
UPDATE LAND_AREA 
SET COUNTRY_NAME = 'The Bahamas'
WHERE COUNTRY_NAME =
(SELECT DISTINCT COUNTRY_NAME FROM LAND_AREA
WHERE COUNTRY_NAME = 'BAHAMAS, THE'
);
```
#### CHANGING OF COUNTRY_NAME FROM 'GAMBIA, THE' TO 'THE GAMBIA'
```
UPDATE LAND_AREA 
SET COUNTRY_NAME = 'The Gambia'
WHERE COUNTRY_NAME =
(SELECT DISTINCT COUNTRY_NAME FROM LAND_AREA
WHERE COUNTRY_NAME = 'Gambia, The'
);
```
#### CHANGING OF COUNTRY_NAME FROM 'CABO VERDE' TO 'CAPE VERDE'
```
UPDATE LAND_AREA 
SET COUNTRY_NAME = 'CAPE VERDE'
WHERE COUNTRY_NAME =
(SELECT DISTINCT COUNTRY_NAME FROM LAND_AREA
WHERE COUNTRY_NAME = 'CABO VERDE'
);
```
#### EVALUATION FOR LAND_AREA
```
SELECT * FROM Land_Area
 WHERE total_area_sq_mi IS  NULL
  ORDER BY country_name;
```
#### START OF REGION
```
  SELECT * FROM REGION;
```
#### CHANGING OF COUNTRY_NAME FROM 'CABO VERDE' TO 'CAPE VERDE'
```
UPDATE REGION 
SET COUNTRY_NAME = 'Cape Verde'
WHERE COUNTRY_NAME =
(SELECT  COUNTRY_NAME FROM REGION
WHERE COUNTRY_NAME = 'CABO VERDE'
);
```
#### CHANGING OF COUNTRY_NAME FROM 'GAMBIA, THE' TO 'THE GAMBIA'
```
UPDATE REGION 
SET COUNTRY_NAME = 'The Gambia'
	WHERE COUNTRY_NAME =
(SELECT  COUNTRY_NAME FROM REGION
	WHERE COUNTRY_NAME = 'Gambia, The'
);
```
#### CHANGING OF COUNTRY_NAME FROM 'BAHAMAS, THE' TO 'THE BAHAMAS'
```
UPDATE REGION 
SET COUNTRY_NAME = 'The Bahamas'
WHERE COUNTRY_NAME =
(SELECT  COUNTRY_NAME FROM REGION
WHERE COUNTRY_NAME = 'BAHAMAS, THE'
);
```
#### DELETED 'WORLD' FROM COUNTRY_NAME AS OUTLYERS FROM REGION
```
DELETE FROM REGION WHERE COUNTRY_NAME =
(SELECT DISTINCT COUNTRY_NAME FROM REGION 
WHERE COUNTRY_NAME = 'WORLD')
;
```
#### Exploratory DAta Analysis(EDA)
 EDA tends to seek answers to several thoughts that appear unclear by providing more detailing and precise insights with the use of the Data. For the purpose of this project, focus is on the following grey areas for clarity and precision;
 1. WHAT ARE THE TOTAL NUMBER OF COUNTRIES INVOLVED IN DEFORETATION?
2. SHOW THE INCOME GROUPS OF COUNTRIES HAVING TOTAL AREA RANGING FROM 75,000 TO 150,000 SQUARE METER
3.CALCULATE AVG. AREA IN SQUARE MILES FOR COUNTRIES IN THE 'UPPER MIDDLE INCOME REGION'	COMPARE THE RESULT WITH THE REST OF THE
CATEGORIES
4.DETERMINE THE TOTAL FOREST AREA IN SQUARE KM FOR COUNTRIES IN THE 'HIGH INCOME' GROUP
	COMPARE RESULT WITH THE REST OF THE INCOME CATEGORIES
5. SHOW COUNTRIES FROM EACH REGION (CONTINENT) HAVING THE HIGHEST TOTAL FOREST AREAS.


   ### Data Analysis
#####  WHAT ARE THE TOTAL NUMBER OF COUNTRIES INVOLVED IN DEFORETATION?
  ```
   SELECT COUNT(COUNTRY_NAME) AS COUNTRY_INVOLVED_IN_DEFORESTATION FROM (
SELECT COUNTRY_NAME FROM
(SELECT COUNTRY_NAME,YEAR,FOREST_AREA_SQKM, 
	RANK () OVER (PARTITION BY COUNTRY_NAME ORDER BY FOREST_AREA_SQKM) RANKS
FROM FOREST_AREA) U
WHERE RANKS = 2
 ) B;
   ```
![q1t](https://github.com/Cleancent26/DATA_ANALYTICS/assets/159614822/39a1229b-552e-47e4-bedb-788b83e53dc1)

##### SHOW THE INCOME GROUPS OF COUNTRIES HAVING TOTAL AREA RANGING FROM 75,000 TO 150,000 SQUARE METER
```
SELECT DISTINCT L.COUNTRY_NAME, R.INCOME_GROUP FROM REGION R LEFT JOIN LAND_AREA L ON R.COUNTRY_CODE = L.COUNTRY_CODE WHERE TOTAL_AREA_SQ_MI BETWEEN  75000 AND 150000
```
![q2t](https://github.com/Cleancent26/DATA_ANALYTICS/assets/159614822/bb6c92b7-eeb1-4f9c-8520-a92833eff37c)

##### CALCULATE AVG. AREA IN SQUARE MILES FOR COUNTRIES IN THE 'UPPER MIDDLE INCOME REGION' COMPARE THE RESULT WITH THE REST OF THE
CATEGORIES
```
SELECT R.COUNTRY_NAME,AVG(L.TOTAL_AREA_SQ_MI) AVG_OF_TOTAL_AREA_SQ_MI,R.INCOME_GROUP FROM REGION R LEFT JOIN LAND_AREA L ON R.COUNTRY_CODE = L.COUNTRY_CODE WHERE INCOME_GROUP =  'UPPER MIDDLE INCOME'
GROUP BY R.COUNTRY_NAME,R.INCOME_GROUP
ORDER BY AVG(L.TOTAL_AREA_SQ_MI) DESC;
```
![q3t](https://github.com/Cleancent26/DATA_ANALYTICS/assets/159614822/415fdd7e-d3cf-4234-96ad-422d3069fc22)

 ```
SELECT R.INCOME_GROUP, AVG(L.TOTAL_AREA_SQ_MI) AVG_AREA_SQ_MI
FROM REGION R JOIN LAND_AREA L ON R.COUNTRY_CODE = L.COUNTRY_CODE
GROUP BY R.INCOME_GROUP
HAVING INCOME_GROUP IN 
('LOWER MIDDLE INCOME','UPPER MIDDLE INCOME','HIGH INCOME','LOW INCOME')
ORDER BY AVG(L.TOTAL_AREA_SQ_MI) DESC;
```
![Q3OTHERS](https://github.com/Cleancent26/DATA_ANALYTICS/assets/159614822/a9614f25-ebf1-4422-86d1-3300acdfbd96)

DETERMINE THE TOTAL FOREST AREA IN SQUARE KM FOR COUNTRIES IN THE 'HIGH INCOME' GROUP,COMPARE RESULT WITH THE REST OF THE INCOME CATEGORIES. 
```
SELECT R.COUNTRY_NAME,SUM(F.FOREST_AREA_SQKM) TOTAL_FOREST_AREA_SQKM,R.INCOME_GROUP  
FROM REGION R RIGHT JOIN FOREST_AREA F ON R.COUNTRY_CODE = F.COUNTRY_CODE
WHERE INCOME_GROUP =  'HIGH INCOME'
GROUP BY R.COUNTRY_NAME,R.INCOME_GROUP 
ORDER BY SUM(F.FOREST_AREA_SQKM) DESC
;
```
![q4t](https://github.com/Cleancent26/DATA_ANALYTICS/assets/159614822/05178968-e252-4c19-b477-b13eb3b1f5bc)


```
SELECT R.INCOME_GROUP, SUM(F.FOREST_AREA_SQKM) TOTAL_FOREST_AREA_SQKM
FROM REGION R JOIN FOREST_AREA F ON R.COUNTRY_CODE = F.COUNTRY_CODE
GROUP BY R.INCOME_GROUP
HAVING INCOME_GROUP IN 
('LOWER MIDDLE INCOME','UPPER MIDDLE INCOME','HIGH INCOME','LOW INCOME')
ORDER BY SUM(F.FOREST_AREA_SQKM) DESC;
```
![q4to](https://github.com/Cleancent26/DATA_ANALYTICS/assets/159614822/471b0f69-734c-4760-8f9c-ccab651057d2)

#####  SHOW COUNTRIES FROM EACH REGION (CONTINENT) HAVING THE HIGHEST TOTAL FOREST AREAS.
```
WITH COUNTRIES_WITH_HIGHEST_FOREST_AREA AS 
(SELECT f.COUNTRY_NAME,R.REGION,SUM(F.FOREST_AREA_SQKM) TOTAL_FOREST_AREA_SQKM, 
RANK () OVER (PARTITION BY R.REGION ORDER BY SUM(F.FOREST_AREA_SQKM) DESC) RANKS
FROM REGION R RIGHT JOIN FOREST_AREA F ON R.COUNTRY_CODE = F.COUNTRY_CODE
GROUP BY F.COUNTRY_NAME,R.REGION)
SELECT DISTINCT COUNTRY_NAME,REGION,TOTAL_FOREST_AREA_SQKM 
FROM COUNTRIES_WITH_HIGHEST_FOREST_AREA 
WHERE RANKS = 1
ORDER BY TOTAL_FOREST_AREA_SQKM DESC
;
```
![q5t](https://github.com/Cleancent26/DATA_ANALYTICS/assets/159614822/eba2b6c5-fb17-4354-9f47-1121e1c1daa3)

##### Results
	1. From the analysis reached,  the total numbers of countries that involved in deforestation is one hundred and fifty four (154).
 	2. the income groups of countries having total area ranging from 75,000 to 150,000 was aslo gotten.
  	3. the average area in sqaure miles for countries in the 'upper middle income region'was also compared to other income groups categories.
   	4. the total forest area in square km for countries in the 'high income' group was also compared to the rest income groups.
    	5. countries from each region with the highest total forest area was also gotten.

##### Recoomendations
 From the analysis derived at, deforestation is mainly carried out by countries with outstanding economic performance. Deforestation has played a pivotal role
 role in nations advancement into being a developed nation. Nations in the lower income group could venture into deforestation for economic growth by reaching to 
 nations in the high income group to sell off woods to those countries at higher prices for there economic advancement as those in the high income group have lesser forest area to for deforestation which would get worse in the future as it seems they have gotten to the peak of thir economic advancement with deforestation. so in the near future to come those in the high income group would be short of forest land area and those in the low income group could take advantage of this for their economic advancement.


##### Limitations
 In the course of this analysis, among several limitations faced are;
 1. null values in the data; some countries have no values for several years but i was able to go around this by using mean(avgr), and mode to solve this limitation
 2. outlyers; in the data set, i also encountered some outlyers in the form of extreme figures with the country name give as world which i had to take out completely.
 3. there were also spelling errors which i had to deal with by providing the correct spelling for those words.

 ##### References
 1. Google; in the course of this analysis, i had to derive photo from google to aid and portray what this project is about.
 2. I also want to reference the person of victor somandina who happens to be among my tutor

#### PROBLEM STATEMENTS
```
1. WHAT ARE THE TOTAL NUMBER OF COUNTRIES INVOLVED IN DEFORETATION?
2. SHOW THE INCOME GROUPS OF COUNTRIES HAVING TOTAL AREA RANGING FROM
	75,000 TO 150,000 SQUARE METER
3.CALCULATE AVG. AREA IN SQUARE MILES FOR COUNTRIES IN THE 'UPPER MIDDLE INCOME REGION'
	COMPARE THE RESULT WITH THE REST OF THE CATEGORIES
4.DETERMINE THE TOTAL FOREST AREA IN SQUARE KM FOR COUNTRIES IN THE 'HIGH INCOME' GROUP
	COMPARE RESULT WITH THE REST OF THE INCOME CATEGORIES
5. SHOW COUNTRIES FROM EACH REGION (CONTINENT) HAVING THE HIGHEST TOTAL FOREST AREAS.
```





##### Find full project [here](https://github.com/Cleancent26/DATA_ANALYTICS/blob/main/DEFORESTATION_AMONG_COUNTRIES.sql)


