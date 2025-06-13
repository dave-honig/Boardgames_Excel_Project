# BoardGameGeek.com Excel Project

## Introduction

My name is David Honig and I'm a Data Analyst. I'm also a fan of boardgames with some favorites including Ascension, San Juan, and Century Golem Edition. This project demonstrates a variety of Excel skills needed as an analyst

## Background

[According to an article by Fortune Business Insights](https://www.fortunebusinessinsights.com/board-games-market-104972), "The global board games market size was valued at USD 14.37 billion in 2024. The market is projected to grow from USD 15.83 billion in 2025 to USD 32.00 billion by 2032.

North America dominated the board games market with a market share of 41.68% in 2024.
	
## Scenario

I'm a data analyst working for a fictional boardgame developer called *Checkmate LLC*. Ms. Queen, the company president, is looking to make the next best selling game. Checkmate has a team of developers but doesn't know what they should concentrate on.
	
Mr. Rook of the marketing team has previously found the highest rated games sell the most and bring in the  most revenue. 

My manager, Mr. Bishop, has assigned me the task of looking for commonalities in the top boardgames which could help point the development team in the right direction.

### Excel Skills Used

The following Excel skills were utilized for analysis:

- **📊 Pivot Tables**
- **📈 Pivot Charts**
- **🧮 DAX (Data Analysis Expressions)**
- **🔍 Power Query**
- **💪 Power Pivot**
 
## Data collection

BoardGameGeek.com is a well-known website in the board game community. It provides many details for each game, allows users to rate games, and keeps track of games in each user's collection.
	
[A dataset from February 2021](https://www.kaggle.com/datasets/melissamonfared/board-games) was located from [kaggle.com](www.kaggle.com).
- The Comma Separated Values (.csv) file was partially cleaned by excluding unranked games and including games with a minimum of 30 user ratings.
	
## Dataset Review
1. Using Power Query data was imported into an Excel Table named "All_Games". 
   1. 20,345 rows were loaded.
2. The unique ID will be a handy reference as some games have the same name.
   1. There are 11 different games called "Robin Hood."
3. The formula `=COUNTBLANK(All_Games[ID])` found 15 missing IDs.
4. These numbers could be found by quickly searching boardgamegeek.com, but let's take the approach if there were many more missing values.

### Updating missing IDs

1. [A BoardGameGeek list from February 2025](https://www.kaggle.com/datasets/bwandowando/boardgamegeek-board-games-reviews-jan-2025) was loaded with "Only create Connection" and named *Feb2025 Boardgames*.  
   1. This dataset was not originally used as it is missing many game details.
2. "Get data from .csv" imported the data and was loaded as to "Only create Connection."
3. The query was named Feb2025 Boardgames.
4. Within the query editor on the All_Games tab, "Merge Queries" combined the data.
   1. The "Name" and Year Published" were used for the matching columns.
5. In the resulted merge, Game_ID, Description, and Link were kept.
6. Both ID and Final_ID were formatted as Text for the next step.
7. A "Conditional Column" was added called Final_ID.  

	<img src="/Images/conditional_column.png" width=60% alt="Conditional Column Entry">  

8. Cleaning up, the two extra ID columns were removed.
9. Two games were left with null values. 
10. For completeness, two more clauses were added to the conditional formatting.  

	<img src="/Images/updated_conditional_column.png" width=60% alt="Updated Conditional Column Entry">

11. Final_ID was renamed to ID
12. Duplicate rows were removed using Remove Rows --> Remove Duplicates

	
### Data exploration
1. Boardgamegeek allows all users to rank games on a scale of 1-10.
2. Each boardgame has a "Rating Average" calculated by averaging all user ratings whether they have played the game or not.
   1. This is a limitation, though I don't believe  many people are rating games they have never played.
3. These are the column statistics for the Rating Average.  
	Count: 20345  
	Error: 0  
	Empty: 0  
	Distinct: 622  
	Unique:	79  
	NaN: 0  
	Zero: 0  
	Min:  1.05  
	Max:  9.58  
	Average:  6.403226663  
	Standard deviation:  0.935910525920439  
	
## When did we get so popular?

1. From the Power Query Editor, "All_Games" was closed and loaded to a new sheet named "All Games."
2. The table All_Games was added to the Data Model.
3. A count of all games was created with the formula:`Game_Count:=DISTINCTCOUNT(All_games[ID])`
4. Median of the Rating Average was calculated: `All_Games_Median:=MEDIAN(All_games[Rating Average])`
5. Looking at the pivotchart **"How Many Boardgames Have Been Created Over Time?"**, over the past 20 years the number of boardgames have greatly increased.
   1. With the advent of crowdfunding platforms like Kickstarter and Indiegogo numerous small developers have been able to release their games.
   2. The biggest success story is [Exploding Kittens](https://en.wikipedia.org/wiki/Exploding_Kittens) by The Oatmeal. *"Beginning as a [Kickstarter](https://en.wikipedia.org/wiki/Kickstarter) project seeking $10,000 USD in crowdfunding, it exceeded its goal in eight minutes. On January 27, 2015, after seven days, it passed 103,000 backers, setting the record for the most backers in Kickstarter history. At completion on February 19, 2015, it had US$8,782,571 in pledges by 219,382 backers."*  
   
	<img src="/Images/boardgames_over_time.png" width=100% alt="Boardgames over time">  

### Not every game is a winner

The histogram **"How Are All the Boardgames Rated"** uses the "Rating Average" column creating a nice bell curve with a median value is 6.43.  

	<img src="/Images/how_are_all_games_rated.png" width=100% alt="How Are All the Games Rated">	
The median value was calculated to be 6.43 with: `All_Games_Median:=MEDIAN(All_games[Rating Average])`  
	
## Do you have a game recommendation?

To determine how to choose the top games a couple options were considered. Picking a number like the top 1000 is easy but a more scientific choice was decided on. 
1. First, the 90th and 95th percentile were calculated.
   1. 90th Percentile: `90th_Percentile:=PERCENTILE.INC(All_games[Rating Average],0.90)` and
   2. 95th Percentile: `95th_Percentile:=PERCENTILE.INC(All_games[Rating Average],0.95)`
2. Two more measure were created to count the number of games in these percentiles.
   1. 90th Percentile Count 
```
 Count_of_90th_Percentile:=VAR PercentileValue = [90th_Percentile]
			RETURN
			COUNTROWS(
			 FILTER(
			 All_games,
			 All_games[Rating Average] >= PercentileValue
			 )
			)
```
   2. 95th Percentile Count
```
Count_of_95th_Percentile:=VAR PercentileValue = [95th_Percentile]
			RETURN
			COUNTROWS(
			 FILTER(
			 All_games,
			 All_games[Rating Average] >= PercentileValue
			 )
			)
```
3. The 90th percentile was chosen with 2,075 games with a rating average of 7.56 or higher compared to the 95th percentile with 1,038 games and a rating average of 7.88 or higher.
4. In the Power Query Editor the original query was referenced to a new query named "Top_Games".
   1. A filter was applied to the Rating Average for those greater than or equal to 7.56.
   2. The column statistics were checked to make sure there were 2,075 rows.
   3. The query "Top_Games" was added to the data model.   
	
	<img src="/Images/90th_percentile_column_stats.png" alt="90th Percentile Column Stats.png">  

# Let's be explicit
1. In Power Pivot, "Top_Games" was added into the data model for the creation of explicit measures to save time in the long run.
2. These explicit measures include:
   1. The count of games: `Count_TopGames:=DISTINCTCOUNT(TopGames[ID])`
   2. Percent of the top games:
```
Percent_of_games:=DIVIDE(
COUNT(TopGames[ID]),
CALCULATE(COUNT(TopGames[ID]), ALL(TopGames)))
```
3. Rating Average: `Rating_Average_TopGames:=AVERAGE(TopGames[Rating Average])`
   1. The top games have an average of 7.97/10
4. Complexity Average: `Complexity_Average_TopGames:=AVERAGE(TopGames[Complexity Average])`

## How many can play?

The sheet "# of Players" compares the Minimum and Maximum number of player to the Rating Average. A slicer was added to both tables allowing visual comparison for counts on the lower end.

1. The most common minimum number of player for the top games are 2 at 1,188 games and 1 at 741 games.  

	<img src="/Images/min_players.png" width=60% alt="Minimum Number of Players">	  
	
2. The most common maximum number of player for the top games are 4 at 666 games and 2 at 630 games.  

	<img src="/Images/max_players.png" width=60% alt="Maximum Number of Players">  
	
3. Looking at the minimum player count values, having a large minimum player count is not a good idea.

### Going on a side quest

1. The Top_Games query was referenced to create "Top_Games_Minimum_Players."
   1. All column except ID, Name, Year Published, and Min Players were removed.
   2. A filter was applied to Min Players for any values >=5
2. In the Top Games, only 13 require more than 4 players
3. Changing the filer again, only 39 games require more than 3 players.   

	<img src="/Images/side_quest_min_players.png" width=60% alt="Side Quest Into Player Minimums">  

## One more round?

The "Play Time" sheet includes a bar chart with the top 20 play times.
- 120, 60, and 90 minutes are the most popular followed by 180, 30, and 45.
- Between 1 and 2 hours is the ideal play time. It's long enough to have engaging gameplay and strategy, yet short enough to prevent the game from becoming tedious.  

	<img src="/Images/play_time.png" width=75% alt="Minimumn Play Time">  

## More complicated than Trouble

The "Minimum Age" sheet includes the bar chart "What is the Minimum Player Age of the Top Games?"
- The top games recommend players be at least 12 to 14 years old.
- At this age they would be mature enough to understand the rules and come up with a strategy.  
		
		<img src="/Images/min_age.png" width=60% alt="Minimum Recommended Player Age">

## Do you  have any Jacks?

While there are over 20,000 boardgames with a myriad of designs and themes, there are similar ways the games are played.
Boardgame mechanics are the specific rules and systems that define how a game is played, influencing player actions, outcomes, and the overall flow of the game. They dictate everything from turn order to how players achieve victory. Each game typically has multiple mechanics.  
Some common mechanics include:
- **Dice rolling:** Adding an element of chance and random outcomes. 
- **Card drafting:** Players select from a pool of cards, then pass them to another player to select.
- **Area control:** Players compete to control areas on a map or board. 
- **Set collection:** Players collect specific sets of items or cards to score points.

The "Top_Games" query includes the column "Mechanics" with each mechanic listed as comma separated value.
   1. The next goal is to split this column into columns with one mechanic per column.
   2. Then create a single column with them all listed.
1. Within the Power Query Editor the "Top_Games" query was references creating a new query named "Top_Games_Mechanics."
2. Most of the columns are not needed so columns "ID", "Rating Average" and "Mechanics" were selected and "Remove Other Columns" cleaned up the query.
3. Cleaning up the data, 27 games were found with no mechanic listed. 
   1. Blank cells were replaced with "None Listed"
4. "Split Columns by Delimiter" is very similar to "Text to Columns" but with a few more options.
   1. Splitting by ", " reduces the need to use the "Trim" function after to remove leading spaces. 
5. Selecting the 17 newly created columns, "Unpivot Columns" transforms the 17 columns into two: "Attribute" and "Value" which I renamed "Top_Game_Mechanics."
   1. Unpivoting the columns changed the row count from 2,046, all the way to 9,311.
   2. A new row was created for each mechanic listed after the first. 
   3. For example,game ID "192891" is listed in 3 rows. Each row contains an individual mechanic: "Deck Bag and Pool Building", "Hand Management", and "Variable Player Powers."   
   
   		<img src="/Images/unpivot_columns_example.png" alt="Unpivot Columns Example">   
   
6. "Attribute" will not be used so the column was removed.
7. "Save and Load to" gives multiple options including the ability to load directly to a PivotTable Report and adding the query into the data model. This removes an extra step of creating a new sheet with a table and then adding it to the data model.
8. The new sheet was renamed to "Game Mechanics".
9. Returning back to Power Pivot.
   1. The "Count_Mechanic" explicit measure was created: `Count_Top_Game_Mechanics:=COUNT(Top_Games_Mechanics[Top_Game_Mechanics])`
   2. Formatting the field with thousands commas and no decimal places saves time when using it in a pivot table later.
   3. To know how many game mechanics are available, a distinct measure was created: `Distinct_Top_Game_Mechanics:=DISTINCTCOUNT(Top_Games_Mechanics[Top_Game_Mechanics])`
   4. A percentage measure was added to know how often each mechanic is used compared to the usage of all mechanics:
```
Percent_of_Mechanic_to_all_mechanics:=DIVIDE(
	 COUNT([Top_Game_Mechanics]),
	 CALCULATE(COUNT(Top_Games_Mechanics[Top_Game_Mechanics]), ALL(Top_Games_Mechanics))
	 )
```	
10. The "Game Mechanics" sheet was created with a Pivot Table from the Data Model.

Board game players seem to like the excitement and uncertainty of rolling their math rocks (dice) with 1,029 of the top games using the "Dice Rolling" mechanic.

This is followed by Variable Player Powers, Simulation, Hand Management, and a Hexagon Grid used in ~500 of the top games.  

		<img src="/Images/top_game_mechanics.png" width=60% alt="Top Boardgame Game Mechanics">   

## Wait, what am I supposed to do next?

For each game, BoardGameGeek assigns a complexity rating between 1 and 5 defined as a "Community rating for how difficult a game is to understand. Lower rating (lighter weight) means easier."

For example, the kids game [Trouble](https://boardgamegeek.com/boardgame/1410/trouble) is rated at 1.07. While [Europa Universalis](https://boardgamegeek.com/boardgame/4102/europa-universalis), a complex wargame reliving 300 years of world history with a playing time of <ins>60 hours</ins> has a rating of 4.82.

1. All complexity values are rounded to the hundredths place. Grouping these values into buckets will provide a better analysis.
   1. This is the first field I encountered an unexpected problem.
2. It is not possible to group the values in a pivot table created from the data model, so a pivot table was created referencing the table "TopGames". 
3. "Complexity Average" was added for the rows with an implicit measure for count.
4. Grouping was added to "Complexity Average" starting at 0 and ending at 5, By 0.25 increments.
5. A bar chart was created with the data but Excel processes the bucket numbers as text. This results in 1-1.25, 2-2.25… listed out of order.   
		
		   	<img src="/Images/complexity_bucket_problem.png" alt="Complexity_Bucket_Problem">  
6. While a manual reorder is possible, it would not help if a different sutuation with hundreds of buckets out of order.
7. This pivot table and chart were deleted for a better way.

### The better way

1. As an alternative method, in the query editor a new column "Complexity Rounded" was created: `Number.RoundDown([Difficulty] / 0.25) * 0.25`
   1. This formula rounds down all values to the nearest quarter of a point.
2. A new column named "Complexity_Buckets" created clear value buckets: `Text.From([Complexity_Rounded]) & " - " & Text.From([Complexity_Rounded]+ 0.25)`
3. The query editor was closed and loaded.
4. A pivot table was created with the new "Complexity_Buckets" field and an implicit measure for a count.

The bar chart *"How Difficult Are the Top Games to Understand?"* with a slicer shows the top games mainly lie between 2 and 3.25.   

		<img src="/Images/top_game_complexity.png" width=60% alt="Complexity Graph of Top Games">   
		
# What should the Checkmate LLC developers focus on?

Reviewing each metric they should create a game with:
1. A minimum of 2 players to play
2. Will support 4 or more players
3. Take 1 - 2 hours to complete
4. Able to be played by those as young as 12
5. Involve dice rolling, as well as either Variable Player Powers, Simulation, Hand Management, or a Hexagonal Grid.
6. Have a complexity between 2 - 4 out of 5.

## Current offerings
There are already 9 games as of February 2021 which meet this criteria
- Bora Bora
- Carnevale: Vicious Fighting Along the Canals of Venice
- Company of Heroes
- Raiders of the North Sea
- The Castles of Burgundy
- Warhammer 40,000: Kill Team
- Warhammer Age of Sigmar: Warcry Starter Set
- Warmachine Prime Mk II
- Yohei

**Thank you**