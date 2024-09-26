# Electronic Sales Analytic using Jupiter Nootebook

## Library used
<details>
  <ul>
    <li><a href="https://pandas.pydata.org/">Pandas</a></li>
    <li><a href="https://matplotlib.org/">matplotlib</a></li>
    <li><a href="https://plotly.com/">plotly</a></li>
  </ul>
</details>

## Data Eploratory

<!-- Import data -->
### Importing data using Pandas

```bash
 df = pd.read_csv('Electronic_sales.csv')
 df = df.drop(columns=df.columns[[13, 14, 15]])
 df
```

<img src="https://github.com/user-attachments/assets/370f3193-2424-4164-808d-1d16d7472493">

## Data Cleaning

<!-- Checking data types -->
### Checking data types

```bash
 df['Purchase Date'] = pd.to_datetime(df['Purchase Date'])
```

<!-- Checking empty/null values -->
### Checking empty/null values

```bash
 df[df['Gender'].isnull()]
```

<img src="https://github.com/user-attachments/assets/f4fa7493-c783-4124-8032-efbbb79e498c">

<div align="right">
<small><i>1 customer has NaN gender, so erase the row</i></small>
</div>


<!-- Deleting empty/null row -->
### Deleting empty/null row

```bash
 df = df.dropna()
```

<!-- Checking duplicates -->
### Checking duplicates

```bash
 df[df.duplicated()]
```

<!-- Age grouping -->
### Age grouping

```bash
 bins = [0, 2, 39, 59, 99]
 labels = ['Baby', 'Young Adults', 'Midle-aged Adults', 'Old Adults']
 df['Age Group'] = pd.cut(df['Age'], bins=bins, labels=labels, include_lowest=True)
```

<img src="https://github.com/user-attachments/assets/da6ed717-4a2d-4eb7-bd98-1907d9d82b8d">

## Data Visualization

### Customer Identification

Identify who buying the products

<!-- Customer by gender -->
#### Customer by gender

```bash
 gender_counts = df.groupby('Gender')['Customer ID'].nunique()
 fig_gender = px.pie(gender_counts, values=gender_counts, names=gender_counts.index, title='Customer by Gender')
 fig_gender.show()
```

<img src="https://github.com/user-attachments/assets/e6c5638d-dd52-411e-8e98-61a68a3481f3">

<blockquote>The data provides insights into the customer distribution by gender and loyalty membership. Of the total customers, 5,938 are female, while 6,197 are male, showing a nearly balanced gender representation with a slight male majority.</blockquote>

<!-- Customer by loyalty member -->
#### Customer by loyalty member

Since the data shows that a customer has transactions both as a member and as a non-member.

```bash
 member_variation = df.groupby('Customer ID')['Loyalty Member'].nunique()
 print(member_variation[member_variation > 1].value_counts())
```

<img src="https://github.com/user-attachments/assets/23908af2-261e-4b68-a52f-054a17b16a1c">

I assume the customer currently holds a membership.

```bash
 loyalty_status = df.groupby('Customer ID')['Loyalty Member'].agg(
     lambda x: 'Yes' if 'Yes' in x.values else 'No'
 ).reset_index()

 member_counts = loyalty_status.groupby('Loyalty Member')['Customer ID'].nunique()
 fig_member = px.pie(member_counts, values=member_counts, names=member_counts.index, title='Customer by Loyalty Member')
 fig_member.show()
```

<img src="https://github.com/user-attachments/assets/2be7d339-54fb-4539-b7bf-4e768aba9725">

<blockquote>The data reveals that a significant majority of customers are not loyalty program members. Out of the total customers, 8,302 do not have a loyalty membership, while 3,833 are enrolled in the loyalty program. This indicates that approximately 68% of the customer base is not utilizing the loyalty membership, suggesting an opportunity to increase engagement with the program.</blockquote>

<!-- Customer by age group -->
#### Customer by age group

```bash
 age_group_counts = df.groupby('Age Group')['Customer ID'].nunique()

 fig_age_group = px.pie(age_group_counts, values=age_group_counts, names=age_group_counts.index, title='Customer by Age Group')
 fig_age_group.show()
 age_group_counts
```

<img src="https://github.com/user-attachments/assets/760a6a4f-fdb9-4489-a1a6-131c88701b98">

