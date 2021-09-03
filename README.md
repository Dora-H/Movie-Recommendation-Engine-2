# Movie-Recommendation-Engine-2
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
      3-1. call draw_movies_watched_counts function 
   4. Run draw_movies_counts() function
   5. Run get_movie_rates_median() function
   6. Run get_user_names() function  
   7. Run run() function  
      7-1. call user_similarity() fuction  
      7-2. call draw_user_similarity()  
   8. Run user_similarity() function  
      8-1. call write_to_csv() function  
   9. Run write_to_csv() function  
   10. Run draw_user_similarity() function  
      10-1 call draw_movie_rates() function  
   11. Run draw_movie_rates() function  


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
                      
##### 3-1. call draw_movies_watched_counts function
          self.draw_movies_watched_counts(movie_watched_counts)
          return all_movie_rates

#### 4. Run draw_movies_watched_counts function
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

##### 2-2. call get_movie_rates_median() function
        median_ratings, mrm = self.get_movie_rates_median(all_movie_rates, ratings)
 
#### 5. Run get_movie_rates_median() function
    def get_movie_rates_median(self, all_movie_rates, ratings):
        mrm = {}
        for movie in all_movie_rates.keys():
            mrm[movie] = statistics.median(all_movie_rates[movie])
        for name in ratings.keys():
            for movie in set(mrm)-set(ratings[name]):
                ratings[name][movie] = mrm[movie]
        return ratings, mrm
        
##### 2-3. call get_user_names() function
        user_names = self.get_user_names(median_ratings)
        
#### 6. Run get_user_names() function        
    def get_user_names(self, ratings):
        user_names = list(ratings.keys())
        return user_names

##### 2-4. call run() function to run the engine
        self.run(user_names, median_ratings, mrm)
        
#### 7. Run run() function
    def run(self, user_names, ratings, mrm):
        all_movie_list, scmat = set(), []
        for user_row in user_names:
            score_row = []
            for user_column in user_names:
                movies = set()
                for movie in ratings[user_row]:
                     if movie in ratings[user_column]:
                        movies.add(movie)

                a, b = [], []
                for x in movies:
                    a.append(ratings[user_row][x])
                    b.append(ratings[user_column][x])
                # 計算a,b的歐式距離
                a, b = np.array(a), np.array(b)
                score = 1 / (1 + np.sqrt(((a-b)**2).sum()))
                score_row.append(score)
                all_movie_list.update(movies)
            scmat.append(score_row)
        self.get_all_movie_list(all_movie_list)

        new_matrix, similar, max_arg = [], [], []
        for row in scmat:
            new_row = []
            for score in row:
                if score == 1.0:
                    score -= 1
                new_row.append(round(np.max(score), 2))
            new_matrix.append(new_row)

        for new_row in new_matrix:
            max_arg.append(np.argmax(new_row)+0.2)
            similar.append(user_names[np.argmax(new_row)])

##### 7-1. call user_similarity() fuction
        self.user_similarity(mrm, user_names, scmat)
        
#### 8. Run user_similarity() function
    def user_similarity(self, mrm, user_names, scmat):
        with open('ratings.json', 'r') as f:
            origin_ratings = json.loads(f.read())

            scmat = np.array(scmat)
        user_names = np.array(user_names)
        recom_list = []

        
        '''升序排序出除了自己以外的此用者相關度'''
        for i, user in enumerate(user_names):
            sorted_indexs = scmat[i].argsort()[::-1]
            sorted_Ascending_indexs = sorted_indexs[sorted_indexs != i]
            similar_users = user_names[sorted_Ascending_indexs]
            similar_scores = np.round(scmat[i, sorted_Ascending_indexs], 2) *100
            print()
            # 打印出使用者相似程度結果
            print('使用者%d: %s\n相似程度使用者:\n%s\n相似分數%%:\n%s' %
                  (i+1, user, similar_users, similar_scores))

            '''以下製作推薦清單 : 1.取相關係數 > 0(正相關) 2.取打分高 3.取權重高 '''
            positive_mask = similar_scores > 0
            similar_users = similar_users[positive_mask]
            similar_scores = similar_scores[positive_mask]

            # 建立一個空字典(score_sums)把使用者沒有看過的電影清單以權重得分作為values放進
            score_sums = {}
            user_weight_sums = {}
            for similar_user, similar_score in zip(similar_users, similar_scores):
                for movie, score in origin_ratings[similar_user].items():
                    '''假設現在遍歷到'William Reynolds'。某一個正向相似度高的使用者('John')
                    看過的電影清單內中的一部Serendipity，沒有在William Reynolds看過的電影清單內，
                    就先把Serendipity打上該電影的中位數'''
                    if movie not in origin_ratings[user].keys():
                        if movie not in score_sums.keys():
                            score_sums[movie] = mrm[movie]
                        score_sums[movie] += score * similar_score

                        if movie not in user_weight_sums.keys():
                            user_weight_sums[movie] = mrm[movie]
                        user_weight_sums[movie] += similar_score

            # 建立一個空電影等級空字典，用來裝放要推薦的電影
            movie_ranks = {}
            for movie, score_sum in score_sums.items():
                movie_ranks[movie] = score_sum / user_weight_sums[movie]
            valid_recomm_index = np.array(list(movie_ranks.values())).argsort()[::-1]
            recomms = np.array(list(movie_ranks.keys()))[valid_recomm_index]
            recom_list.append((user, recomms))
            
#####  8-1. 8-1. call write_to_csv() function          
        self.write_to_csv(recom_list)

