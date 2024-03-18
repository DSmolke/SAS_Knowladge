# SAS_Knowladge

#### Środowisko SAS OnDemand for Academics dostępne dla wszystkich https://welcome.oda.sas.com/



# A00-232 Exam

#### Pełny opis wymaganych zagadnień https://www.sas.com/en_us/certification/exam-content-guides/advanced-programmer-sas94.html

#### Opis zbiorów sashelp https://support.sas.com/documentation/tools/sashelpug.pdf


### Część 1 - Accessing Data Using SQL (35%)

## Moduł 1 - Generate detail reports by working with a single table, joining tables, or using set operators in SQL

- Use PROC SQL to perform SQL queries.
- Select columns in a table with a SELECT statement and FROM clause.
- Create a table from a query result set.

```
    proc sql;
        create table baseball_1 as
        select name, team, natbat from sashelp.baseball;
    quit;
```

- Create new calculated columns
- Assign an alias with the AS keyword

```
    proc sql;
        select name, (crhome / cratbat) * 100 as HomeRunsPercentage
        from sashelp.baseball;
    quit;
```

- Use case logic to select values for a column.
- Retrieve rows that satisfy a condition with a WHERE clause.

```
    proc sql;
        select name, 
            case
                when crhome > 15 then 'high'
                when crhome between 5 and 15 then 'medium'
                else 'low'
            end as HomeRunScore
        from sashelp.baseball
        where crAtBat > 200;
        ;
    quit;

```

-  Subset data by calculated columns

```
    proc sql;
        create table work.baseball2 as
        select *, (crhome / cratbat) * 100 as HomeRunsPercentage
        from sashelp.baseball
        where calculated HomeRunsPercentage > 2;
    quit;
```

-  Join tables - inner joins, full joins (coalesce function), right joins, left joins.

[placeholder na grafikę joinów]

```
    proc sql;
    	create table work.sales_inner_join as
        select a.*, b.MapIDNameAbrv as state_abbreviation
        from sashelp.prdsal2 a
        inner join sashelp.gcstate b /*, = inner join*/
        on a.state = b.MapIDName;
    quit;
```

```
    proc sql;
        create table state_full_join as
        select a.*, coalesce(Country, ISOalpha3) as Country_Name
        from sashelp.prdsal2 a
            full join sashelp.gcstate b
        on a.state = b.MapIDName;
    quit;
```
```
    proc sql;
        create table flights_right_join as
        select b.*, coalesce(a.statename, 'NON_US') as State
        from sashelp.zipcode a
            right join sashelp.revhub2 b
        on a.city = b.hub
        where b.hub <> 'London';
    quit;
```
```
    proc sql;
        create table flights_left_join as
        select a.*, coalesce(b.statename, 'NON_US') as State
        from sashelp.revhub2 a
            left join sashelp.zipcode b
        on a.hub = b.city
        where a.hub <> 'London'; /* Stan Kentucky posiada miasto London :) */
    quit;

```

- Combine tables using set operators - union, outer union, except, intersect.
https://support.sas.com/documentation/cdl/en/sqlproc/63043/HTML/default/viewer.htm#n0vo2lglyrnexwn14emi8m0jqvrj.htm
(błędna formuła dokumentacji)
```
data set1;
    input x $ z;
    datalines;
A 1
A 1
B 2
;
run;

data set2;
    input w $ y;
    datalines;
A 1
B 2
C 3
C 3
;
run;

title 'Set 1 rows';
proc sql;
	select * from set1;
quit;

title 'Set 2 rows';
proc sql;
	select * from set2;
quit;

title 'Union (what distinct values set1 and set2 have)';
proc sql;
	select * from set1
	union
	select * from set2;
quit;

title 'Outer union (what set1 and set2 have in total)';
proc sql;
	select * from set1
	outer union
	select * from set2;
quit;

title "Except (what set2 has that set1 don't)";
proc sql;
	select * from set2
	except
	select * from set1;
quit;

title 'Intersect (what distinct values set1 and set2 have in common)';
proc sql;
	select * from set1
	intersect
	select * from set2;
quit;

```
- Sort data with an ORDER BY clause.
- Assign labels and formats to columns

```
    proc sql noprint;
        create table cars_euro as 
        select make, model, msrp, msrp*0.9 as EuroMSRP "MSRP in EURO" format euro32.
        from sashelp.cars
        order by calculated EuroMSRP DESC;
    quit;
```

## Moduł 2 - Generate summary reports by working with a single table, joining tables, or using set operators in the SQL

- Summarize data across and down columns using summary functions (AVG, COUNT, 
MAX, MIN, SUM)

```
    proc sql;
        select 
            avg(msrp) as avg_msrp,
            max(msrp) as max_msrp,
            min(msrp) as min_msrp,
            count(*) as cars_count,
            sum(msrp) as total_cars_msrp
        from sashelp.cars;
    quit;

```

- Group data using GROUP BY clause. 
- Filter grouped data using HAVING clause.

```
    proc sql;
        select type, avg(msrp) as avg_msrp
        from sashelp.cars
        group by type;
    quit;

    proc sql;
        select type, avg(msrp) as avg_msrp
        from sashelp.cars
        group by type
        having avg_msrp > 25000;
    quit;

```


- Eliminate duplicate values with the DISTINCT keyword.

```
    proc sql;
        select type
        from sashelp.cars;
    quit;

    proc sql;
        select distinct type
        from sashelp.cars;
    quit;
```

### Moduł 3 - Construct sub-queries and in-line views within an SQL procedure step. 

- Subset data by using non-correlated subqueries.

```
    proc sql;
        select 
            min(msrp) as min_msrp format dollar32.,
            avg(msrp) as avg_msrp format dollar32.,
            max(msrp) as max_msrp format dollar32.
        from sashelp.cars;
    quit;
    proc sql;
        select * 
        from sashelp.cars
        where msrp between
            (select min(msrp) from sashelp.cars)
            and
            (select avg(msrp) from sashelp.cars);
    quit;
```
- Reference an in-line view with other views or tables (multiple tables). 
