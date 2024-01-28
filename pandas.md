# pandas

## install

```bash
# show installed
pd.show_versions()
```

## read & write

```python
# csv
reader = pd.read_csv('data/servicelogs', iterator=True)
df.to_csv('foo.csv')

# hdf5
pd.read_hdf('foo.h5','df')
df.to_hdf('foo.h5','df')

# excel
df.to_excel('foo.xlsx', sheet_name='Sheet1')
pd.read_excel('foo.xlsx', 'Sheet1', index_col=None, na_values=['NA'])
```

## dataframe

```python
# create
pd.Series([1,3,5,np.nan,6,8])
pd.DataFrame(np.random.randn(6,4), index=dates, columns=list('ABCD'))

# copy
df2 = df.copy()

# look
df.head()
df.tail(3)
df.index
df.columns
df.values
df.describe()
df.dtypes
df.inro()
df.<TAB>

# select
df['A'] # column
df[0:3] # index
df['20130102':'20130104'] # key

# sort
df.sort_index(axis=1, ascending=False)
df.sort_values(by='B')
df.sort_values(by='col1', ascending=False)

# access
df.loc[:,['A','B']] # 多轴选择
df.loc[dates[0],'A'] # 获取一个标量
df.loc[df['aaa']>5,['bbb','ccc']] = 555
df.at[dates[0],'A'] # 同上
df.iloc[3] #按行
df.iloc[3:5,0:2]
df.iloc[[1,2,4],[0,2]]
df.iloc[1:3,:]
df.iloc[1,1] # 获取特定
df.iat[1,1] # 同上

# filter
df[df.A > 0]
df2[df2['E'].isin(['two','four'])]
df[(df.AAA >= 6) & (df.index.isin([0,2,4]))]
df['logic'] = np.where(df['AAA'] > 5,'high','low')

# add new column
df['F'] =  pd.Series([1,2,3,4,5,6], index=pd.date_range('20130102', periods=6))
df.loc[:,'D'] = np.array([5] * len(df))

# set new value
df.at[dates[0],'A'] = 0

# change index
df1 = df.reindex(index=dates[0:4], columns=list(df.columns) + ['E'])

# drop null
df1.dropna(how='any')
# drop rows with at least two nulls
df1.dropna(thresh=2)

# fill null
df1.fillna(value=5)
pd.isnull(df1)

# statistic
df.mean(1)
df.mean(axis=0) # 每一列
df.mean(axis=1) # 每一行

# apply func
df.apply(np.cumsum)
df.apply(lambda x: x.max() - x.min())

# string
s = pd.Series(['A', 'B', 'C', 'Aaba', 'Baca', np.nan, 'CABA', 'dog', 'cat'])
s.str.lower()

# logic op
# concat
pieces = [df[:3], df[3:7], df[7:]]
pd.concat(pieces)

# join
pd.merge(left, right, on='key')
result = pd.merge(left, right, how='inner', on=['key1', 'key2'])

# append
s = df.iloc[3]
df.append(s, ignore_index=True)

# groupby
df.groupby('A').sum()
df.groupby(['A','B']).sum()

# stack
stacked = df.stack()
stacked.unstack()
stacked.unstack(1)

# pivot
pd.pivot_table(df, values='D', index=['A', 'B'], columns=['C'])

# resample
ts = pd.Series(np.random.randint(0, 500, len(rng)), index=rng)
ts.resample('5Min').sum()

# categorical data
df["grade"] = df["raw_grade"].astype("category")
df["grade"].cat.categories = ["very good", "good", "very bad"]
df["grade"] = df["grade"].cat.set_categories(["very bad", "bad", "medium", "good", "very good"])
df.sort_values(by="grade") # 按 category 顺序
df.groupby("grade").size()

# time
df['time'] = df['time'].astype(int)
df['time'] = df.to_datetime(df['time'], unit='s')
df['timestamp'] = pd.to_datetime(df["timestamp"],format='%Y-%m-%d %H:%M:%S',errors='ignore')

df.loc[df['url'].str.startswith('/data')].groupby('url').count().to_csv('url.csv')

# timezone
ts.tz_localize('UTC')
ts_utc.tz_convert('US/Eastern')
pd.to_datetime(df['a'], unit='ms').dt.tz_localize('utc')

# weekday
df.index.weekday

# unique
df['userid'].unique()

# true => False
~df['userid']
```