#### 9. Run write_to_csv() function      
    def write_to_csv(self, recom_list):
        with open('recommendations.csv', 'w') as f:
            for data in recom_list:
                seq = ['Recommend ' + str(data[0]) + str(data[1])]
                f.writelines(seq)
                f.write('\n')
        f.close()
        
##### 7-2. call draw_user_similarity() 
        self.draw_user_similarity(user_names, max_arg, ratings)
 
#### 10. Run draw_user_similarity() 
    def draw_user_similarity(self, user_names, max_arg, ratings):
        mp.figure(self.x, figsize=self.fig_size)
        sns.set(context='talk', style='white')

        mp.subplot(211)
        mp.title(self.x + ' '+self.y, fontsize=self.title_fontsize)
        mp.grid(":")
        mp.xlabel(self.x, fontsize=self.label_fontsize)
        mp.ylabel(self.y, fontsize=self.label_fontsize)
        mp.xticks(range(0, len(user_names)), user_names, rotation=10)
        mp.ylim(-1, len(user_names))
        mp.xlim(-0.25, len(user_names))
        mp.barh(user_names, max_arg, zorder=3)

        mp.subplot(212)
        mp.xlabel(self.y, fontsize=self.label_fontsize)
        mp.ylabel(self.x, fontsize=self.label_fontsize)
        mp.xticks(rotation=10)
        mp.yticks(range(0, len(user_names)), user_names)
        mp.xlim(-0.5, len(user_names))
        mp.scatter(user_names, max_arg, color='orange', s=150, zorder=3)
        mp.vlines(user_names, -0.2, max_arg, linestyle="dashed", color='coral')
        mp.hlines(max_arg, -0.8, user_names, linestyle="dashed", color='coral', linewidth=3)
        mp.tight_layout()
        sns.set()
        mp.show()
![Similarity](https://user-images.githubusercontent.com/70878758/132031964-e5c8d95e-9c80-44f1-8b6f-fec68c156aa1.png)


##### 10-1 call  draw_movie_rates() function
        self.draw_movie_rates(ratings, user_names)
        
#### 11. Run draw_movie_rates() function 
    def draw_movie_rates(self, ratings, user_names):
        mp.figure('Movies Rates', figsize=self.fig_size)
        mp.title('Rates', fontsize=self.title_fontsize)
        king, item = {}, []
        for name in ratings.keys():
            user_data = ratings[name]
            king[name] = []
            for movie, score in user_data.items():
                item.append((movie, score))
                king[name].append((movie, score))

        movies, scores = [], []
        for i in king.values():
            for f in i:
                movies.append(f[0])
                scores.append(f[1])

        sns.set(style='white')
        mp.subplot(421)
        mp.title('%s' % user_names[0])
        mp.grid(axis='y')
        mp.xticks(fontsize=9, rotation=13)
        mp.yticks(self.yts, self.movie_scale)
        mp.xlim(-0.25, 5.2)
        mp.ylim(0, 5)
        mp.bar(movies[:6], scores[:6], width=0.6)

        mp.subplot(422)
        mp.title('%s' % user_names[1])
        mp.grid(axis='y')
        mp.xticks(fontsize=9, rotation=13)
        mp.xlim(-0.25, 5.2)
        mp.ylim(0, 5)
        mp.bar(movies[6:12], scores[6:12], width=0.6)

        mp.subplot(423)
        mp.title('%s' % user_names[2])
        mp.grid(axis='y')
        mp.xticks(fontsize=9, rotation=13)
        mp.yticks(self.yts, self.movie_scale)
        mp.xlim(-0.25, 5.2)
        mp.ylim(0, 5)
        mp.bar(movies[12:18], scores[12:18], width=0.6)

        mp.subplot(424)
        mp.title('%s' % user_names[3])
        mp.grid(axis='y')
        mp.xticks(fontsize=9, rotation=13)
        mp.xlim(-0.25, 5.2)
        mp.ylim(0, 5)
        mp.bar(movies[18:24], scores[18:24], width=0.6)

        mp.subplot(425)
        mp.title('%s' % user_names[4])
        mp.grid(axis='y')
        mp.ylabel('  ', fontsize=20)
        mp.xticks(fontsize=9, rotation=13)
        mp.yticks(self.yts, self.movie_scale)
        mp.xlim(-0.25, 5.2)
        mp.ylim(0, 5)
        mp.bar(movies[24:30], scores[24:30], width=0.6)

        mp.subplot(426)
        mp.title('%s' % user_names[5])
        mp.grid(axis='y')
        mp.xticks(fontsize=9, rotation=13)
        mp.xlim(-0.25, 5.2)
        mp.ylim(0, 5)
        mp.bar(movies[30:36], scores[30:36], width=0.6)

        mp.subplot(427)
        mp.title('%s' % user_names[6])
        mp.grid(axis='y')
        mp.xlabel(self.movie_name, fontsize=18)
        mp.xticks(fontsize=9, rotation=13)
        mp.yticks(self.yts, self.movie_scale)
        mp.xlim(-0.25, 5.2)
        mp.ylim(0, 5)
        mp.bar(movies[36:42], scores[36:42], width=0.6)

        mp.subplot(428)
        mp.title('%s' % user_names[7])
        mp.grid(axis='y')
        mp.xticks(fontsize=9, rotation=13)
        mp.xlabel(self.movie_name, fontsize=18)
        mp.xlim(-0.25, 5.2)
        mp.ylim(0, 5)
        mp.bar(movies[42:], scores[42:], width=0.6)

        mp.tight_layout()
        mp.show()
![Movies_Rates](https://user-images.githubusercontent.com/70878758/132031894-419ef85e-d3f9-4adf-ba2f-c569500caabd.png)
