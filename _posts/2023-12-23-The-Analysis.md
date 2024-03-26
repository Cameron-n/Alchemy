---
title: "The Analysis"
layout: post
---

## Introduction (work in progress)

Welcome back. If you haven't come from my previous post, this follows on directly from there.

Today, we're going to look at some transformations to get the data we really want, followed by the actual analysis.

Previously, the actual goal we want to accomplish was brushed over. What we want is to contruct tables of all possible potions, and then draw any interesting conclusions from them.
What are potions, you ask? They are a combination of ingredients (from our data) following these rules:
* Each Ingredient has up to four effects. 
* A potion has an effect if two (or more) ingredients share the same effect.
* The total number of ingredients in a potion is limited to four.
* Each ingredient can only be used once per potion.

So, let's get started!





---
## Transformations SQL

What we want is all possible potion combinations.

So, using our 'Ingredients_final' table, we'll create three new tables. One for potions with two ingredients, one for three, and one for four (spoiler, we are actually going to create four tables).

First, let's SELECT all the columns we want. That's: the ingredients, and the effects. We'll be creating the table by joining the 'ingredients_final' table with itself, i.e. linking rows from two tables together (in this case the same table, a 'self join'). The first part of the code is as follows:

```sql
select t1.ingredients as t1ing, t2.ingredients as t2ing,
t1."effect 1" as t1e1, t1."effect 2" as t1e2, t1."effect 3" as t1e3, t1."effect 4" as t1e4,
t2."effect 1" as t2e1, t2."effect 2" as t2e2, t2."effect 3" as t2e3, t2."effect 4" as t2e4
from  ingredients_final t1, ingredients_final t2
```

This gives us all possible combinations, but we want to impose the rules stated earlier. If we had three ingredients: A, B, and C, we want AB, AC, and BC only. You might be able to see this can be achieved my sorting the ingredients alphabetically. We also want to make sure the ingredients share at least one effect (and to exclude 'blank' cells as counting as effects). The second part of the code is as follows and together creates the full code:

```sql
where t1.ingredients < t2.ingredients
and (
   t1."effect 1" in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") and t1."effect 1" != ''
or t1."effect 2" in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") and t1."effect 2" != ''
or t1."effect 3" in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") and t1."effect 3" != ''
or t1."effect 4" in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") and t1."effect 4" != ''
) 
```

Thats pairs of ingredients out the way, but what about triplets? We can actually use our results for potion pairs to create triplets by just adding on all valid third ingredients.
We'll need to SELECT the correct columns for three ingredients, and join a copy of 'ingredients_final' to our potion pairs:

```sql
select t1ing, t2ing, t3.ingredients as t3ing,
t1e1, t1e2, t1e3, t1e4, t2e1, t2e2, t2e3, t2e4,
t3."effect 1" as t3e1, t3."effect 2" as t3e2, t3."effect 3" as t3e3, t3."effect 4" as t3e4
from --uses potion_pairs to generate potions with 3 ingredients.
(
select t1.ingredients as t1ing,t2.ingredients as t2ing,
t1."effect 1" as t1e1, t1."effect 2" as t1e2, t1."effect 3" as t1e3, t1."effect 4" as t1e4,
t2."effect 1" as t2e1, t2."effect 2" as t2e2, t2."effect 3" as t2e3, t2."effect 4" as t2e4
from ingredients_final t1, ingredients_final t2
where t1.ingredients < t2.ingredients
and (
   t1."effect 1" in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") and t1."effect 1" != ''
or t1."effect 2" in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") and t1."effect 2" != ''
or t1."effect 3" in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") and t1."effect 3" != ''
or t1."effect 4" in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") and t1."effect 4" != ''
)
) t12, ingredients_final t3
```

As you can see, most of the above is just our first script. We then need to enforce the rules like before:

