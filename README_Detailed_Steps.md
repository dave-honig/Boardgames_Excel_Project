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
  - 20,345 rows were loaded.
2. The unique ID will be a handy reference as some games have the same name.
  - There are 11 different games called "Robin Hood."
3. The formula `=COUNTBLANK(All_Games[ID])` found 15 missing IDs.
4. These numbers could be found by quickly searching boardgamegeek.com, but let's take the approach if there were many more missing values.

### Updating missing IDs

1. [A BoardGameGeek list from February 2025](https://www.kaggle.com/datasets/bwandowando/boardgamegeek-board-games-reviews-jan-2025) was loaded with "Only create Connection" and named *Feb2025 Boardgames*.  
  i. This dataset was not originally used as it is missing many game details.
2. "Get data from .csv" imported the data and was loaded as to "Only create Connection."
3. The query was named Feb2025 Boardgames
4. In the query editor, for All_Games "Merge Queries" combined the data.
  i. The "Name" and Year Published" were used for the matching columns
	7. In the resulted merge, Game_ID, Description, and Link were kept.
	8. Both ID and Final_ID were formatted as Text for the next step
	9. A "Conditional Column" was added called Final_ID
		a. If ID = null then Game_ID
		 Else ID

	<img src="/Images/conditional_column.png" width=60% alt="Conditional Column Entry">
	
-------------------

	5. 

	11. Cleaning up, the two extra ID columns were removed.
	12. Two games were left with null values. 
	13. For completeness, two more clauses were added to the conditional formatting.

	15. Final_ID was renamed to ID
	16. Duplicate rows were removed  using Remove Rows --> Remove Duplicates

	
Data exploration
	1. Boardgamegeek allows all users to rank games on a scale of 1-10.
	2. Each boardgame has a "Rating Average" calculated by averaging all user ratings whether they have played the game or not.
		a. This is a limitation, though I don't believe  many people are rating games they have never played.
	3. These are the column statistics for the Rating Average
	4. Count	20345
	Error	0
	Empty	0
	Distinct	622
	Unique	79
	NaN	0
	Zero	0
	Min	1.05
	Max	9.58
	Average	6.403226663
	Standard deviation	0.935910525920439

	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
## When did we get so popular?

1. The table All_Games was added to the Data Model and a count of all games was calculated: `Game_Count:=DISTINCTCOUNT(All_games[ID])`
2. Median of the Rating Average was calculated: `All_Games_Median:=MEDIAN(All_games[Rating Average])`
3. Looking at the pivotchart **"How Many Boardgames Have Been Created Over Time?"**, over the past 20 years the number of boardgames have greatly increased. With the advent of crowdfunding platforms like Kickstarter and Indiegogo numerous small developers have been able to release their games.  

	<img src="/Images/boardgames_over_time.png" width=100% alt="Boardgames over time">  
	
The histogram **"How Are All the Boardgames Rated"** uses the "Rating Average" column creating a nice bell curve with a median value is 6.43.  

	<img src="/Images/how_are_all_games_rated.png" width=100% alt="How Are All the Games Rated">	

## Do you have a game recommendation?

- The 90th and 95th percentile were calculated: `90th_Percentile:=PERCENTILE.INC(All_games[Rating Average],0.90)` (changing the final element for 95th percentile)
- Two more measure were created to count the number of games in these percentiles.
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
- The 90th percentile was chosen with 2,075 games with a rating average of 7.56 or higher compared to the 95th percentile with 1,038 games and a rating average of 7.88 or higher.
			
- A new query named "Top_Games" was referenced and filter applied to the Rating Average for those greater than or equal to 7.56.
- Column statistics were checked to make sure there were 2,075 rows and the query was added to the data model

# Let's be explicit

Explicit measures were added including:
1. The count of games: `Count_TopGames:=DISTINCTCOUNT(TopGames[ID])`
2. Percent of the top games:
```
Percent_of_games:=DIVIDE(
COUNT(TopGames[ID]),
CALCULATE(COUNT(TopGames[ID]), ALL(TopGames)))
```
3. Rating Average: `Rating_Average_TopGames:=AVERAGE(TopGames[Rating Average])`
4. Complexity Average: `Complexity_Average_TopGames:=AVERAGE(TopGames[Complexity Average])`

## How many can play?

The sheet "# of Players" compares the Minimum and Maximum number of player to the Rating Average.
1. The most common minimum number of player for the top games are 2 at 1,188 games and 1 at 741 games.  

	<img src="/Images/min_players.png" width=60% alt="Minimum Number of Players">	  
	
3. The most common maximum number of player for the top games are 4 at 666 games and 2 at 630 games.  

	<img src="/Images/max_players.png" width=60% alt="Maximum Number of Players">  

### Going on a side quest

Top_Games was referenced to create "Top_Games_Minimum_Players" with a filter applied to Min Players for any values >=5.
- In the Top Games, only 13 require more than 4 players
- Changing the filer again, only 39 games require more than 3 players.

## One more round?

The sheets "Play Time" includes a bar chart with the top 20 play times.
- 120, 60, and 90 minutes are the most popular followed by 180, 30, and 45.
- Between 1 and 2 hours is the ideal play time. It's long enough to have engaging gameplay and strategy, yet short enough to prevent the game from becoming tedious.  

		<img src="/Images/play_time.png" width=75% alt="Minimumn Play Time">  
		
## More complicated than Trouble

The sheet "Minimum Age" includes the bar chart "What is the Minimum Player Age of the Top Games?"
		a. The top games recommend players be at least 12 to 14 years old.
		b. At this age they would be mature enough to understand the rules and come up with a strategy.  
		
		<img src="/Images/min_age.png" width=60% alt="Minimum Recommended Player Age">

## Do you  have any Jacks?

While there are over 20,000 boardgames with a myriad of designs and themes, there are similar ways the games are played.
Boardgame mechanics are the specific rules and systems that define how a game is played, influencing player actions, outcomes, and the overall flow of the game. They dictate everything from turn order to how players achieve victory. Each game typically has multiple mechanics.
Some common mechanics include:
- Dice rolling: Adding an element of chance and random outcomes. 
- Card drafting: Players select from a pool of cards, then pass them to another player to select.
- Area control: Players compete to control areas on a map or board. 
- Set collection: Players collect specific sets of items or cards to score points
	
The "Top_Games" query was referenced creating a new query named "Top_Games_Mechanics".
1. Cleaning up the data, 27 games were found with no mechanic listed.
2. Blank cells were replaced with "None Listed."
3. Each mechanic was split into 17 new columns which were then unpivoted.
4. Data was saved to a PivotTable Report and the query was added to the data model.
5. The new sheet was renamed to "Game Mechanics".
6. The "Count_Mechanic" explicit measure was created: `Count_Top_Game_Mechanics:=COUNT(Top_Games_Mechanics[Top_Game_Mechanics])`
7. To know how many game mechanics are available, a distinct measure was created: `Distinct_Top_Game_Mechanics:=DISTINCTCOUNT(Top_Games_Mechanics[Top_Game_Mechanics])`
8. A percentage measure was added to know how often each mechanic is used compared to the usage of all mechanics:  with the formula:
```
Percent_of_Mechanic_to_all_mechanics:=DIVIDE(
	 COUNT([Top_Game_Mechanics]),
	 CALCULATE(COUNT(Top_Games_Mechanics[Top_Game_Mechanics]), ALL(Top_Games_Mechanics))
	 )
```	
9. The sheet "Game Mechanics" was created with a Pivot Table from the Data Model.
- Board game players seem to like the excitement and uncertainty of rolling their math rocks (dice) with 1,029 of the top games using the "Dice Rolling" mechanic.
- This is followed by Variable Player Powers, Simulation, Hand Management, and a Hexagon Grid used in ~500 of the top games.  

		<img src="/Images/top_game_mechanics.png" width=60% alt="Top Boardgame Game Mechanics">

## Wait, what am I supposed to do next?

For each game, Boardgamegeek assigns a complexity rating between 1 and 5 defined as a "Community rating for how difficult a game is to understand. Lower rating (lighter weight) means easier."

1. A new column "Complexity Rounded" was created: `Number.RoundDown([Difficulty] / 0.25) * 0.25`
2. "Complexity_Buckets" creates clear value buckets: `Text.From([Complexity_Rounded]) & " - " & Text.From([Complexity_Rounded]+ 0.25)`
3. The bar chart "How Difficult Are the Top Games to Understand?" with a slicer shows the top games mainly lie between 2 and 3.25.  

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
