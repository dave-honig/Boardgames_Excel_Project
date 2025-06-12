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
	
[A dataset from February 2021 was located](https://www.kaggle.com/datasets/melissamonfared/board-games) from [kaggle.com](www.kaggle.com).
- The Comma Separated Values (.csv) file was partially cleaned by excluding unranked games and including games with a minimum of 30 user ratings.
	
## Dataset Review
1. Using Power Query, 20,345 rows were imported into an Excel Table named "All_Games".
2. The unique ID will be used as a primary key as there are 11 different games called "Robin Hood."
3. The formula `=COUNTBLANK(All_Games[ID])` found 15 missing IDs.

### Updating missing IDs
1. Missing IDs could be found by quickly searching boardgamegeek.com, but an approach was taken if there were many more missing values.
2. [A BoardGameGeek list from February 2025](https://www.kaggle.com/datasets/bwandowando/boardgamegeek-board-games-reviews-jan-2025) was loaded with "Only create Connection" and named *Feb2025 Boardgames*. This dataset was not originally used as it is missing many game details.
3. The two queries were merged, a "Conditional Column" was added, and after removing duplicates the column ID was finalized.
   ![conditional_column](/Images/conditional_column.png)
	
# When did we get so popular?
1. The table All_Games was added to the Data Model and a count of all games was calculated with `Game_Count:=DISTINCTCOUNT(All_games[ID])`
2. Median of the Rating Average was calculated with `All_Games_Median:=MEDIAN(All_games[Rating Average])`
3. Looking at the pivotchart **How Many Boardgames Have Been Created Over Time?** we can see over the past 20 years the number of boardgames have been greatly increased. With the advent of crowdfunding platforms like Kickstarter and Indiegogo numerous small developers have been able to release their games.
![Boardgames over time](Images/boardgames_over_time)
The histogram **"How Are All the Boardgames Rated"** uses the "Rating Average" column creating a nice bell curve with a median value is 6.43.
![how_are_all_games_rated](/Images/how_are_all_games_rated/png)
	

# Do you have a game recommendation?
To determine how to choose the top games a couple options were considered. Picking a number like the top 1000 is easy but a more scientific choice was decided on. 
	2. First, the 90th and 95th percentile were calculated.
		a. Within the data model the measure "90th_Percentile" and 95th_Percentile" were created using the formulas:
			i. 90th_Percentile:=PERCENTILE.INC(All_games[Rating Average],0.90) and
			ii. 95th_Percentile:=PERCENTILE.INC(All_games[Rating Average],0.95)
		b. Two more measure were created to count the number of games in these percentiles.
			i. Count_of_90th_Percentile:=VAR PercentileValue = [90th_Percentile]
			RETURN
			COUNTROWS(
			 FILTER(
			 All_games,
			 All_games[Rating Average] >= PercentileValue
			 )
			)
			ii. Count_of_95th_Percentile:=VAR PercentileValue = [95th_Percentile]
			RETURN
			COUNTROWS(
			 FILTER(
			 All_games,
			 All_games[Rating Average] >= PercentileValue
			 )
			)
			iii. These two measures resulted in 2,075 games in the 90th percentile with a value of 7.56 or higher and 1,038 games in the 95th percentile with a value of 7.88 or higher.
			iv. Analyzing games in the 90th percentile was chosen.
	3. In the Power Query Editor the original query was referenced to a new query named "Top_Games"
		a. A filter was applied to the Rating Average for those greater than or equal to 7.56
		b. The column statistics were checked to make sure there were 2,075 rows.
		c. The query "Top_Games" was added to the data model


Let's me be explicit
	1. Going into the data model, adding the "Top_Games" into the data model allows me to create explicit measures saving time in the long run
	2. These measures include the count of games:
		a. Count_TopGames:=DISTINCTCOUNT(TopGames[ID])
	3. Percent of the top games
		Percent_of_games:=DIVIDE(
		 COUNT(TopGames[ID]),
		 CALCULATE(COUNT(TopGames[ID]), ALL(TopGames)))
	4. Rating Average
		a. Rating_Average_TopGames:=AVERAGE(TopGames[Rating Average])
		b. The top games have an average of 7.97/10
	5. Complexity Average
		a. Complexity_Average_TopGames:=AVERAGE(TopGames[Complexity Average])

How many can play?
	1. The sheet "# of Players" compares the Minimum and Maximum number of player to the Rating Average.
	2. A slicer was added to both tables allowing visual comparison for counts on the lower end.
		a. The most common minimum number of player for the top games are 2 at 1,188 games and 1 at 741 games
		b. The most common maximum number of player for the top games are 4 at 666 games and 2 at 630 games.
		c. Looking at the minimum player count values, having a large minimum player count is not a good idea.
		
Doing a quick side quest
		a. The Top_Games query was referenced to create a new query titled "Top_Games_Minimum_Players" 
			i. All column except ID, Name, Year Published, and Min Players were removed.
			ii. A filter was  applied to Min Players for any values >=5
			iii. In the Top Games, only 13 require more than 4 players
			iv. Changing the filer again, only 39 games require more than 3 players.
	1. 

		
	1. The sheets "Play Time" includes a bar chart with the top 20 play times.
		a. 120, 60, and 90 minutes are the most popular followed by 180, 30, and 45.
		b. Between 1 and 2 hours looks to be the ideal play time. It's long enough to have engaging gameplay and strategy, yet short enough to prevent the game from becoming tedious. 

	1. The sheet "Minimum Age" includes the bar chart "What is the Minimum Player Age of the Top Games?"
		a. The top games recommend players be at least 12 to 14 years old.
		b. At this age they would be mature enough to understand the rules and come up with a strategy. 

Do you  have any Jacks?
	While al of February 2021 there are over 20,000 boardgames with a myriad of designs and themes, there are similar ways the games are played.
	
	Boardgame mechanics are the specific rules and systems that define how a game is played, influencing player actions, outcomes, and the overall flow of the game. They dictate everything from turn order to how players achieve victory. Each game typically has multiple mechanics.
Some common mechanics include: 
	• Dice rolling: Adding an element of chance and random outcomes. 
	• Card drafting: Players select from a pool of cards, then pass them to another player to select.
	• Area control: Players compete to control areas on a map or board. 
	• Set collection: Players collect specific sets of items or cards to score points
	
The "Top_Games" query includes the column "Mechanics" with each mechanic listed as comma separated values. The next goal is to split this column into individual column with one mechanic per column, then create a single column with them all listed.

	1. Within the Power Query Editor the "Top_Games" query was references creating a new query named "Top_Games_Mechanics".
	2. Most of the columns are not needed so columns "ID", "Rating Average" and "Mechanics" were selected and "Remove Other Columns" cleaned up the query.
	3. Cleaning up the data, 27 games were found with no mechanic listed. 
		a. Blank cells were replaced with "None Listed"
	4. "Split Columns by Delimiter" is very similar to "Text to Columns" but with a few more options. Splitting by ", " reduces the need to use the "Trim" function after to remove leading spaces. 
	5. Selecting the 17 new columns which were just created, "Unpivot Columns" transforms the 17 columns into two: "Attribute" and "Value" which I renamed "Top_Game_Mechanics".
		a. Unpivoting the columns changed the row count from 2,046, all the way to 9,311.
		b. A new row was created for each mechanic listed after the first. 
		c. For example, the first ID "303554" is listed in 3 rows. Each row contains a individual mechanic: "Hexagonal Grid", "Modular Board", and "Variable Set-up".
	6. "Attribute" will not be used so the column was removed.
	7. "Save and Load to" gives multiple options including loading directly to a PivotTable Report and adding the query into the data model. This removes an extra step of creating a new sheet with a table and then adding it to the data model.
		a. The new sheet was renamed to "Game Mechanics".
	
	8. Returning back to Power Pivot, The "Count_Mechanic" explicit measure was created with the formula: 
		a. Count_Top_Game_Mechanics:=COUNT(Top_Games_Mechanics[Top_Game_Mechanics])
		b. Formatting the field with thousands commas and no decimal places saves time in the future.
	9. To know how many game mechanics are available, a distinct measure was created and named "Distinct_Top_Game_Mechanics" with the formula:
		a. Distinct_Top_Game_Mechanics:=DISTINCTCOUNT(Top_Games_Mechanics[Top_Game_Mechanics])
		b. There are 169 distinct game mechanics.
	10. A percentage measure was added to know how often each mechanic is used compared to the usage of all mechanics with the formula:
		a. Percent_of_Mechanic_to_all_mechanics:=DIVIDE(
	 COUNT([Top_Game_Mechanics]),
	 CALCULATE(COUNT(Top_Games_Mechanics[Top_Game_Mechanics]), ALL(Top_Games_Mechanics))
	 )
	
	11. The sheet "Game Mechanics" was created with a Pivot Table from the Data Model.
		a. Top_Game_Mechanics, Count_Top_Game_Mechanics, and Percent_of_Mechanic_to_all_mechanics were added
		b. Count_Top_Game_Mechanics was sorted by ascending  values
		c. A value filter was added to show only the top 10 game mechanics 
		d. Board game players seem to like the excitement and uncertainty of rolling their math rocks (dice) with 1,029 of the top games using the "Dice Rolling" mechanic.
		e. This is followed by Variable Player Powers, Simulation, Hand Management, and a Hexagon Grid used in ~500 of the top games.

Wait, what am I supposed to do next?
	For each game, Boardgamegeek assigns a complexity rating between 1 and 5 defined as a "Community rating for how difficult a game is to understand. Lower rating (lighter weight) means easier."
	
	For example, the kids game Trouble is rated at 1.07. While Europa Universalis, a complex wargame reliving 300 years of world history with a playing time of 60 hours has a rating of 4.82.
	
	1. All complexity values are rounded to the hundredths place. Grouping these values into buckets will provide a better analysis.
	2. This is the first field I encountered an unexpected problem.
		a. It is not possible to group the values in a pivot table created from the data model, so a pivot table was created referencing the table "TopGames" with 
		b. "Complexity Average" and adding an implicit measure for count.
		c. Grouping was added to "Complexity Average" Starting at 0, Ending at 5, By 0.25.
		d. A bar chart was created with the data.
		e. 
		f. When the buckets were created Excel processes the numbers as text. This results in 1-1.25, 2-2.25…listed out of order.
		g. While a manual reorder is possible, it would not help if there were hundreds of buckets out of order.
		h. This pivot table and chart were deleted for a better way.
	3. As an alternative method, in the query editor a new column "Complexity Rounded" was created with the formula:
		a. Number.RoundDown([Difficulty] / 0.25) * 0.25
		b. This formula rounds down all values to the nearest quarter of a point.
	4. Next, a new column named "Complexity_Buckets" created clear value buckets with the formula:
		a. Text.From([Complexity_Rounded]) & " - " & Text.From([Complexity_Rounded]+ 0.25)
		b. The query editor was closed and loaded.
	5. A pivot table was created with the new "Complexity_Buckets" field and an implicit measure for count.
	6. The bar chart "How Difficult Are the Top Games to Understand?" with a slicer shows the top games mainly lie between 2 and 3.25
	
		
So what should the Checkmate LLC developers focus on?
	Reviewing each metric I can confidently say they should create a game with:
	1. A minimum of 2 players to play
	2. Will support 4 or more players
	3. Take 1 - 2 hours to complete
	4. Able to be played by those as young as 12
	5. Involve dice rolling, as well as either Variable Player Powers, Simulation, Hand Management, or a Hexagonal Grid.
	6. Have a complexity between 2 - 4 out of 5.
	
	Interestingly enough, there are already 9 games as of February 2021 which meet this criteria
		Bora Bora
		Carnevale: Vicious Fighting Along the Canals of Venice
		Company of Heroes
		Raiders of the North Sea
		The Castles of Burgundy
		Warhammer 40,000: Kill Team
		Warhammer Age of Sigmar: Warcry Starter Set
		Warmachine Prime Mk II
		Yohei
		
		
		




To DO:

Min and max players average score, then a slicer, then a bar chart, then a box and whisker




Look at the reviews
	• Do they tend to be on the high range?
	• Can I create a subset of just the best ranked, then rank them again within that higher end.
	• Is there any relation between price and score
	• Between price and complexity
	• Between complexity and score
	• What are the most used mechanisms
	• What are the least used mechanisms
	• Mechanisms with the highest scores
	• Score and playtime