```sql
where t1ing < t3.ingredients and t2ing < t3.ingredients --stops duplicates/single ingredient potions.
and ( --Third ingredient must add at least 1 new effect.
   t1e1 in (t3."effect 1",t3."effect 2",t3."effect 3",t3."effect 4") and t1e1 not in (t2e1,t2e2,t2e3,t2e4) and t1e1 != ''
or t1e2 in (t3."effect 1",t3."effect 2",t3."effect 3",t3."effect 4") and t1e2 not in (t2e1,t2e2,t2e3,t2e4) and t1e2 != ''
or t1e3 in (t3."effect 1",t3."effect 2",t3."effect 3",t3."effect 4") and t1e3 not in (t2e1,t2e2,t2e3,t2e4) and t1e3 != ''
or t1e4 in (t3."effect 1",t3."effect 2",t3."effect 3",t3."effect 4") and t1e4 not in (t2e1,t2e2,t2e3,t2e4) and t1e4 != ''
or t2e1 in (t3."effect 1",t3."effect 2",t3."effect 3",t3."effect 4") and t2e1 not in (t1e1,t1e2,t1e3,t1e4) and t2e1 != ''
or t2e2 in (t3."effect 1",t3."effect 2",t3."effect 3",t3."effect 4") and t2e2 not in (t1e1,t1e2,t1e3,t1e4) and t2e2 != ''
or t2e3 in (t3."effect 1",t3."effect 2",t3."effect 3",t3."effect 4") and t2e3 not in (t1e1,t1e2,t1e3,t1e4) and t2e3 != ''
or t2e4 in (t3."effect 1",t3."effect 2",t3."effect 3",t3."effect 4") and t2e4 not in (t1e1,t1e2,t1e3,t1e4) and t2e4 != ''
)
and not(
(t1e1 = t3."effect 1" and t1e2 = t3."effect 2" and t1e3 = t3."effect 3" and t1e4 = t3."effect 4") or 
(t2e1 = t3."effect 1" and t2e2 = t3."effect 2" and t2e3 = t3."effect 3" and t2e4 = t3."effect 4")
)
```

The only addition is the 'AND NOT' code at the end. This is to exclude the case where one of the first two ingredients shares all 4 effects with the third, removing the need for the last ingredient and hence not being a triplet.

We can also adapt the above code for four ingredients, though we run into a slight problem:

