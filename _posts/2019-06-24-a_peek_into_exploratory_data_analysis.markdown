---
layout: post
title:      "A Peek into Exploratory Data Analysis"
date:       2019-06-24 12:26:39 -0400
permalink:  a_peek_into_exploratory_data_analysis
---


## A Peek into Exploratory Data Analysis

Picture this:  The year is 1970 and you’ve just turned 18. It’s your first Summer as an adult, and you’re ready to explore the world, finally allowed to take that road trip with your best friends. The morning of the trip comes, you wake up, pick up your friends, and spiritedly make your way toward your destination. In your excitement, reveling in your newfound freedom, you speed along the road with the windows down.  Hours later, you realize that no one brought a map and (since cell phones don’t exist) you pull into a gas station for directions. There are no maps but the clerk is kind enough to attempt to attempt to draw one. You head on your way, meandering down the road, cursing your makeshift map and the damn clerk that drew it, too.

Oddly enough, this fictitious coming of age story has parallels to the life of a fledgling Data Scientist.  The field of Data Science is perhaps as broad as the nature of data itself.  There are countless “tools” to add to your proverbial belt in the form of new libraries, languages, and SO. MUCH. MATH.  It’s easy to get lost in the latest trends, imagining all the things that you’ll do with data you don’t yet have or understand. Walking in on the first day of your new job, you’ll have already ideated on models you’ll build, correlations you’ll explore, and all of the glorious process automation you’ll implement.  On that day, if you’re very lucky, you’ll be presented with an updated ERD that accurately documents the data you need, all formatted perfectly. 

From personal experience working in startup environments, reality often paints a picture more akin to that of the eager young adult, eager to explore the world but encumbered by a lack of direction. In order to be effective in our role, we must take the time to become familiar with the both the dataset and the business we're working with itself.  We might ask ourselves:

> Do we understand the focus of the business we’re entering? The value-proposition of its services? The pain-point it seeks to address?  Do we know what data we need? Do we have that data? Where is it stored? How is it formatted? Is there additional data that we should be collecting? Are the conclusions we’re drawing statistically significant? 

Recently, I found myself in a similar position with Microsoft’s Northwind dataset. My task was simple: explore the dataset, learn about Northwind’s business, identify meaningful lines of inquiry, and evaluate them to determine whether or not any trends we observe are significant.  I would then be tasked with presenting my hypothetical business recommendations to the company’s Sales Team. 

To begin, I was presented with a link to a SQLite database and the following ERD (Entity-Relationship Diagram) mapping the schema of the data.	

Great! I quickly imported the standard libraries I knew I’d need to peruse the data and wrote my first SQL query, guided by the ERD above. If you’ve read this far, you shouldn’t be surprised that the output wasn’t what I expected.

To begin, none of the table names in the ERD exist in the dataset. Using the ERD loosely, I began the process of mapping the database by writing a function to  use SQLAlchemy’s inspect module to iterate over each table in the database, outputting the information I needed to create functional SQL queries.

```python
def get_column_info(table):
    cols_list = inspector.get_columns(table)
    print('')
    print('------------------------------------------------------------------------')
    print(f'Table Name: {table}, Primary Key: {inspector.get_primary_keys(table)}')
    print('------------------------------------------------------------------------')
    print('')
    for column in cols_list:
        print(f"Column Name: {column['name']}, Column Type: {column['type']}")
```

Sample Output:
```
------------------------------------------------------------------------
Table Name: Employee, Primary Key: ['Id']
------------------------------------------------------------------------

Column Name: Id, Column Type: INTEGER
Column Name: LastName, Column Type: VARCHAR(8000)
Column Name: FirstName, Column Type: VARCHAR(8000)
Column Name: Title, Column Type: VARCHAR(8000)
Column Name: TitleOfCourtesy, Column Type: VARCHAR(8000)
Column Name: BirthDate, Column Type: VARCHAR(8000)
Column Name: HireDate, Column Type: VARCHAR(8000)
Column Name: Address, Column Type: VARCHAR(8000)
Column Name: City, Column Type: VARCHAR(8000)
Column Name: Region, Column Type: VARCHAR(8000)
Column Name: PostalCode, Column Type: VARCHAR(8000)
Column Name: Country, Column Type: VARCHAR(8000)
Column Name: HomePhone, Column Type: VARCHAR(8000)
Column Name: Extension, Column Type: VARCHAR(8000)
Column Name: Photo, Column Type: BLOB
Column Name: Notes, Column Type: VARCHAR(8000)
Column Name: ReportsTo, Column Type: INTEGER
Column Name: PhotoPath, Column Type: VARCHAR(8000)
None
```

