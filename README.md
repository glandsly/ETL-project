# ETL Project

For this project we looked at data regarding the video game platform Steam. We segmented and merged csvs into multiple tables and then brought everything back together in a clean, organized database using PostgreSQL and Pandas.

Data sourced from kaggle:
- https://www.kaggle.com/tamber/steam-video-games
- https://www.kaggle.com/luthfim/steam-reviews-dataset
- https://www.kaggle.com/trolukovich/steam-games-complete-dataset

## erd.txt
Our first goal was to visualize the database we were creating by generating an ERD using www.quickdatabasediagrams.com. Once generated we decided on a relational database as we would be able to connect multiple tables based on game_id and user_id.

We used the code generated by quickdbd.com to quickly generate our tables in pgAdmin 4.

![ERD](/images/ERD.png)

## import_games.ipynb
This notebook was used to narrow down our dataset by games found in the steam reviews dataset.

This dataset contained a specific number of reviews from Steam's best selling games as of February 2019. Narrowing down to these games let us created a focused database as well as apply transformation across all other datasets.

A few other transformations we did in this file involved dropping titles with incomplete data as well as removing specific characters so that the data was consistent across all tables.

Finally we exported the finished dataset to titles.csv.

## game_title.ipynb
For the games table, first we dropped a large number of columns from the steam games complete dataset that contained information either unnecessary or difficult to merge. Using the top games identified from the titles.csv, we then compared the values in our new dataframe to the top games and filtered it down to just the matches.

Next we dealt with special characters across our dataset, using replace and regex to clean the dataframe.

With our data mostly collected at this point, the last few things needed was to rename some columns to make more logical sense and to increase consistency. Finally we dropped any NaN values and reset the index so that all tables would match in our finished database.

We also filtered the reviews column to just the count of reviews using str extract and converted that extracted string into an int so it matched our ERD.

With this complete, we exported the dataframe to a cleaned CSV so that it could be easily loaded into pgAdmin.

## user_table.ipynb
This notebook built the framework for the user_table. It involved the following steps: 
- Dropping/renaming columns
- Merging to only include users that were in found in titles.csv 
- Then exporting the complete data to user_table.csv.

## purchase_table.ipynb
For the first Kaggle source, Steam Video Games, our team decided to use the following columns of data from the CSV to create our Purchase Table: user id, game title, and play time. The original CSV did not have column headers so these are the titles that we assigned. Further, in the original CSV there was a column of play/purchase data which related to the column “play_time”. However, if a user only purchased a game and did not yet play it, there was a 1 assigned to the “play_time” column. For users who played the game, their play time hours were listed. As such, we dropped all rows containing “purchase” since they did not include play time data, and we wanted to ensure accuracy. Once this step was complete, we dropped the play_purchase column as it was no longer useful, and we also dropped an unneeded column of zeros found in the CSV. To further clean up the dataset, we dropped all rows with n/a values.

To incorporate another dataset, we merged this edited data frame with the transformed Steam Reviews Dataset (titles.csv) using the game title from both CSVs. The purpose of this merge was to add game id data, which could then be used to link this table to the Game Table. The final Purchase Table includes game id, game title, user id, and play time. We saved the data frame as Purchase_Table.csv and imported it to SQL on pgAdmin4 using the ERD_SQL.sql and data_etl.ipynb files.

## review_table.ipynb
The Review Table data was extracted from the csv in the steam reviews dataset.

The Review table needed:
- Columns dropped/removed
- Funny, helpful, and is early access review were removed
- Rearranged columns
- The ‘name’ column renamed to ‘game_title’
- An ‘id’ column added
- The Game Title CSV merged with the Review Table
- Needed to have Game Id’s for each game.

 The Review Table was chosen because we wanted to compare and parse user input on games. We merged the Game Title CSV with the Review Table because we wanted to easily sort reviews by game information. This dataset had over 300,000 reviews across 25+ games. As such, we chose the Best-Selling Games from Steam to narrow our focus due primarily to the size of this dataset in particular.

## data_etl.ipynb
After the data was cleaned, and merged, the data was successfully uploaded into the Postgres SQL Database. This notebook finally loaded the databa.se using sqlaclhemy. It loaded all of the completed csv's, adjusted a few column names to meet our ERD requirements and then finally appended the data to tables that we created in a local pgadmin database.