```sql
select t1ing, t2ing, t3ing, t4.ingredients as t4ing, -- missing disjoint potion pairs.
t1e1, t1e2, t1e3, t1e4, t2e1, t2e2, t2e3, t2e4, t3e1, t3e2, t3e3, t3e4,
t4."effect 1" as t4e1, t4."effect 2" as t4e2, t4."effect 3" as t4e3, t4."effect 4" as t4e4
from --uses potion_triples to generate potions with 4 ingredients, not including 2 disjoint pairs.
(
select t1ing, t2ing, t1e1, t1e2, t1e3, t1e4, t2e1, t2e2, t2e3, t2e4,
t3.ingredients as t3ing,
t3."effect 1" as t3e1, t3."effect 2" as t3e2, t3."effect 3" as t3e3, t3."effect 4" as t3e4
from (
select t1.ingredients as t1ing,t2.ingredients as t2ing,
t1."effect 1" as t1e1, t1."effect 2" as t1e2, t1."effect 3" as t1e3, t1."effect 4" as t1e4,
t2."effect 1" as t2e1, t2."effect 2" as t2e2, t2."effect 3" as t2e3, t2."effect 4" as t2e4
from ingredients_final t1, ingredients_final t2
where t1.ingredients < t2.ingredients
and (
   t1."effect 1" in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") and t1."effect 1" != ''
or t1."effect 2" in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") and t1."effect 2" != ''
or t1."effect 3" in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") and t1."effect 3" != ''
or t1."effect 4" in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") and t1."effect 4" != ''
)
) t12, ingredients_final t3
where t1ing < t3.ingredients and t2ing < t3.ingredients
and (
   t1e1 in (t3."effect 1",t3."effect 2",t3."effect 3",t3."effect 4") and t1e1 not in (t2e1,t2e2,t2e3,t2e4) and t1e1 != ''
or t1e2 in (t3."effect 1",t3."effect 2",t3."effect 3",t3."effect 4") and t1e2 not in (t2e1,t2e2,t2e3,t2e4) and t1e2 != ''
or t1e3 in (t3."effect 1",t3."effect 2",t3."effect 3",t3."effect 4") and t1e3 not in (t2e1,t2e2,t2e3,t2e4) and t1e3 != ''
or t1e4 in (t3."effect 1",t3."effect 2",t3."effect 3",t3."effect 4") and t1e4 not in (t2e1,t2e2,t2e3,t2e4) and t1e4 != ''
or t2e1 in (t3."effect 1",t3."effect 2",t3."effect 3",t3."effect 4") and t2e1 not in (t1e1,t1e2,t1e3,t1e4) and t2e1 != ''
or t2e2 in (t3."effect 1",t3."effect 2",t3."effect 3",t3."effect 4") and t2e2 not in (t1e1,t1e2,t1e3,t1e4) and t2e2 != ''
or t2e3 in (t3."effect 1",t3."effect 2",t3."effect 3",t3."effect 4") and t2e3 not in (t1e1,t1e2,t1e3,t1e4) and t2e3 != ''
or t2e4 in (t3."effect 1",t3."effect 2",t3."effect 3",t3."effect 4") and t2e4 not in (t1e1,t1e2,t1e3,t1e4) and t2e4 != ''
)
and not(
(t1e1 = t3."effect 1" and t1e2 = t3."effect 2" and t1e3 = t3."effect 3" and t1e4 = t3."effect 4") or 
(t2e1 = t3."effect 1" and t2e2 = t3."effect 2" and t2e3 = t3."effect 3" and t2e4 = t3."effect 4")
)
) t123, ingredients_final t4
where t1ing < t4.ingredients and t2ing < t4.ingredients and t3ing < t4.ingredients --no duplicates/singles
and ( --Fourth ingredient must add at least 1 new effect.
   t1e1 in (t4."effect 1",t4."effect 2",t4."effect 3",t4."effect 4") and t1e1 not in (t2e1,t2e2,t2e3,t2e4,t3e1,t3e2,t3e3,t3e4) and t1e1 != ''
or t1e2 in (t4."effect 1",t4."effect 2",t4."effect 3",t4."effect 4") and t1e2 not in (t2e1,t2e2,t2e3,t2e4,t3e1,t3e2,t3e3,t3e4) and t1e2 != ''
or t1e3 in (t4."effect 1",t4."effect 2",t4."effect 3",t4."effect 4") and t1e3 not in (t2e1,t2e2,t2e3,t2e4,t3e1,t3e2,t3e3,t3e4) and t1e3 != ''
or t1e4 in (t4."effect 1",t4."effect 2",t4."effect 3",t4."effect 4") and t1e4 not in (t2e1,t2e2,t2e3,t2e4,t3e1,t3e2,t3e3,t3e4) and t1e4 != ''
or t2e1 in (t4."effect 1",t4."effect 2",t4."effect 3",t4."effect 4") and t2e1 not in (t1e1,t1e2,t1e3,t1e4,t3e1,t3e2,t3e3,t3e4) and t2e1 != ''
or t2e2 in (t4."effect 1",t4."effect 2",t4."effect 3",t4."effect 4") and t2e2 not in (t1e1,t1e2,t1e3,t1e4,t3e1,t3e2,t3e3,t3e4) and t2e2 != ''
or t2e3 in (t4."effect 1",t4."effect 2",t4."effect 3",t4."effect 4") and t2e3 not in (t1e1,t1e2,t1e3,t1e4,t3e1,t3e2,t3e3,t3e4) and t2e3 != ''
or t2e4 in (t4."effect 1",t4."effect 2",t4."effect 3",t4."effect 4") and t2e4 not in (t1e1,t1e2,t1e3,t1e4,t3e1,t3e2,t3e3,t3e4) and t2e4 != ''
or t3e1 in (t4."effect 1",t4."effect 2",t4."effect 3",t4."effect 4") and t3e1 not in (t2e1,t2e2,t2e3,t2e4,t1e1,t1e2,t1e3,t1e4) and t3e1 != ''
or t3e2 in (t4."effect 1",t4."effect 2",t4."effect 3",t4."effect 4") and t3e2 not in (t2e1,t2e2,t2e3,t2e4,t1e1,t1e2,t1e3,t1e4) and t3e2 != ''
or t3e3 in (t4."effect 1",t4."effect 2",t4."effect 3",t4."effect 4") and t3e3 not in (t2e1,t2e2,t2e3,t2e4,t1e1,t1e2,t1e3,t1e4) and t3e3 != ''
or t3e4 in (t4."effect 1",t4."effect 2",t4."effect 3",t4."effect 4") and t3e4 not in (t2e1,t2e2,t2e3,t2e4,t1e1,t1e2,t1e3,t1e4) and t3e4 != ''
)
and not(
(t1e1 = t4."effect 1" and t1e2 = t4."effect 2" and t1e3 = t4."effect 3" and t1e4 = t4."effect 4") or 
(t2e1 = t4."effect 1" and t2e2 = t4."effect 2" and t2e3 = t4."effect 3" and t2e4 = t4."effect 4") or
(t3e1 = t4."effect 1" and t3e2 = t4."effect 2" and t3e3 = t4."effect 3" and t3e4 = t4."effect 4")
)
```

