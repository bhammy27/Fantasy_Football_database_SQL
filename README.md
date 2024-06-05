# Fantasy_Football_database_SQL
A desire to win my Fantasy Football leagues helped me realize that I have a passion for Data Analytics. What once began as manually entering 100s of rows of data into Excel to prepare for the draft, has evolved into the creation of this Fantasy Football data base. I created the database using pgAdmin and postgreSQL. Upon completion, I will query the data to create the [2024 Draft Report](https://github.com/bhammy27/2024_Draft_Report_SQL). I will also create visualizations to help draft my team next season, and to help manage the team on a weekly basis during the season.  
#### How does someone win Fantasy Football?
  -  The first step is to draft a team of players from 6 different positions 
  -    A team has 1 Quarterback, 2 Running Backs, 2 Wide Receivers, 1 Tight End, 1 Flex position (which can be another Running Back, Wide Receiver, or Tight End), and finally 1 Kicker and Team Defense
  -  Each week of the NFL season, your team faces another team in your league of 10 players.
  -  The team that accumulates the most points which each week gets a win
      - Points are accumulated based off the players stats for that week. Each stat category has a different point value.
  -  After 14 weeks, the top 6 teams compete in the playoffs for the Championship
## My Intentions:
  -  Win my Fantasy Football leagues by querying and analyzing the database to draft the best team possible
  -  Create and query a database from csv files using the ETL process
      - Write queries using both DDL and DML 
  -  Create a reference repository with frequently used queries and functions

## The Data:
 -  Data was extracted on a weekly basis from [FantasyPros](https://www.fantasypros.com/nfl/stats/qb.php) using Power Query
     - The original data use was for an Excel analysis found in my [Excel_Analysis](https://github.com/bhammy27/Excel_Analysis) repository
 - The raw unaltered files were loaded into pgAdmin as a csv to begin creating the Fantasy Football database

# Creating the Fantasy Football data base:
- First, I created and defined a new data base in pgAdmin and created 6 tables, one for each fantasy football position
  
![Screenshot (64)](https://github.com/bhammy27/Fantasy_Football_database_SQL/assets/154477061/e0e2427a-cb35-4388-85d8-3af55c8843cc)


### Cleaning and transforming the raw tables
-  All tables were checked to ensure there were 18 distinct weeks, 32 distinct team_codes, and 6 distinct seasons
-  A count of all distinct players was also created and matched with values in the csv
-  A new field called position was added which will help when creating a player_index table that will be used with data visualization tools later

### Creating and adding a new field with aggregated values from other fields
-  The kicker table had 3 fields that could be combined into 1 field because each field has the same fantasy point value
-  I created a field called "under_forty" which combines the data from field goals made under 20 yards, in the 20 yard range, and in the 30 yard range.
-  The queries used to create the "under_forty" field, import the aggregated data into it, and delete the unnecessary fields can be seen here:

![Screenshot (61)](https://github.com/bhammy27/Fantasy_Football_database_SQL/assets/154477061/7f305f05-848d-4598-91df-478dcd20ef1f)


### Filtering out and removing unnecessary records
-  The original data source included every NFL player rostered since 2018
-  Many players listed did not play in 2023 
-  I filtered each position table to remove all players who did not record stats in 2023
-  The following query depicts the process of removing these players from the kicker table
    - This process was replicated in all position tables

![Screenshot (62)](https://github.com/bhammy27/Fantasy_Football_database_SQL/assets/154477061/6f3871ae-0511-49ae-a6fe-4a133ae7625b)
![Screenshot (63)](https://github.com/bhammy27/Fantasy_Football_database_SQL/assets/154477061/bf490f75-d1df-4e16-a36e-ca0709465d86)


 
### Checking for and correcting duplicate rows
- The exact combination of the week, player, and season fields makes each record unique
- Creating a pivot table by adding FILTER in the SELECT statement helps identify errors
- The query below revels wide receivers with 18 games played in 2021
    - The max games per season is 17
 
![Screenshot (76)](https://github.com/bhammy27/Fantasy_Football_database_SQL/assets/154477061/61786a76-3cb2-44da-b8f8-bb2677f13b8d)


- In the running_back table Raheem Mostert also had 18 games played
- The query below was used to check out the problem and correct it
  

![Screenshot (78)](ht![Screenshot (77)](https://github.com/bhammy27/Fantasy_Football_database_SQL/assets/154477061/4f2f5a6a-2516-42cb-ac9a-7cd950862946)
![Screenshot (78)](https://github.com/bhammy27/Fantasy_Football_database_SQL/assets/154477061/386d7076-d4fe-47e3-a2dc-941a972981a4)
### Removing duplicate player names
- After running TRIM() function, I still noticed duplicate player names
- After being unable to identify the whitespace causing the duplication, I removed the files using the following query: 

![Screenshot (119)](https://github.com/bhammy27/Fantasy_Football_database_SQL/assets/154477061/2ec9d9af-f9c0-4c7d-a7f5-1cd1d415c490)

  
## Quality Assurance Procedures:
-  Quality control was a major part of my responsibilities as an account manager in the landscape horticulture industry
      -  Knowing the importance of quality control led me to apply the following steps to create quality assurance
-  I vetted [FantasyPros](https://www.fantasypros.com/nfl/stats/qb.php) by ensuring FPTS values match FPTS values in my Yahoo league
-  When creating a new table from the raw csv, I queried each table to ensure the number of weeks = 18, seasons = 6, games = 2 and players = players from csv
-  I noted the number of distinct players that needed removed and the total number of distinct players after removing the unnecessary players 
-  Each player's team_code values had to be updated for the years 2022-2018
-  While updating each player's team_code by cross checking the value with the NFL website, I also checked the data for:
  -  Duplicate weekly values and duplicate player listings that were not identified in the pivot table
      -  I found Andrew Beck and Reggie Gillman listed under two different positions
      -  I assigned Beck to the Tight End position and Gillman to the Running Back Position
      -  I also found a couple players with duplicate weekly values and deleted them
   -  Cross referenced games played per season while cross referencing player team_code
      -  This ensured that each week of data was accounted for and there were no missing records
-  Rechecked players 2023 stats to ensure all players had at least 1 target, carry, pass attempt, or field goal
  
# Things I learned creating this database
- Creating databases is FUN
    - This process provides excellent knowledge of the data making analyzing it easier
    - The detailed methodical process required is very similar to the process of landscape design
- **Data cleaning never ends!**
    - When creating the Draft Report, I discovered unremoved whitespace on players names despite using TRIM
    - After not being able to identify the whitespace, I had to think of another way to replace the values
- As a landscape designer, I learned when drawing landscape designs to **document each step, save each iteration**, and keep a notepad nearby
    - I saved the code here in GitHub after each step creating a reference point 
        -  When creating the Draft Report, I used UPDATE SET instead of INSERT INTO and lost all data in the table
        -  I used TRUNCATE to clear all records then recreated the table from saved queries 
    - My mind was producing new ideas with each step. Writing the ideas down helped to remember them and stay on track
- When creating field names in pgAdmin, use lowercase letters. Using proper case requires the use of " " around field names


