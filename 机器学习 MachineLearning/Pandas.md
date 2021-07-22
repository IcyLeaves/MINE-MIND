# Pandas

*summary*

**Pandas**是一个读取数据文件的模块

---

*2021.07.02*

### 读取csv文件

> [pandas读写CSV操作 - John-刘约翰 - 博客园 (cnblogs.com)](https://www.cnblogs.com/shenxi-ricardo/p/12123845.html)

```python
df = pd.read_csv('data.csv')

df = pd.read_csv('data.csv',header=None) #csv的第一行没有列标题时

labels = ['id', 'name']
df = pd.read_csv('data.csv', names=labels) #自定义列标题
```

### 分析某列数据的分布

> [Pandas怎样对表格某列值进行查看并计数value_counts() - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/101038866)
>
> [Pandas Series: sort_index() function - w3resource](https://www.w3resource.com/pandas/series/series-sort_index.php)

```python
import matplotlib

# ...
num_counts=df['num'].value_counts().sort_index() # 对num列进行计数，并按key进行排序
num_counts.plot(kind='bar',figsize=(10,5)) # 绘制成柱状图 
```

