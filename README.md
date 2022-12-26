# Python-Data-Cleaning-and-Analysis-Project
Cleaned the IMDB Movies Data of more 6000 rows using Jupyter Notebook, python libraries such as numpy, pandas by removing all the null values, dropping all unnecessary  rows and columns, deleting duplicate values so that data can be used for further analysis. And answered various question using python that can generate new insights.



## In[369]:


import numpy as np
import pandas as pd
import matplotlib.pyplot as plt


## Importing and reading the movie database. Storing it in a variable called movies.

## In[370]:


movies = pd.read_csv('IMDB_Movies.csv')
originaldata = movies
movies


## Inspecting the dataframe shapes, coloumns, varible types etc.

## In[371]:


movies.shape


## In[372]:


movies.info()


## Calculating the number of Null values of rows and coloumns. Finding the    percentage of Null values in each coloumn.

## In[373]:


movies.isnull().sum(axis=0).sort_values(ascending=False)


## In[374]:


movies.isnull().sum(axis=1).sort_values(ascending=False)


## In[375]:


movies.isnull().sum(axis=0).sort_values(ascending=False)/len(movies) * 100


## Drop the unecessary coloumns.
## Coloumns which are irrelevant and to be dropped are:
   color
   director_facebook_likes
   actor_1_facebook_likes
   actor_2_facebook_likes
   actor_3_facebook_likes
   actor_2_name
   cast_total_facebook_likes
   actor_3_name
   duration
   facenumber_in_poster
   content_rating
   country
   movies_imdb_link
   aspect_ratio
   plot_keywords

## In[376]:


movies = movies.drop([
    
    'color',
    'director_facebook_likes',
    'actor_1_facebook_likes',
    'actor_2_facebook_likes',
    'actor_3_facebook_likes',
    'actor_2_name',
    'cast_total_facebook_likes',
    'actor_3_name',
    'duration',
    'facenumber_in_poster',
    'content_rating',
    'country',
    'movie_imdb_link',
    'aspect_ratio',
    'plot_keywords'],axis=1)


## In[377]:


movies


## Drop the unecessary rows using coloumns with high Null percentages(greater than 5%) of Null Values.

## In[378]:


round(movies.isnull().sum().sort_values(ascending=False)/len(movies)*100,2)


## In[379]:


movies = movies[movies['gross'].notnull()]
movies = movies[movies['budget'].notnull()]


## In[380]:


round(movies.isnull().sum().sort_values(ascending=False)/len(movies)*100,2)


## Drop more unecessary rows which might have greater than five NaN values. Such rows are not of much use for the analysis and hence, should be removed.

## In[381]:


(movies.isnull().sum(axis=1).sort_values(ascending=False) > 5).sum()


## In[382]:


movies = movies[movies.isnull().sum(axis=1).sort_values(ascending=False) <= 5]
movies


## Replace all the missing NaN values with 'English' in the Language coloumn.

## In[383]:


round(movies.isnull().sum().sort_values(ascending=False)/len(movies)*100,2)


## In[384]:


movies.language.describe()


## In[385]:


movies.language = movies.language.fillna('English')


## In[386]:


round(movies.isnull().sum().sort_values(ascending=False)/len(movies)*100,2)


## Checking the percentage of the rows retained after completing all the tasks above.

## In[387]:


movies


## In[388]:


len(movies)/len(originaldata)* 100


## Analysing Data - Converting the unit of budget and gross coloumns to million dollar.

## In[389]:


movies['budget'] = movies['budget']/1000000
movies['gross'] = movies['gross']/1000000


## In[390]:


movies


## Finding the movies with highest profit and store the top ten movies with highest profit in a new dataframe- top 10

## In[391]:


movies['profit'] = movies['gross'] - movies['budget']
movies


## In[392]:


movies.sort_values(by='profit',ascending=False)


## In[393]:


top10 = movies.sort_values(by='profit',ascending=False).head(10)
top10


## Drop the duplicate values.

## In[394]:


movies.drop_duplicates(keep='first',inplace=True)


## In[395]:


movies


## In[396]:


top10 = movies.sort_values(by='profit',ascending=False).head(10)
top10


## Finding IMDB top 100 movies with highest IMDB rating and number of voted users greater than 25000. Add a rank coloumn containing the values 1 to 100. 

## In[397]:


IMDb_Top_100 = movies[movies['num_voted_users'] > 25000].sort_values(by='imdb_score',ascending=False).head(100)
IMDb_Top_100


## In[398]:


IMDb_Top_100['Rank'] = IMDb_Top_100['imdb_score'].rank(method='first',ascending=False)
IMDb_Top_100


## In[399]:


IMDb_Top_100[IMDb_Top_100['language']!='English']


## Finding the best directors with high imdb scores and store top 10 directors in a new coloumn.

## In[400]:


top10director = movies.groupby('director_name').imdb_score.mean().sort_values(ascending=False).head(10)
top10director


## Finding popular genres.

## In[401]:


TempGenre = movies.genres.str.split('|',expand=True).iloc[:,0:2]
TempGenre.columns=['genre_1','genre_2']
TempGenre.genre_2.fillna(TempGenre.genre_1,inplace=True)
TempGenre


## In[402]:


movies = pd.concat([movies,TempGenre],axis=1)
movies


## In[403]:


movies.groupby(['genre_1','genre_2']).gross.mean().sort_values(ascending=False).head(5)


## Finding the critic-favourite and audience-favourite actors.

## In[404]:


Meryl_Streep = movies[movies['actor_1_name']=='Meryl Streep']
Leo_Caprio = movies[movies['actor_1_name']=='Leonardo DiCaprio']
Brad_Pitt = movies[movies['actor_1_name']=='Brad Pitt']


## In[405]:


Combined = Meryl_Streep.append([Leo_Caprio,Brad_Pitt])
Combined


## In[406]:


Combined.groupby('actor_1_name').num_critic_for_reviews.mean()


## In[407]:


Combined


## In[408]:


Combined.num_user_for_reviews = Combined.num_user_for_reviews.astype('int')
Combined.num_user_for_reviews


## In[409]:


Combined.groupby('actor_1_name').num_user_for_reviews.mean()


## In[410]:


Combined.groupby('actor_1_name')[['num_critic_for_reviews','num_user_for_reviews']].mean()
