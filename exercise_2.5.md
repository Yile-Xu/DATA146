# Exercise for 2.5
1. Get a list of all the years in this data, without any duplicates. How many unique values are there, and what are they?

2. What is the largest value for population (pop) in this data? When and where did this occur?

3. Extract all the records for Europe. In 1952, which country had the smallest population, and what was the population in 2007?


```python
import panda as pd
data=pd.read_csv('gapminder.tsv', sep='\t')

#Q1
all_years=list(data['year'].unique())
print(all_years)
print(len(all_years))

#Q2
max_pop=data['pop'].max()
max_data=data[data['pop']==max_pop]
print(max_data)

#Q3
Europe_1952_data=data[(data['continent']=='Europe')&(data['year']== 1952)]
min_pop= Europe_1952_data['pop'].min()
min_country=Europe_1952_data[Europe_1952_data['pop']==min_pop]
print(min_country)
Iceland_2007_pop=data[(data['country']=='Iceland')&(data['year']== 2007)]
print(Iceland_2007_pop)
```
