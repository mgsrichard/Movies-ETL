# Movies-ETL (Extract Transform Load)
## ETL project for AmazingPrime Hackathon 

This week's work was cleaning up and merging movie data from Wikipedia and movie metadata and ratings from MovieLens via Kaggle for more than 6,000 movies.  The deliverable files represent different stages of the data work. The four deliverables and their purposes are:

### Deliverable 1: Write an ETL Function to Read Three Data Files - ETL_function_test.ipynb
The first deliverable creates a function called hackathon_bring_in_data() that reads in the three data files and stores each of them in their own DataFrames.  The three files are the Wikipedia movie data, the Kaggle movie metadata, and the Kaggle ratings data. At the end of the file are rows of code that look at the top of the three DataFrames created to be sure they imported correctly.

### Deliverable 2: Deliverable 2: Extract and Transform the Wikipedia Data - ETL_clean_wiki_movies.ipynb
The second deliverable creates a new function, clean_movie(), which does two things: 1)it combines alternate titles from several different columns whose headers are generally language names or transliteration types from different languages (i.e. Hangul or Romanization), and 2)it combines duplicate columns, such as composer/composer(s). Additionally, the hackathon_bring_in_data() function from deliverable 1 is also brought in and expanded here, with coding to clean/transform the wiki data. The steps added in this deliverable to the hackathon_bring_in_data() function are:
 - filter out tv shows
 - call the clean_movie() function described above and apply to each movie row in the data
 - read the cleaned movie data into a data frame
 - use a try except block with regex to extract the imdb id for each movie
 - eliminate columns with mostly null values (>90%)
 - clean up money columns budget and box office with a parse_dollars() function and regex for various forms
 - clean up release date column with regex and to_datetime
 - clean up running time column with regex
 - return the cleaned wiki DataFrame and the unchanged/uncleaned kaggle and ratings DataFrames

### Deliverable 3: Extract and Transform the Kaggle Data - ETL_clean_kaggle_data.ipynb
The third deliverable has both functions from deliverable 2, clean_movie() and hackathon_bring_in_data(). Clean_movie() is unchanged from deliverable 2.  Coding is added to hackathon_bring_in_data() to clean up the kaggle and ratings data and merge all three data files together into a comprehensive clean data file.  The steps added are:
- in kaggle data, delete adult films and delete the adult column
- in kaggle data, convert the video column to a boolean datatype
- in kaggle data, convert budget, id, and popularity to numeric datatype
- in kaggle data, convert release date to a datetime format
- merge the wiki and kaggle data files on the imdb id column
- drop wiki title, wiki release date, language, production company because we are keeping the kaggle versions of these
- create a function fill_missing_kaggle_data() to replace missing data in kaggle columns from wiki data, apply it to kaggle and wiki runtime, budget, revenue columns
- filter the database for the columns we want to keep
- reorder columns we are keeping for an order that makes sense for the hackathon participants
- in ratings file, transform timestamp column to a datetime datatype
- in ratings file, use groupby and count to calculate the number of times each movie got each rating and pivot these values into a table with a different shape, instead of 26 million rows of ratings we now have a row for each movie with the imdb id and a column for each possible rating. This compresses the file nicely and makes it much more comprehensible.  We also will save the unchanged ratings file for hackathon participants in case they want to dig into that also.
- merge the new ratings data columns into the merged wiki/kaggle DataFrame. We now have a DataFrame that combines all three data files cleanly.
- return the DataFrames created in here: the wiki_movies_df which represents the wiki data, the movies_with_ratings_df which is all three files pulled into one DataFrame, and the movies_df which is the wiki data combined with the kaggle data .

### Deliverable 4: Create the Movie Database - ETL_create_database.ipynb
Deliverable 4 has both of the functions from deliverable 3, clean_movie() and hackathon_bring_in_data(). Clean_movie() is unchanged, and hackathon_bring_in_data() is altered to write the movies DataFrame to a table in a SQL database, and replace the movie table if it already exists.  It's the file with the merged wiki and kaggle data. I don't know why we don't use the DataFrame with all three data files merged when we write this table to SQL, but the instructions for the challenge stated to bring in the movies_df and not the movies_with_ratings_df.  Additionally, the hackathon_bring_in_data() function imports the ratings file into another table in SQL called ratings. It imports it in chunks and reports every million rows as it goes along and how much time has elapsed with each chunk. The ratings table, though, will have to be deleted from the SQL database if you reimport it/rerun the code, because it appends to the existing file rather than replacing it.

Here is screenshot of the output showing the ratings file importing:
![ratings import](https://github.com/mgsrichard/Movies-ETL/blob/main/elapsed_time.png)

Here are screenshots of queries from the database tables movies and ratings, showing how many rows imported.
![movies query](https://github.com/mgsrichard/Movies-ETL/blob/main/movies_query.png)
![ratings query](https://github.com/mgsrichard/Movies-ETL/blob/main/ratings_query.png)
