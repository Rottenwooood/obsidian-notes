https://pandas.pydata.org/docs/user_guide/10min.html
https://www.kaggle.com/learn/pandas
### 导入&DataFrame&Series
```python
import pandas as pd
pd.DataFrame({'Yes': [50, 21], 'No': [131, 2]})
pd.DataFrame({'Bob': ['I liked it.', 'It was awful.'], 'Sue': ['Pretty good.', 'Bland.']})
pd.DataFrame({'Bob': ['I liked it.', 'It was awful.','1'],
              'Sue': ['Pretty good.', 'Bland.','2']},
             index=['Product A', 'Product B','Product C'])

                
pd.Series([1, 1, 3, 4, 5],dtype=float)
pd.Series([30, 35, 40], index=['2015 Sales', '2016 Sales', '2017 Sales'], name='Product A')
```
### CSV
```python
wine_reviews = pd.read_csv("/kaggle/input/wine-reviews/winemag-data-130k-v2.csv")
wine_reviews.head() # 显示前五行
# 使用第0列作为index
wine_reviews = pd.read_csv("/kaggle/input/wine-reviews/winemag-data-130k-v2.csv", index_col=0)
wine_reviews.head()
```
### Exercise 1
```python
pd.DataFrame([[30, 21]], columns=['Apples', 'Bananas'])
pd.DataFrame([[35, 21], [41, 34]], columns=['Apples', 'Bananas'],
                index=['2017 Sales', '2018 Sales'])

ingredients = pd.Series(['4 cups', '1 cup', '2 large', '1 can'], name='Dinner',
                index=['Flour', 'Milk', 'Eggs', 'Spam'],dtype = object)
quantities = ['4 cups', '1 cup', '2 large', '1 can']
items = ['Flour', 'Milk', 'Eggs', 'Spam']
recipe = pd.Series(quantities, index=items, name='Dinner')

animals = pd.DataFrame({'Cows': [12, 20], 'Goats': [22, 19]}, index=['Year 1', 'Year 2'])
animals.to_csv('cows_and_goats.csv')
```
### 索引
```python
# 访问列
reviews.country
reviews['country']
# fancy字典
reviews['country'][0]
```
### iloc
基于矩阵的访问
先行后列
```python
reviews.iloc[0] # 第0行
reviews.iloc[:, 0]  # 第0列
reviews.iloc[:3, 0] # 第0列，0-2行
reviews.iloc[[0, 1, 2], 0] # 传入列表
```
### loc
基于tag的访问
```python
reviews.loc[0, 'country']
reviews.loc[:, ['taster_name', 'taster_twitter_handle', 'points']]
```
	陷阱：iloc是左闭右开，loc是左闭右闭
### 条件选择
```python
reviews.country == 'Italy' # 生成一个筛选Italy的mask序列
reviews.loc[reviews.country == 'Italy']# 条件筛选
reviews.loc[(reviews.country == 'Italy') & (reviews.points >= 90)]
reviews.loc[(reviews.country == 'Italy') | (reviews.points >= 90)]
reviews.loc[reviews.country.isin(['Italy', 'France'])]

reviews.loc[reviews.price.notnull()]
```
### 分配数据
```python
reviews['critic'] = 'everyone'
reviews['index_backwards'] = range(len(reviews), 0, -1)
```
### 重命名
```python
reviews.rename(columns={'points': 'score'})
reviews.rename(index={0: 'firstEntry', 1: 'secondEntry'})

reviews.rename_axis("wines", axis='rows').rename_axis("fields", axis='columns')
```
### 合并
```python
canadian_youtube = pd.read_csv("/kaggle/input/youtube-new/CAvideos.csv")
british_youtube = pd.read_csv("/kaggle/input/youtube-new/GBvideos.csv")  

pd.concat([canadian_youtube, british_youtube])

left = canadian_youtube.set_index(['title', 'trending_date'])
right = british_youtube.set_index(['title', 'trending_date'])

left.join(right, lsuffix='_CAN', rsuffix='_UK')
```
### 分组
```python
# 根据points分组，另一列是该points的对象的个数
reviews.groupby('points').points.count()
# 根据porints分组，另一列是该points中price的最小值
reviews.groupby('points').price.min()
# 根据winery分组，apply中任意筛选数据（第一次的评论）
reviews.groupby('winery').apply(lambda df: df.title.iloc[0])
# 根据country分组，筛选出points最大的部分
reviews.groupby(['country', 'province']).apply(lambda df: df.loc[df.points.idxmax()])
# agg()
reviews.groupby(['country']).price.agg([len, min, max])
reviews.groupby(['country', 'province']).description.agg([len])
# 多索引
countries_reviewed.reset_index()
```
### 排序
```python
# 默认升序
countries_reviewed.sort_values(by='len')
# 降序
countries_reviewed.sort_values(by='len', ascending=False)
# 按照索引排序
countries_reviewed.sort_index()
# 按照多个列排序
countries_reviewed.sort_values(by=['country', 'len'])
```
### 填充&替换
```python
# 筛选出NaN值
reviews[pd.isnull(reviews.country)]
# 填充NaN
reviews.region_2.fillna("Unknown")
reviews.taster_twitter_handle.replace("@kerinokeefe", "@kerino")
```