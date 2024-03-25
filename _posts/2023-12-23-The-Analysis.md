---
title: "The Analysis"
layout: post
---

## The Analysis (WORK IN PROGRESS)

Welcome back. If you haven't come from my previous post, this follows on directly from there.

Today, we're going to look at some transformations to get the data we really want, followed by the actual analysis.

Let's get started!

---
## Transformations
What we want is all possible potion combination. The rules are as follows:
* Each Ingredient has up to four effects. 
* A potion has an effect if two (or more) ingredients share the same effect.
* The total number of ingredients in a potion is limited to four.

So, using our 'Ingredients_final' database, we want to create three new databases. One for potions with two ingredients, one for three, and one for four (Spoiler, we are actually going to create four).

We are going to use the following SQL code to create our first database, made from two ingredients.

```sql
select t1.ingredients as t1ing, t2.ingredients as t2ing,
t1."effect 1" as t1e1, t1."effect 2" as t1e2, t1."effect 3" as t1e3, t1."effect 4" as t1e4,
t2."effect 1" as t2e1, t2."effect 2" as t2e2, t2."effect 3" as t2e3, t2."effect 4" as t2e4,
  cast(t1."effect 1" in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") and t1."effect 1" != '' as INTEGER)
+ cast(t1."effect 2" in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") and t1."effect 2" != '' as INTEGER)
+ cast(t1."effect 3" in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") and t1."effect 3" != '' as INTEGER)
+ cast(t1."effect 4" in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") and t1."effect 4" != '' as INTEGER)
as number_of_effects
from  ingredients_final t1, ingredients_final t2
where t1.ingredients < t2.ingredients
and (
   t1."effect 1" in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") and t1."effect 1" != ''
or t1."effect 2" in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") and t1."effect 2" != ''
or t1."effect 3" in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") and t1."effect 3" != ''
or t1."effect 4" in (t2."effect 1",t2."effect 2",t2."effect 3",t2."effect 4") and t1."effect 4" != ''
) 
```

We can adapt the above code for three ingredients.

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

We can adapt the above code for four ingredients.

```sql
select t1ing, t2ing, t3ing, t4.ingredients as t4ing, -- missing disjoint potion pairs intentionally.
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

Now, our code for four ingredients doesn't cover the case when there are two pairs of ingredients that don't share any effects between the pairs.

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

Now, the data has extra columns that we do not need. We can use the following python code to remove these for each database. We can switch the numbers to access each database (wrong word?).

```python
# Remove the columns containing duplicate information in potion_pairs
# in the postgres database

from sqlalchemy import create_engine

import pandas as pd
import numpy as np

#setup connection
sqlEngine       = create_engine('postgresql://postgres:mysecretpassword@localhost:5432/postgres', pool_recycle=3600)
dbConnection    = sqlEngine.connect()

#code for later
table_number = 4 #raw input time
if  table_number == 2:
    string_part = 'potion_pairs'
    effects = 4
elif table_number == 3:
    effects = 4
    string_part = 'potion_triples'
elif table_number == 4:
    effects = 8
    string_part = 'potion_quads'

#create frame. Replace name with 'potion_pairs','potion_triples','potion_quads'
frame           = pd.read_sql("select * from public.potion_quads", dbConnection);

#change to numpy array to access data individually
frame = frame.to_numpy()

#remove duplicate data and fill remaining spaces, if any, with blanks
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

#create new reshaped dataframe for output
#rewrite later to include 2,3,4
final_frame=pd.DataFrame(new_frame, columns=['ingredient 1', 'ingredient 2','ingredient 3','ingredients 4','effect 1','effect 2','effect 3','effect 4','effect 5','effect 6','effect 7','effect 8','number of effects'])


#sql magic to write new table 
#rewrite later to include 2,3,4      
tableName = "potion_quads_final" 

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

Now that our data is in the right format, we can begin the analysis.



---
## Superset Analysis

(load data in)

(make charts)

(make dashboard)

(make dramatic revelation)

---
## Conclusion

(something something this has been fun)
