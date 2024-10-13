# Astronaut Data Analysis
## Project Overview

This data analysis study aims to provide a deep dive into NASA's astronaut yearbook to gather insights. 10 queries are used to discover insights into astronaut performances, alum success, groups and demographics, historical events and composition of NASA's astronaut corps. Some of the advanced select queries utilized are **HAVING, CASE, MIN, MAX, SUM, COUNT, and AVG**.


## Steps Taken
### a. Creating A New database

- This database was created to store, manage, and retrieve amounts of structured data for easy access and analysis.
```sql
CREATE DATABASE nasa_astronauts;
USE nasa_astronauts;
```

### b. Creating a table

- This table was created to structure data into fields and specify data type for each of them.
  ```sql
  CREATE TABLE astronauts(
   Name                varchar(255) PRIMARY KEY,
   Year                INTEGER,
   GroupNum            INTEGER,
   Status              TEXT,
   Birth_Date          TEXT,
   Birth_Place         TEXT,
   Gender              TEXT,
   Alma_Mater          TEXT,
   Undergraduate_Major TEXT,
   Graduate_Major      TEXT,
   Military_Rank       TEXT,
   Military_Branch     TEXT,
   Space_Flights       INTEGER,
   Space_Flight_hr     INTEGER,
   Space_Walks         INTEGER,
   Space_Walks_hr      REAL,
   Missions            TEXT,
   Death_Date          TEXT, 
   Death_Mission       TEXT);
  

### c. Populating the table

- This was done to insert multple records into the table before querying the data.
  
#### Single Record Population

```sql
INSERT INTO astronauts(Name,Year,GroupNum,Status,Birth_Date,Birth_Place,Gender,Alma_Mater,Undergraduate_Major,Graduate_Major,Military_Rank,Military_Branch,Space_Flights,Space_Flight_hr,Space_Walks,space_Walks_hr,Missions,Death_Date,Death_Mission)
VALUES ('Joseph M. Acaba',2004,19,'Active','5/17/1967','Inglewood, CA','Male','University of California-Santa Barbara; University of Arizona','Geology','Geology',NULL,NULL,2,3307,2,13,'STS-119 (Discovery), ISS-31/32 (Soyuz)',NULL,NULL);
```

### d. Querying Data

-Some of these complex queries are listed below:

#### Astrounat Performances

``` sql

-- Find name,status and average time spent on space walks (in hours) 
-- for top active 10 astronauts with a documented military rank
SELECT name,  
       status,     
       military_rank,    
       AVG(IFNULL(space_walks_hr, 0)) AS average_time_spent_walking_hours 
FROM astronauts 
WHERE military_rank IS NOT NULL 
AND status = "active"
GROUP BY name,    
military_rank 
ORDER BY average_time_spent_walking_hours DESC  
LIMIT 10;
```

#### Alum Success

``` sql
-- Find alma mater of NASA astronauts with the most records of service
-- Astrounaut should have more than 10 walks and flights recorded
SELECT alma_mater,
	   SUM(space_flights) AS total_no_of_flights,
	   SUM(space_walks) AS total_no_of_walks
FROM astronauts
GROUP BY alma_mater
HAVING SUM(space_flights) > 10
AND SUM(space_walks) > 10;
```

#### Groups and Demographics

``` sql
-- Find name, birtdate and status of astronauts in either group 19 or 20 
SELECT name,
	   status, 
       groupnum,
	   birth_date
FROM astronauts
HAVING groupnum = 19 
OR     groupnum = 20
ORDER BY groupnum ; 
```

#### Historical Events

``` sql
-- Find name and birth date of first astronaut born in New York
SELECT name,
       birth_place,
	   MIN(birth_date) AS first_birth
FROM astronauts
WHERE birth_place = 'New York, NY' 
GROUP BY name,
		 birth_place
HAVING MIN(birth_date) > "12/12/1951" 
LIMIT 1;
```

#### Composiition of NASA astronaut corps

```sql
-- Group astronauts by gender and military rank, counting astronauts per rank 
-- categorized into NASA's 5-star military level system.

SELECT 
	   gender,
       COUNT(Military_rank) AS rank_count,
    CASE 
        WHEN Military_rank IN ("Lieutenant General", "Vice Admiral") THEN "5 stars"
        WHEN Military_rank IN ("Brigadier General","Major General", "Rear Admiral") THEN "4 stars"
		WHEN Military_rank IN ("Lieutenant Colonel", "Colonel" ) THEN "3 stars"
        WHEN Military_rank IN ("Commander", "Major" ) THEN "2 stars"
        ELSE "Other"
    END AS "military_level"
FROM astronauts
WHERE military_rank is not null 
GROUP BY gender, military_rank
ORDER BY gender;
```

### Key Findings

1. Top Performers:
    Sunita L. Williams and Jeffrey N. Williams lead in space walks and flights, respectively.
3. Successful Alums:
    US Naval Academy, US Naval Postgraduate School, and MIT produce successful astronauts.
4. Astronaut Groups:
   All astronauts in Group 20 remain active in the program, though their performance levels may vary.
5. Historical Events:
- Apollo 1 Tragedy: Three astronauts tragically lost their lives in the Apollo 1 mission.
- First New York Astronaut: Anna L. Fisher was the first astronaut born in New York.
- Most Recent Death: William B. Lenoir was the last astronaut to pass away.
5. Peak Years:
  1990 and 1998 saw increased astronaut activity due to Hubble and Mir missions.
6. Diversity and Structure:
- While gender diversity has improved, more women need representation at the highest ranks.
- Many astronauts are beginners in military ranking, requiring career development opportunities.

### Conclusion

This study has demonstrated the power of advanced SQL queries in extracting valuable insights. Addressing these findings can help NASA continue to optimize astronaut military training, mission planning, and program structure to ensure the continued success of its space exploration endeavors.