Our code for four ingredients doesn't cover the case when there are two pairs of ingredients that don't share any effects between the pairs. We'll solve this a little later, when our tables are in amore useful format. (We can easily add this case by self joining the 'potion pairs' table.)

We can now say goodbye to SQL (for now), and hello to Python, since we *still* have problems we need to solve. Yay!

---
## Transformations Python

The tables so far list every effect for every ingredient. We just want the valid effects, and we also want the total number of effects. Doing this in SQL would have been quite complicated, and we need all the effects to create each table from the previous one. 

First, if it hasn't been done already, we need to install sqlalchemy to interact with the database, and the import the neccessary functions:

```python
from sqlalchemy import create_engine

import pandas as pd
import numpy as np

# Setup connection
sqlEngine       = create_engine('postgresql://postgres:mysecretpassword@localhost:5432/postgres', pool_recycle=3600)
dbConnection    = sqlEngine.connect()
```

The connection to the database requires a password. As long as you've been following along, the password and port should be the same. (NEED TO ADD MYSQL).

Next, since we'll be using this script for the three tables, lets make it a little user friendly by adding the ability to select which table to use, along with the names of the columns we want the new tables to have, and their names.

```python
# Choose which table to access
while True:
    table_number = int(input("Choose 2, 3, or 4 "))
    if  table_number == 2:
        effects = 4                  # total possible number of effects
        string_part = 'potion_pairs' # name of table
        columns_part = ['ingredient 1', 'ingredient 2',
         'effect 1','effect 2','effect 3','effect 4',
         'number of effects']        # name of columns in new schema
        break
    elif table_number == 3:
        effects = 4
        string_part = 'potion_triples'
        columns_part = ['ingredient 1', 'ingredient 2','ingredient 3',
         'effect 1','effect 2','effect 3','effect 4',
         'number of effects']
        break
    elif table_number == 4:
        effects = 8
        string_part = 'potion_quads'
        columns_part = ['ingredient 1', 'ingredient 2','ingredient 3','ingredients 4',
         'effect 1','effect 2','effect 3','effect 4',
         'effect 5','effect 6','effect 7','effect 8',
         'number of effects']
        break
    else:
        print("Error, not 2, 3, or 4")
```

After that, we SELECT the data we want:

```python
# Create frame. 
frame = pd.read_sql(f"select * from public.{string_part}", dbConnection);

# Change to numpy array to access data individually
frame = frame.to_numpy()
```

And then remove the data we don't need:

```python
# Remove duplicate data and fill remaining spaces, if any, with blanks
new_frame = np.empty(table_number + effects + 1)
for rows in frame:
    set_values = list(set(rows[table_number:]))
    list_values = list(rows[table_number:])
    list_values_copy = list(rows[table_number:])
    for i in set_values:
        if i in list_values:
            list_values.remove(i)
            list_values_copy.remove(i)
                
    for i in set_values:
        if i in list_values_copy:
            list_values_copy.remove(i)
            
    for i in list_values_copy:
        if i in list_values:
            list_values.remove(i)
            
            
    while '' in list_values:
        list_values.remove('')
    number_of_effects = len(list_values)
    while len(list_values) < effects:
        list_values = np.append(list_values,'')
    list_values = np.append(rows[0:table_number],list_values)
    list_values = np.append(list_values, number_of_effects)
    new_frame = np.vstack([new_frame, list_values])
new_frame = new_frame[1:]

# Create new reshaped dataframe for output
final_frame=pd.DataFrame(new_frame, columns=columns_part)
```