<blockquote>This insight highlights that the majority of the customer base consists of adults, with a fairly even distribution among younger, middle-aged, and older adults. This suggests that marketing strategies should be tailored to adult audiences, focusing on the specific needs and preferences of each age segment.</blockquote>

<!-- Customer by loyalty member, gender, and age group-->
#### Customer by loyalty member, gender, and age group

```bash
 grouped = df.groupby(['Customer ID', 'Gender', 'Age Group'])['Customer ID'].nunique().reset_index(name="Customer Count")
 grouped['Loyalty Member Status'] = grouped['Customer ID'].map(loyalty_status.set_index('Customer ID')['Loyalty Member'])
 final_grouped = grouped.groupby(['Loyalty Member Status', 'Gender', 'Age Group'])['Customer Count'].sum().reset_index()

 fig = px.bar(final_grouped, x='Loyalty Member Status', y='Customer Count', color='Gender', barmode="group", facet_col="Age Group",
              title='Customer by Loyalty Member, Gender, and Age Group')
 fig.show()
```

<img src="https://github.com/user-attachments/assets/dbb8ca92-bb4c-462f-b5c0-cf18af36815c">

<blockquote>There is an opportunity to increase loyalty membership across all age groups, especially among Young Adults and Middle-aged Adults where non-membership is dominant. Males consistently show higher counts than females in both loyalty statuses, particularly among non-members. Engagement with loyalty programs seems to be lower in Young Adults, which could indicate a need for targeted campaigns to encourage membership in this age group.</blockquote>

<!-- Top 3 customer by amount of purchase-->
#### Top 3 customer by amount of purchase

```bash
 top_3_cust_by_amount_purchase = df.groupby('Customer ID')['Total Price'].sum().nlargest(3).reset_index(name='Total Amount')
 top_3_cust_by_amount_purchase['Customer ID'] = top_3_cust_by_amount_purchase['Customer ID'].astype(str)

 fig = px.bar(top_3_cust_by_amount_purchase, x='Customer ID', y='Total Amount',
              title='Top 3 Customer by Amount of Purchase')
 fig.show()
 top_3_cust_by_amount_purchase
```

<img src="https://github.com/user-attachments/assets/1203b918-63f8-43ce-8e24-aff435bc9bb3">


### Purchasing Performance


<!-- Purchasing quantity by gender over time-->
#### Purchasing quantity by gender over time

```bash
 grouped = df.groupby(['Purchase Date', 'Gender'])['Quantity'].sum().reset_index()

 fig = px.line(grouped, x='Purchase Date', y='Quantity', color='Gender', title='Purchasing Quantity by Gender Over Time')
 fig.show()
```

<img src="https://github.com/user-attachments/assets/86d394a6-ed3c-4c17-884f-307bae4c71d1">

<!-- Purchasing quantity by loyalty member over time-->
#### Purchasing quantity by loyalty member over time

```bash
 grouped = df.groupby(['Purchase Date', 'Loyalty Member'])['Quantity'].sum().reset_index()

 fig = px.line(grouped, x='Purchase Date', y='Quantity', color='Loyalty Member', title='Purchasing Quantity by Loyalty Member Over Time')
 fig.show()
```

<img src="https://github.com/user-attachments/assets/7f8a56b1-7fd2-428b-9fa6-3af3fc07e60d">

<blockquote>The data shows that non-members have far higher and more variable purchasing quantities compared to loyalty members. This may highlight the need to improve loyalty program incentives to encourage more frequent or larger purchases from members. Focusing on engaging loyalty members during peak purchasing seasons could help balance this disparity and maximize the potential of the loyalty program.</blockquote>

<!-- Sales by product type-->
#### Sales by product type

```bash
 product_type_chart = df.groupby('Product Type')['Quantity'].sum().reset_index()
 product_type_chart['Percentage'] = (product_type_chart['Quantity'] / product_type_chart['Quantity'].sum()) * 100
 product_type_chart = product_type_chart.sort_values(by='Percentage', ascending=False)

 fig = px.bar(product_type_chart, x='Product Type', y='Quantity', color='Product Type', title='Quantity by Product Type', text=product_type_chart['Percentage'].round(2).astype(str) + '%')
 fig.show()
 product_type_chart
```

