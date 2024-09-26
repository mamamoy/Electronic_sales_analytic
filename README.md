<h1>Electronic Sales Analytic using Jupiter Nootebook</h1>

<h3>Library used:<h3>
<details>
  <ul>
    <li><a href="https://pandas.pydata.org/">Pandas</a></li>
    <li><a href="https://matplotlib.org/">matplotlib</a></li>
    <li><a href="https://plotly.com/">plotly</a></li>
  </ul>
</details>

<h2>Data</h2>
<img src="https://github.com/user-attachments/assets/370f3193-2424-4164-808d-1d16d7472493">

<h3>Data Cleaning</h3>
<li>Changing the data types</li>
```bash
  df['Purchase Date'] = pd.to_datetime(df['Purchase Date'])
```

<li>Checking empty/null value</li>
<code>df[df['Gender'].isnull()]</code>
<img src="https://github.com/user-attachments/assets/f4fa7493-c783-4124-8032-efbbb79e498c">
<i>1 customer has NaN gender, so erase the row</i>
<code>df = df.dropna()</code>
<li>Checking duplicates</li>
<code>df[df.duplicated()]</code>
<li>Age grouping</li>
<code>bins = [0, 2, 39, 59, 99]
labels = ['Baby', 'Young Adults', 'Midle-aged Adults', 'Old Adults']
df['Age Group'] = pd.cut(df['Age'], bins=bins, labels=labels, include_lowest=True)</code>