And, finally, write it back to the database:

```python
# SQL magic to write new table    
tableName = f"{string_part}_final" 

try:

    final_frame           = final_frame.to_sql(tableName, dbConnection, if_exists='fail');

except ValueError as vx:

    print(vx)

except Exception as ex:   

    print(ex)

else:

    print("Table %s created successfully."%tableName);   

finally:

    dbConnection.close()
```

The final thing we need to do is fix the problem we had with missing disjoint pairs of ingredients. Now that we have the 'potions pairs final' table, we can simply do a self join:

```sql
SELECT t1."ingredient 1" as "ingredient 1", t1."ingredient 2" as "ingredient 2", --disjoint potion pairs
t2."ingredient 1" as "ingredient 3", t2."ingredient 2" as "ingredient 4",
t1."effect 1" as "effect 1", t1."effect 2" as "effect 2", t1."effect 3" as "effect 3", t1."effect 4" as "effect 4", 
t2."effect 1" as "effect 5", t2."effect 2" as "effect 6", t2."effect 3" as "effect 7", t2."effect 4" as "effect 8", 
t1."number of effects" + t2."number of effects" as "number of effects"
FROM potion_pairs_final t1, potion_pairs_final t2
WHERE t1."ingredient 1" < t2."ingredient 1" --stops duplicates
and t1."ingredient 1" not in (t1."ingredient 2", t2."ingredient 1", t2."ingredient 2") 
and t1."ingredient 2" not in (t2."ingredient 1", t2."ingredient 2")
and t2."ingredient 1" not in (t2."ingredient 2") --the two potions cannot share any effects 
and (t1."effect 1" not in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") or t1."effect 1" = '') 
and (t1."effect 2" not in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") or t1."effect 2" = '') 
and (t1."effect 3" not in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") or t1."effect 3" = '') 
and (t1."effect 4" not in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") or t1."effect 4" = '')
```

Now that all four tables are in the right format, we can begin the analysis. (ADD MISSING ROWS 2,3,8).

---
## Superset Analysis

We first have to load our new tables into Superset as we did at the end of the previous post. Remember to load all four tables, 'potion pairs', 'potion triples', 'potion quads', and 'potion pair pairs'.

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/Superset_2_dark.png)

We can now make some charts to understand the data. What we want to do is create some overview charts to see the total number of potions, and then charts that are basically copies of the tables we just made. Since Superset is interactable, these charts are more easily used than the SQL tables.

As an example, we can make the charts for 'potion pairs'. The process will be the same for the others.

First, we want an overview of the number of potions by number of effects.

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/Superset_6_dark.png)
.
![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/Superset_7_dark.png)

Then, we want a chart containing each and every potion pair.

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/Superset_pairs_1.png)

We also want to add a chart that we can use to filter our other charts by number of effects. This will requiring adding 2 rows to the 'potion quads' table because (reasons).

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/Superset_number_of_effects.png)

Now, we can put everything together into a dashboard as follows.

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/Superset_5_dark.png)
.
![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/Superset_triples.png)

And we can filter them. For example, looking at all potions with exactly 4 effects.

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/Superset_n4.png)
.
![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/Superset_n4_pairs.png)

For those of you actually interested in Morrowind, we can make a few observations. (something something shock potion, double berry, probably already knew).

---
## Conclusion

And that's this project done! In this part, we used our base table to create a bunch of new tables with the relevant data, created an interactive dashboard analyse the data, and drew some conclusions.

Overall, we managed to create a simple ELT pipeline that takes a google sheet document, does a bunch of transformations, and ends up with a nice dashboard in Superset.

I hope to see you all again next time. Goodbye!