<img src="https://github.com/user-attachments/assets/c799a0c0-2116-4108-81a9-3eeffa14ba1a">

<!-- Total sales of product type on each year-->
#### Total sales of product type on each year

```bash
 df['Purchase Year'] = df['Purchase Date'].dt.year
 product_type_by_year_chart = df.groupby(['Purchase Year', 'Product Type'])['Quantity'].sum().reset_index()
 product_type_by_year_chart = product_type_by_year_chart.sort_values(by='Quantity', ascending=False)

 fig = px.bar(product_type_by_year_chart, x='Purchase Year', y='Quantity', color='Product Type', barmode="group",
             title='Total Sales of Product Type on Each Year')
 fig.show()
 product_type_by_year_chart
```

<img src="https://github.com/user-attachments/assets/98d666c1-c4ae-4ec3-89c2-2767036883ed">

<blockquote>
<ul>
  <li><strong>Trend Analysis</strong>: Smartphones are consistently popular, showing a strong upward trend in sales.</li>
  <li><strong>Market Stability</strong>: Tablets have stable sales, indicating a steady market demand.</li>
  <li><strong>Product Performance</strong>: Laptops had a peak in 2021, suggesting a possible market shift or a successful product launch that year.</li>
</ul>
</blockquote>

<!-- Top 3 SKU purchased-->
#### Top 3 SKU purchased

```bash
 order_complete = df[df['Order Status'] == 'Completed']
 sku_purchase_chart = order_complete.groupby(['Product Type', 'SKU'])['Quantity'].sum().reset_index(name='Total Purchased')
 sku_purchase_chart = sku_purchase_chart.sort_values(by='Total Purchased', ascending=False)
 top_3_sku = sku_purchase_chart.head(3)

 fig = px.bar(top_3_sku, x='SKU', y='Total Purchased', color='Product Type', title='Top 3 SKU Purchased')
 fig.show()
 top_3_sku
```

<img src="https://github.com/user-attachments/assets/962277e6-fe56-4d55-8033-e26965c93621">

<!-- Low 3 SKU purchased-->
#### Low 3 SKU purchased

```bash
 low_3_sku = sku_purchase_chart.tail(3)

 fig = px.bar(low_3_sku, x='SKU', y='Total Purchased', color='Product Type', title='Low 3 SKU Purchased')
 fig.show()
 low_3_sku
```

<img src="https://github.com/user-attachments/assets/87a1e7a2-bd79-456d-8501-3b4e2d5bbe01">

<!-- Purchasing by loyalty member and payment method-->
#### Purchasing by loyalty member and payment method

```bash
 payment_method_chart = df.groupby(['Loyalty Member', 'Payment Method'])['Total Price'].sum().reset_index()
 payment_method_chart = payment_method_chart.sort_values(by='Total Price', ascending=False)

 fig = px.bar(payment_method_chart, x='Loyalty Member', y='Total Price', color='Payment Method', barmode="group", title='Purchasing by Loyalty Member and Payment Method')
 fig.show()
 payment_method_chart
```

<img src="https://github.com/user-attachments/assets/943f277f-4e8e-4de0-a639-1a52c02fb124">

<blockquote>
  <ul>
    <li>Credit Card Dominance: Credit cards are the most popular payment method among loyalty members, with the highest purchasing amount.</li>
    <li>Bank Transfer and PayPal: Both methods show significant usage, but they lag behind credit cards.</li>
    <li>Cash Usage: Cash is the least preferred payment method among loyalty members.</li>
    <li>Loyalty Member Impact: Loyalty members tend to use credit cards more frequently, indicating a preference for convenience and possibly rewards associated with credit card usage.</li>
  </ul>
</blockquote>

### Services Performance

<!-- Order status: canceled vs completed-->
#### Order status: canceled vs completed

```bash
 order_status = df['Order Status'].value_counts()

 fig_order_status = px.pie(order_status, values=order_status, names=order_status.index, title='Order Status: Canceled vs Completed')
 fig_order_status.show()
 order_status
```

<img src="https://github.com/user-attachments/assets/0511f8b2-dd7c-4459-919f-eec9b0a57d11">