Having printed the database schema, it’s tempting to dive right into writing targeted SQL queries and hypothesis testing. After all, every company would want to know which employee has generated the most revenue over time, right? As an example, you might decide to calculate this metric by diving the sum of each employee’s total sales revenue by the difference in business days between the last date in the dataset and each employee’s start date. If you did, you might also be left scratching your head. To illustrate the importance of data scrubbing and exploration, let’s take a look at what might cause issues with our example calculation above.

We’ll begin by writing a simple SQL query to select all data from the “Employee” table as a DataFrame object using Pandas and SQLalchemy.

```python
df_employee = pd.read_sql_query("SELECT * FROM Employee", engine)
```

After previewing the DataFrame header, we can group our results by grouping using Pandas or SQL . For this first example, we’ll use Pandas to group our data by employee name.

```python
df_employee_names = df_employee.groupby(['FirstName', 'LastName'])['FirstName', 'LastName', 'Region', 'Country', 'City', 'HireDate']
df_employee_names.head(10)
```

```
 FirstName	LastName	Region	Country	City	HireDate
0	Nancy	Davolio	North America	USA	Seattle	2024-05-01
1	Andrew	Fuller	North America	USA	Tacoma	2024-08-14
2	Janet	Leverling	North America	USA	Kirkland	2024-04-01
3	Margaret	Peacock	North America	USA	Redmond	2025-05-03
4	Steven	Buchanan	British Isles	UK	London	2025-10-17
5	Michael	Suyama	British Isles	UK	London	2025-10-17
6	Robert	King	British Isles	UK	London	2026-01-02
7	Laura	Callahan	North America	USA	Seattle	2026-03-05
8	Anne	Dodsworth	British Isles	UK	London	2026-11-15
```

After previewing the data, the problem with our methodology to measure employee revenue generation over time becomes obvious. Unless Northwind is in the time-travel business, it isn’t possible for all of its employees to have been hired 10+ years after the last recorded sale in the dataset. By taking the time to become familiar with the dataset, we aren’t likely to waste our time attempting the calculation above, instead choosing a different methodology to quantify and compare employee sales performance.

After getting to know the rest of the dataset, let’s pose an example line of inquiry and document the steps we’d take to evaluate its results and significance. Through exploratory data analysis, we can determine that Northwind carries 77 unique products selling each at various discount levels, presumably to encourage an increase in customer spend.  To determine whether the efficacy of this strategy, one question we might ask is: 

> “Do discounts have a statistically significant effect on customer spend per order?”  

We’d likely seek to follow up this inquiry with another, such as: 

> “If so, at what level(s) of discount?”. 

