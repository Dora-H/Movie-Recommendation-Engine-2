# Movie-Recommendation-Engine-2-
The purpose of this recommendation engine is to recommend users some un-watched movies. If any unwatched movie got no rate, the rate will be put a median instead, because the median is the value separating the higher half from the lower half of all rates.   The basic feature of the median in describing data is that it is not skewed by a small proportion of extremely large or small values, and therefore provides a better representation of a "typical" value.


## System Algorithm
Collaborative Filtering  

## Similarity Measurement
Euclidean Distance  

## Replace Missing Values
Median

## Steps:
   1. Call the main finction to work  
   2. Run read() function to read json document first  
      2-1. call get_all_movie_rates() function  
      2-2. call get_movie_rates_median() function  
      2-3. call get_user_names() function  
      2-4. call run() function to run the engine  
   3. Run get_all_movie_rates() function  
      3-1. call draw_movies_counts() function 
   4. Run draw_movies_counts() function

## Requirements
● Python 3.8    
● json  
● numpy   
● matplotlib  
● statistics  
● seaborn


## Class
MovieRecommendationEngine


## Functions
● write_to_csv  
● draw_movies_watched_counts  
● draw_user_similarity  
● draw_movie_rates  
● user_similarity  
● get_all_movie_rates  
● get_movie_rates_median  
● get_all_movie_list  
● get_user_names  
● run  
● read   


## Create __init__
#### define all basic sets :
    def __init__(self):
            self.movie_name = 'Movie Names'
            self.x = 'Similarity'
            self.y = 'Users'
            self.movie_scale = ['V Bad', 'Bad', 'Ok', 'Good', 'V Good', 'Perfect']
            self.yts = [0, 1, 2, 3, 4, 5]
            self.fig_size = (12, 9)
            self.label_fontsize = 20
            self.title_fontsize = 25
            self.fig_size = (12, 9)
            
            
## Run codes
#### 1. Call the main finction to work :
    if __name__ == "__main__":
        go = MovieRecommendationEngine()
        go.read()

#### 2. Run read() function :
    def read(self):
        with open('ratings.json', 'r') as f:
                    ratings = json.loads(f.read())
        
##### 2-1. call get_all_movie_rates() function
        all_movie_rates = self.get_all_movie_rates(ratings)
        
#### 3. Run get_all_movie_rates() function :
     def get_all_movie_rates(self, ratings):
          all_movie_rates, movie_watched_counts = {}, {}
          for name in ratings.keys():
              for movie in ratings[name].keys():
                  if movie in all_movie_rates:
                      all_movie_rates[movie].append(ratings[name][movie])
                      movie_watched_counts[movie] += 1
                  else:  
                      all_movie_rates[movie] = [ratings[name][movie]]
                      movie_watched_counts[movie] = 1
                      
##### 3-1. call draw_movies_counts() function
          self.draw_movies_watched_counts(movie_watched_counts)
          return all_movie_rates

#### 4. Run draw_movies_counts() function
    def draw_movies_watched_counts(self, counts):
        '''{'Inception': 6, 'Pulp Fiction': 7, 'Anger Management': 4,
             'Fracture': 8, 'Serendipity': 7, 'Jerry Maguire': 7}'''
        m, r = [], []
        for movie, count in sorted(counts.items(), key=lambda counts:counts[1], reverse=True):
            m.append(movie)
            r.append(count)

        mp.figure('Moive Watched Times', figsize=self.fig_size)
        sns.set(context='notebook', style='white')
        mp.title('Moive Watched Times', fontsize=self.title_fontsize)
        mp.xlabel(self.movie_name, fontsize=self.label_fontsize)
        mp.ylabel('Counts', fontsize=self.label_fontsize)
        mp.xticks(range(0, len(m)), m, rotation=10)
        mp.bar(m, r, width=0.5)
        mp.tight_layout()
        mp.show()
![Moive_Watched_Times](https://user-images.githubusercontent.com/70878758/132020746-c95c3a79-c496-4d5e-b63a-008c8b4e4b01.jpeg)