<blockquote>
  <ul>
    <li><strong>Order Completion Rate</strong>: The majority of orders, about <strong>61.2%</strong>, are completed successfully. This indicates a relatively high efficiency in fulfilling orders.</li>
    <li><strong>Order Cancellation Rate</strong>: Approximately 38.8% of orders are cancelled. This is a significant portion and suggests there might be areas for improvement in the ordering process or customer satisfaction.</li>
    <li><strong>Volume of Orders</strong>: With 1841 completed and 1158 cancelled orders, understanding the reasons behind cancellations could help in reducing this rate and improving overall performance.</li>
    <li><strong>Actionable Steps</strong>: To reduce cancellations, consider enhancing the checkout process, improving product descriptions, and offering better customer support</li>
  </ul>
</blockquote>
<!-- Rating over time-->
#### Rating over time

```bash
 dates = pd.DataFrame({'Purchase Date': df['Purchase Date'].unique()})
 rating = pd.DataFrame({'Rating': df['Rating'].unique()})
 join = dates.assign(key=1).merge(rating.assign(key=1), on='key').drop('key', axis=1)
 grouped_ratings = df.groupby(['Purchase Date', 'Rating']).size().reset_index(name="Total")
 final_result = join.merge(grouped_ratings, on=['Purchase Date', 'Rating'], how='left')
 final_result['Total'] = final_result['Total'].fillna(0)
 final_result = final_result.sort_values(by=['Purchase Date', 'Rating'])

 fig = px.line(final_result, x='Purchase Date', y='Total', color='Rating', title='Rating Over Time')
 fig.show()
```

<img src="https://github.com/user-attachments/assets/efae4f46-5a06-43df-9930-7106b3cb623a">

<!-- Rating distribution of each year-->
#### Rating distribution of each year

```bash
 rating_counts = df.groupby(['Rating', 'Purchase Year'])['Rating'].value_counts().reset_index(name='Rating Count')
 rating_counts = rating_counts.sort_values(by=['Rating','Purchase Year'])

 fig_rating = px.histogram(rating_counts, y='Rating Count', x='Purchase Year', color='Rating', barmode='group', title='Rating Distribution of Each Year')
 fig_rating.show()
 rating_counts
```

<img src="https://github.com/user-attachments/assets/fb249f00-ed70-410e-bc2e-9ff20648ce2e">

<blockquote>
  <ul>
    <li>Trend Analysis: The number of purchases with higher ratings (4 and 5) appears to increase over the years, indicating improving customer satisfaction.</li>
    <li>Yearly Comparison: 2023 had a significant number of purchases with lower ratings (1 and 2), while 2024 and 2025 show a shift towards higher ratings.</li>
  </ul>
</blockquote>

<!-- Rating distribution by loyalty member-->
#### Rating distribution by loyalty member

```bash
 rating_counts = df.groupby('Loyalty Member')['Rating'].value_counts()
 import plotly.graph_objects as go
 from plotly.subplots import make_subplots

 labels = rating_counts.index.get_level_values(1).unique()
 values = [rating_counts.loc['Yes'].values,
           rating_counts.loc['No'].values]

 fig = make_subplots(rows=1, cols=2, specs=[[{'type':'domain'}, {'type':'domain'}]])
 fig.add_trace(go.Pie(labels=labels, values=values[0], name="Loyalty Member"), 1, 1)
 fig.add_trace(go.Pie(labels=labels, values=values[1], name="No Loyalty Member"), 1, 2)

 fig.update_traces(hole=.4 ,hoverinfo="label+value+percent+name")
 fig.update_layout(title_text="Rating Distribution by Loyalty Member",
                   annotations=[dict(text='Loyalty Member', x=sum(fig.get_subplot(1, 1).x) / 2, y=0.5,
                       font_size=20, showarrow=False, xanchor="center"),
                  dict(text='No Loyalty Member', x=sum(fig.get_subplot(1, 2).x) / 2, y=0.5,
                       font_size=20, showarrow=False, xanchor="center")])
 fig.show()
```

<img src="https://github.com/user-attachments/assets/58804e79-a0ea-4b35-84df-5a94af84e302">

<blockquote>Loyalty members tend to be more satisfied compared to non-loyalty members. This is evident from the higher percentage of loyalty members giving a rating of 5, while non-loyalty members have a higher percentage of lower ratings (1 and 2). This suggests that loyalty programs might be effective in enhancing customer satisfaction.</blockquote>