For this post, let’s stick to the first question and walk through the process we’ll use to evaluate it. To begin, we’ll state our null (control) and alternative (a difference in populations we believe may be significant) hypotheses, as well as the alpha value we’ll use to evaluate the likelihood that the results we obtain are not due to chance. 
*For an explanation/example of structuring hypothesis tests, check out [this article](https://machinelearningmastery.com/statistical-hypothesis-tests/) for more info.* 

```
* $H_{0}:$ Discounts do not have a statistically significant effect customer spend.
<br>
* $H_{a}:$ Discounts do have a statistically significant effect (whether positive or negative) on customer spend.
<br>
* $\alpha$ = 0.05
```

First, let’s select and review the data we’ll need using SQL:
```py
df_discount_spend = pd.read_sql_query("""SELECT o.OrderId, o.UnitPrice, o.Discount, o.Quantity, p.QuantityPerUnit \
                    FROM [OrderDetail] o \
                    INNER JOIN Product p on o.ProductId = p.ID
                    GROUP BY o.OrderId;""", engine)
df_discount_spend.head(3)
```

```
	OrderId	UnitPrice	Discount	Quantity
0	10248	34.8	0.00	5
1	10249	42.4	0.00	40
2	10250	16.8	0.15	15
```

Great! We’ve successfully selected the data we’ll need.  In order to determine customer spend per order, we will need to create a new column. This time, we’ll use SQL to manipulate our data, creating a new column containing the total order price, accounting for discount and quantity:

```
df_discount_spend = pd.read_sql_query("""SELECT od.OrderId, od.UnitPrice, od.Discount, od.Quantity, \
                    od.Quantity * od.UnitPrice * (1-od.Discount) OrderPrice \
                    FROM [OrderDetail] od \
                    INNER JOIN Product p on od.ProductId = p.ID
                    GROUP BY od.OrderId;""", engine)
```

```
	OrderId	UnitPrice	Discount	Quantity	OrderPrice
0	10248	34.8	0.00	5	174.0
1	10249	42.4	0.00	40	1696.0
2	10250	16.8	0.15	15	214.2
```

By previewing the newly created DataFrame, we can see the new “OrderPrice” column we created and verify its output. Next, we’ll segment our dataset by evaluating each order using Discount as a boolean. In other words, we’ll group our data into orders that have a discount and orders that don’t, then visualize them.

```
# Creating a new column to identify presence of Discount/no Discount:
df_discount_spend['DiscBool'] = df_discount_spend['Discount'].apply(lambda x: 0 if x == 0 else 1)
df_discount_spend.head(3)
```

Let’s first take a bird’s eye view of our results:

![img](https://i.imgur.com/2s7ZN3d.png)

Based on the visualization above, we can determine that Northwind generates more total revenue from sales of non-discounted items. However, the average order cost for orders containing discounts is greater than those without discounts. To determine whether this difference is statistically significant, we should first review the distribution of Order Price for orders with and without discount(s).

To do that, we’ll create 2 new DataFrames using SQL to segment the data we selected above by the presence of discount.

```
# Create DataFrame where value for Discount > 0:
query1 = 'Discount > 0'
df_discount_spend_disc = df_discount_spend.query(query1)

# Create DataFrame where value for Discount == 0:
query2 = 'Discount == 0'
df_discount_spend_nodisc = df_discount_spend.query(query2)
```

Now, we’re able to more easily compare and visualize the distributions of each group:

![img](https://i.imgur.com/Z08djtO.png)

While we can observe some difference in the distribution of the data in the visualization above, there are still a few steps remaining before we can make any assertions.  Specifically, we need to determine which statistical method should be used to test our hypothesis.  Since we want to know if there is any significant difference between independent variables, whether positive or negative, we would like to use an Independent two-tailed t-test.  Before we’re able to do that, we should review the assumptions required for the test and determine whether our sample populations meet these assumptions. If they don’t, we’ll need to select a different test to use.

An Independent two-tailed t-test assumes:
* Variables are independent
* Distributions are normal
* Homogeneity in variance

Since we know our variables are independent, let’s take a look at the distribution of spend for each sample population.

![img](https://i.imgur.com/IytRQJ5.png)

![img](https://i.imgur.com/HVdB8lm.png)

Based on the figures above, it’s obvious that both distributions are positively skewed. Lucky for us, we still have a few tricks we can use to normalize the distributions. Let’s take re-review sample distributions after log-scaling order revenue using Numpy.

```python
# Log-scaling Order Price:
log_order_price = np.log(df_log_discount_spend.OrderPrice)
df_log_discount_spend['LogOrderPrice'] = log_order_price
```

![img](https://i.imgur.com/uTebo47.png)

![img](https://i.imgur.com/14jbJbK.png)

Slam dunk! Finally, we can evaluate our samples to test for homogeneity in variance. To achieve this goal, let’s divide the variance of one sample by the variance of the other. Values nearing 1 represent increasingly homogeneity in sample variance. Using Numpy, we can quickly perform this calculation.

```python
np.var(a)/np.var(b)
```
```
1.08
```

With confirmation that the variance between samples is very similar, we’ve finally confirmed that our samples meet the assumptions required by the independent two-tailed t-test.

```python
# Using a two-tailed t-test to compare distributions above:
stats.ttest_ind(df_log_discount_spend_disc.LogOrderPrice, df_log_discount_spend_nodisc.LogOrderPrice)
```

The resulting p-value of our two-tailed t-test is 0.07.  Because p > 0.05 (alpha) we fail to reject the null hypothesis that states that discount (as a boolean) does not have a statistically significant effect on customer spend.  Knowing that the presence of a discount does not cause a statistically significant difference in customer order spend is valuable information, and might lead us to pursue additional inquiries into the effect of discounts on customer spend, such as whether discounts of varying levels (not just treated as a boolean) are correlated with a change in customer spending trends.

After all that work, we might tend to feel discouraged that we weren’t able to reject the null hypothesis, but we shouldn’t!  Data has no agenda, and neither should we.  As Data Scientists, it’s important to remember that we serve as a conduit for the data to tell its own story, not the other way around.  Armed with a better ‘map’ and knowledge of the ‘lay of the land’ of the Northwind dataset, we’re ready to continue to share the stories and secrets it holds.

