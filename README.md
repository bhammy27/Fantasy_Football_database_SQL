# Fantasy_Football_database_SQL
A desire to win my Fantasy Football leagues led to the realization that I have a passion for Data Analytics.  What once began as manually entering 100s of rows of data into Excel to prepare for the draft, has evolved into the creation of this Fantasy Football data base.  I created the database using pgAdmin and postgreSQL.  Upon completion, I will query the data to create the [2024 Draft Report](https://github.com/bhammy27/2024_Draft_Report_SQL).  I will use the report to draft my 2024 fantasy team.
#### How does someone win Fantasy Football?
  -  The first step is to draft a team of players from 6 different positions 
  -    A team has 1 Quarterback, 2 Running Backs, 2 Wide Receivers, 1 Tight End, 1 Flex position (which can be another Running Back, Wide Receiver, or Tight End), and finally 1 Kicker and Team Defense
  -  Each week of the NFL season, your team faces a team from another person in your league of 10 players.
  -  The team that accumulates the most points which each week gets a win
      - Points are accumulated based off the players stats for that week.  Each stat category has a different point value.
  -  After 14 weeks, the top 6 teams compete in the playoffs for the Championship
## My Intentions:
  -  Win my Fantasy Football leagues by querying and analyzing the database to draft the best team possible
  -  Create and analyze a database from csv files using ETL and data modeling skills
      - Write queries using both DDL and DML 
  -  Create a reference repository with frequently used queries and functions

## The Data:
 -  Data was extracted on a weekly basis from [FantasyPros](https://www.fantasypros.com/nfl/stats/qb.php) using Power Query
     - The original use was for Excel analysis found in my [Excel_Analysis](https://github.com/bhammy27/Excel_Analysis) repository
 - The raw unaltered files were loaded into pgAdmin as a csv to begin creating the Fantasy Football database

# Creating the Fantasy Football data base:
- First, I created and defined a new data base in pgAdmin and created 6 tables, one for each fantasy football position
  
![Screenshot (64)](https://github.com/bhammy27/Fantasy_Football_database_SQL/assets/154477061/e0e2427a-cb35-4388-85d8-3af55c8843cc)


### Cleaning and transforming the raw tables
-  All tables were checked to ensure there were 18 distinct weeks, 32 distinct team_codes, and 6 distinct seasons
-  A count of all distinct players was also created and matched with values in the csv
-  A new field called position was added which will help when creating a player_index table that will be used with data visualization tools later

![Screenshot (81)](https://github.com/bhammy27/Fantasy_Football_database_SQL/assets/154477061/824881f3-b84d-4548-98e0-a3a4235011b2)


### Creating and adding a new field with aggregated values from other fields
-  The kicker table had 3 fields that could be combined into 1 field since the fantasy point total for each field is the same
-  I created a field called "under_forty" which combines the data from field goals made under 20 yards, in the 20 yard range, and in the 30 yard range.
-  The queries used to create the "under_forty" field, import the aggregated data into it, and delete the unnecessary fields can be seen here:

![Screenshot (61)](https://github.com/bhammy27/Fantasy_Football_database_SQL/assets/154477061/7f305f05-848d-4598-91df-478dcd20ef1f)


### Filtering out and removing unnecessary records
-  The original data source included every NFL player rostered in 2023
-  Some players listed did not play in 2023 
-  Because of this, I had to filter each position table to remove all players who did not record 1 catch, pass, run attempt, or kick attempt
-  The following query depicts the process of removing these players from the kicker table
    - This process was replicated in all position tables

![Screenshot (62)](https://github.com/bhammy27/Fantasy_Football_database_SQL/assets/154477061/6f3871ae-0511-49ae-a6fe-4a133ae7625b)
![Screenshot (63)](https://github.com/bhammy27/Fantasy_Football_database_SQL/assets/154477061/bf490f75-d1df-4e16-a36e-ca0709465d86)


 
### Checking for and correcting duplicate rows
- The exact combination of the week, player, and season fields makes each record unique
- Creating a pivot table by adding FILTER in the SELECT statement will help identify outliers in the number of games played
- The query below revels wide receivers with 18 games played in 2021
    - The max games per season is 17
 
![Screenshot (76)](https://github.com/bhammy27/Fantasy_Football_database_SQL/assets/154477061/61786a76-3cb2-44da-b8f8-bb2677f13b8d)


- In the running_back table Raheem Mostert also had 18 games played
- The query below was used to check out the problem and correct it
  

![Screenshot (78)](ht![Screenshot (77)](https://github.com/bhammy27/Fantasy_Football_database_SQL/assets/154477061/4f2f5a6a-2516-42cb-ac9a-7cd950862946)
![Screenshot (78)](https://github.com/bhammy27/Fantasy_Football_database_SQL/assets/154477061/386d7076-d4fe-47e3-a2dc-941a972981a4)

## Quality Assurance Procedures:
-  Quality control was a major part of my responsibilities as an account manager in the landscape horticulture industry
      -  Understanding the importance of quality control led me to apply the following steps to provide quality assurance for this database
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
-  
# Things I learned creating this database
- Creating databases is FUN!!
    - This process provides excellent knowledge of the data making analyzing it easier
    - The detailed methodical process required is the same process as my thought and reasoning process
- **Data cleaning never ends!**
    - When creating the Draft Report, I discovered unremoved whitespace on players names despite using TRIM
    - I then had to identify what the white space was and reclean the players field in all tables
- As a landscape designer, I learned early on that when drawing landscape designs to **document each step, save each iteration**, and keep a notepad nearby
    - When creating this database, I save the code here in GitHub after each step of the cleaning process as reference points to fall back to
        -  When creating the Draft Report, I used UPDATE SET instead of INSERT INTO and lost all data in the table
        -  I used TRUNCATE to clear all records and then copied queries from GitHub and reran them to replace the table information
    - My mind was producing new ideas with each step.  Writing the ideas down helped to remember them and stay on track
    - I was reminded the importance of **finishing each step before moving on** to the next one
- When creating field names in pgAdmin, use lowercase letters since using proper case requires the use of " " around each filed name
- **Finally, know when to take breaks or stop!**
    - Because I enjoy this, I often entered a Flow State and did not want to stop
    - Having a couple minutes away from the screen helps maintain focus

