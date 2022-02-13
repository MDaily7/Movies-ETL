# Movies-ETL
## Description
This project was utilized to gain experience with the Extract, Transform, and Load (ETL) process of data analysis. The three data sets used were extracted into pandas dataframes. The 
transformation process consisted almost entirely of data cleaning; the data cleaning strategy followed an iterative process of inspect, plan, exectute where the data was inspected for issues, a plan was developed for handling the issues, and 
the plan was executed to repair the issues. Each phase of the iterative process narrowed in focus beginning with broad strokes such as filtering out unneeded data (removing 
tv shows from the movies dataframe for example) and consolidating columns with similar types of data (creating an alternative titles column for the various titles in different languages
for example). The broad strokes eventually gave way to more refined approaches which included using regular expressions (regex) to extract particular patterns within the values in 
columns. Once the data was cleaned to reasonable standards, the cleaned dataframes were merged and some final cleaning took place on the resulting merged dataframe. Finally, the ratings.csv and final merged dataframe were loaded into a Postgres database.
## Methods
### Extract
* The wikipedia-movies.json was opened in read mode and then loaded into a list of dictionaries with json.load(). The list of dictionaries was then added to a dataframe with pd.DataFrame()
* Both movies_metadata.csv and ratings.csv were read into dataframes with pd.read_csv(). 
### Transform
* wiki_movies_df: list comprehension was used to keep only movies with imdb_links and directors. A clean movies function was created to consolidate alternate titles into one column
and to change the column names of the dataframe to maintain consistent formatting. Regular expressions were used to extract imdb ids from the imdb_link column and duplicate ids were dropped.
List comprehension was used again to create a list of columns that were less than 90% null and the dataframe was then updated to contain only columns from that list. Finally, various columns were cleaned in the iterative
fashion described above where regexs were used to extract particular patterns of values within the columns with .str.extract(). [Extract Example](https://github.com/MDaily7/Movies-ETL/blob/main/Data/StringExtractExample.PNG) provides an example of this
where four patterns seen as the release date forms are entered into the extract method. The data extracted was saved into a new columns and the columns it was extracted from were dropped
from the dataframe.
* kaggle_metadata_df: Bad data was isolated and then dropped from the dataframe by filtering it on the 'adult' column and dropping any data where 'adult' did not equal 'False'; the 'adult' column was subsequently dropped.
Next, the data types of various columns were converted to more appropriate types. The 'budget' column was converted to integer with .astype(int) for example. 
* creating and cleaning the movies dataframe: the movies dataframe was created with an inner merge betweent the wiki_movies_df and the kaggle_metadata_df. Duplicate columns were
inspected, a plan was created for which columns to keep, and the plan was executed where several wiki columns were dropped and a function was used to fill missing kaggle data 
with wiki data within the merged dataframe. Finally, the columns were reorderd (and the 'video' column was filtered out) before being renamed to maintain consistency.
* ratings_df: the ratings_df was determined to be fairly clean to begin with, but was too large to merge. Therefore, the data was summarized by grouping the data on 'movieId' and 'rating', counting the ratings, renaming the 'userId' column to 'count' and 
pivoting the table so that the movie Id became the index, the columns became the rating values, and the rows became the counts for each value. The resulting dataframe was then
merged with the movies_df dataframe to create the movies_with_ratings_df dataframe and missing values were replaced with zeros with .fillna(0).
### Load
The sqlalchemy module was used to import the data into Postgres. The engine was created using create_engine() with the appropriate database string provided. The movies_with_ratings_df was 
then loaded into the database with .to_sql() where the name argument is used to specify the name of the table in Postgres and the engine is used for the connection as seen in
[Loading Movies](https://github.com/MDaily7/Movies-ETL/blob/main/Data/Loading_Movies.png). The ratings.csv was too large to simply load into Postgres, so it was instead loaded in chunks with a for loop as seen in [Loading ratings.csv](https://github.com/MDaily7/Movies-ETL/blob/main/Data/Loading_ratingscsv.png) with print statements 
used to indicate the various chunks of data being loaded at any one time and the total amount of time passed per chunk loaded.
## Resources
* movie_metadata.csv and ratings.csv obtained from kaggle here https://www.kaggle.com/rounakbanik/the-movies-dataset/download   (requires an account)
* [Data](https://github.com/MDaily7/Movies-ETL/tree/main/Data) contains relevant csvs and images
* Anaconda 4.11.0
* Python 3.7.11
* [Data_import_explore](https://github.com/MDaily7/Movies-ETL/blob/main/Data_import_explore.ipynb) is the jupyter notebook containing the ETL process described above.
* [ETL_create_database](https://github.com/MDaily7/Movies-ETL/blob/main/ETL_create_database.ipynb) is the jupyter notebook containing the refactored ETL process.













