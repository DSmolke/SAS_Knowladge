<div>

# SAS_Knowladge

<br>






## A00-232 SAS 9.4 Advanced Programming Certification

#### SAS Skill Builder - portal do nauki dla studentów https://www.sas.com/pl_pl/learn/academic-programs/students.html#academic-software

#### Środowisko SAS OnDemand for Academics dostępne dla wszystkich https://welcome.oda.sas.com/

#### Pełny opis wymaganych zagadnień A00-232 https://www.sas.com/en_us/certification/exam-content-guides/advanced-programmer-sas94.html

#### Opis zbiorów sashelp https://support.sas.com/documentation/tools/sashelpug.pdf

<br>


## Część 1 - Accessing Data Using SQL (35%)

### Moduł 1 - Generate detail reports by working with a single table, joining tables, or using set operators in SQL

<br>


https://github.com/DSmolke/SAS_Knowladge/assets/106284705/24cf1498-791d-4d2d-9074-0fc5711edc1e



- Use PROC SQL to perform SQL queries.
- Select columns in a table with a SELECT statement and FROM clause.
- Create a table from a query result set.


```sql
    proc sql;
        create table baseball_1 as
        select name, team, natbat from sashelp.baseball;
    quit;
```

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/2a64e53f-7d01-4f70-ae67-5c72da0b7652



- Create new calculated columns
- Assign an alias with the AS keyword

```sql
    proc sql;
        select name, (crhome / cratbat) * 100 as HomeRunsPercentage
        from sashelp.baseball;
    quit;
```

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/25b09645-66c7-48a2-843e-4e34e4ae2b5c



- Use case logic to select values for a column.
- Retrieve rows that satisfy a condition with a WHERE clause.

```sql
    proc sql;
        select name, 
            case
                when crhome > 15 then 'high'
                when crhome between 5 and 15 then 'medium'
                else 'low'
            end as HomeRunScore
        from sashelp.baseball
        where crAtBat < 200;
        
    quit;

```

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/8548f525-d632-42dd-be1c-a58f51c562a3



-  Subset data by calculated columns

```sql
    proc sql;
        create table work.baseball2 as
        select *, (crhome / cratbat) * 100 as HomeRunsPercentage
        from sashelp.baseball
        where calculated HomeRunsPercentage > 2;
    quit;
```

<br>

-  Join tables - inner joins, full joins (coalesce function), right joins, left joins.

![image](https://github.com/DSmolke/SAS_Knowladge/assets/106284705/bfad8182-c4f0-4386-857d-6996892c0472)







https://github.com/DSmolke/SAS_Knowladge/assets/106284705/47785274-c372-432c-837b-bc40569d3b48





```sql
data work.set1;
    input x $ z;
    datalines;
A 1
B 2
;
run;

data work.set2;
    input w $ y;
    datalines;
A 3
B 2
D 1
;
run;

proc sql;
	select * from work.set1;
	select * from work.set2;
quit;

proc sql;
	select
		a.*,
		b.*
	from work.set1 a
	inner join work.set2 b 
	on a.x = b.w;
quit;
```

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/e405d151-e1d9-480a-a9bf-8e916b52778b



```sql
data work.set1;
    input x $ z;
    datalines;
A 1
B 2
;
run;

data work.set2;
    input w $ y;
    datalines;
A 3
B 2
D 1
;
run;

proc sql;
	select * from work.set1;
	select * from work.set2;
quit;

proc sql;
	select 
		a.*,
		b.*,
		coalesce(a.x, b.w) as coalesce_var
	from work.set1 a 
	full join work.set2 b 
	on a.x = b.w;
quit;
```

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/7fada16c-d33b-42a0-b395-8caeb3dbaa9d



```sql
data work.set1;
    input x $ z;
    datalines;
A 1
B 2
C 3
;
run;

data work.set2;
    input w $ y;
    datalines;
A 3
B 2
D 1
;
run;

proc sql;
	select * from work.set1;
	select * from work.set2;
quit;

proc sql;
	select
		a.*,
		b.*
	from work.set1 a
	right join work.set2 b
	on a.x = b.w;
quit;

proc sql;
	select
		a.*,
		b.*
	from work.set1 a
	left join work.set2 b
	on a.x = b.w;
quit;

```

<br>


https://github.com/DSmolke/SAS_Knowladge/assets/106284705/293d961a-5aa8-47e8-97f7-61c4ed892bf1

- Combine tables using set operators - union, outer union, except, intersect.



```sql
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

title 'Union All (all set 1 values followed by all set 2 values)';
proc sql;
	select * from set1
	union all
	select * from set2;
quit;
```


<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/2e850c61-8e5c-4e27-b7be-f384facbaa36





- Sort data with an ORDER BY clause.
- Assign labels and formats to columns

```sql
    proc sql noprint;
        create table cars_euro as 
        select make, model, msrp, msrp*0.9 as EuroMSRP "MSRP in EURO" format euro32.
        from sashelp.cars
        order by calculated EuroMSRP DESC;
    quit;
```
<br>

## Moduł 2 - Generate summary reports by working with a single table, joining tables, or using set operators in the SQL

<br>


https://github.com/DSmolke/SAS_Knowladge/assets/106284705/bc38d1bf-4a8e-4c1f-a4ab-7ff154d5042a



- Summarize data across and down columns using summary functions (AVG, COUNT, 
MAX, MIN, SUM)

```sql
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

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/e66a9b3c-9169-42ac-b714-d9095b6fda94



- Group data using GROUP BY clause. 
- Filter grouped data using HAVING clause.

```sql
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

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/15240043-0392-4fe5-876f-473414b637a4



- Eliminate duplicate values with the DISTINCT keyword.

```sql
    proc sql;
        select type
        from sashelp.cars;
    quit;

    proc sql;
        select distinct type
        from sashelp.cars;
    quit;
```
<br>

### Moduł 3 - Construct sub-queries and in-line views within an SQL procedure step. 

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/67232200-aa89-4871-b17e-c960f23db10f



- Subset data by using non-correlated subqueries.
- Reference an in-line view with other views or tables (multiple tables). 

```sql
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

<br>

### Moduł 4 -  Use SAS SQL procedure enhancements.

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/7100e6ac-a7dc-4428-bc62-6a2036f38f4d


- Use SAS data set options with PROC SQL (KEEP=, DROP=, RENAME=, OBS=).

```sql
    proc sql;
	    select * from sashelp.cars(keep=make model msrp where=(msrp > 15000) rename=(make=Brand));
    quit;
```

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/0d3d36a2-180f-45df-ba6f-cb42efb6d7ab



- Use PROC SQL invocation options (INOBS=, OUTOBS=, NUMBER)
```sql
    proc sql inobs=1;
        select avg(msrp) as local_average, * from sashelp.cars;
    quit;

    proc sql outobs=1;
        select avg(msrp) as global_average, * from sashelp.cars;
    quit;

    proc sql outobs=1 number;
        select avg(msrp) as global_average, * from sashelp.cars;
    quit;
```

<br>


https://github.com/DSmolke/SAS_Knowladge/assets/106284705/e16125c7-1ff4-4a8c-a676-43917f6cc667


- Use PROC SQL invocation options (NOPRINT)
invoke - wywołać

```sql
    proc sql /*noprint*/;
        select * from sashelp.cars;
    quit;

```

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/d3e08018-417a-45bb-8906-d0acd86dbaf4



- Use SAS functions (SCAN, SUBSTR, LENGTH)

```sql
    data work.tmp;
        set sashelp.cars;
        short_model = SCAN(model, 1);
        model_symbol = SUBSTR(model, 1, 2);
        model_length = LENGTHN(model);
        
        keep model short_model model_symbol model_length;
    run;

    proc sql;
        select
            model,
            SCAN(model, 1) as short_model,
            SUBSTR(model, 1, 2) as model_symbol,
            LENGTHN(model) as model_length
        from sashelp.cars;
    quit;
```

<br>


https://github.com/DSmolke/SAS_Knowladge/assets/106284705/397015f9-9e73-4246-bff1-356a8b6f1c61



- Access SAS system information by using DICTIONARY tables (members, tables, 
columns)

```sql
    proc sql feedback;
        select *
        from dictionary.columns
        where libname = 'SASHELP' and memname = 'CARS';
    quit;

```

-  Use the CALCULATED keyword.

```sql
    proc sql;
        select *, msrp + 1000 as inflation_msrp
        from sashelp.cars
        where calculated inflation_msrp > 35000;
    quit;
```

<br>

## Część 2 - Macro Processing (35%)

<br>

## Moduł 1 - Create and use user-defined and automatic macro variables within the SAS Macro Language.

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/1631fa5c-9ee8-47c6-864e-47cd0566a715



- Define and use macro variables.
- Use macro variable name delimiter. (.)

```sql
    %let country = Poland;
    %put &country;
    %put &country.&country;
```

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/024fae83-a7c7-41f6-b494-dbe4b85b75c7



- Use INTO clause of the SELECT statement in SQL to create a single variable or a list of variables. 

```sql
    proc sql inobs=1 /*noprint*/;
        select make, msrp
        into :make_var, :msrp_var
        from sashelp.cars;
    quit;
    %put &make_var.;
    %put &msrp_var.;

    proc sql noprint;
        select distinct type
        into :cars_types separated by ", "
        from sashelp.cars;
    quit;
    %put &cars_types.;
```

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/7cfb251c-2d65-4991-b3b2-d319d7f6cca6



- Use the SYMPUTX routine in a DATA Step to create a single variable or a list of variables.

```sql
    data _NULL_;
        set sashelp.cars(obs=1);
        call symputx('make', make, 'g');
    run;
    %put make = &make;


    data _NULL_;
        set sashelp.cars(where=(upcase(make) <> "MERCEDES-BENZ" and upcase(make) <> "LAND ROVER"));
        call symputx(make, model);
    run;
    %put _ALL_;

```

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/1b5efe14-0265-4d1b-ba72-7217a57e7e5b



- Control variable scope with:
    - %GLOBAL statement 
    - %LOCAL statement
    - SYMPUTX scope parameter

```sql
    /*%global local_var;*/
    %macro declare_var();
    /* 	%global local_var; */
        %local local_var;
        %let local_var = xxx;
    /* 	%put local_var = &local_var.; */
    %mend declare_var;

    %declare_var();
    %put local_var = &local_var.;

    %put _all_;
```

<br>

### Moduł 2 - Automate programs by defining and calling macros using the SAS Macro Language.

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/153f539c-5600-4dea-97e2-60ff18b904da



- Define a macro using the %MACRO and %MEND statements.
- Calling a macro with and without parameters.
- Document macro functionality with comments

```sql
%macro keep_columns(columns);
/****************************************************************************************/
/*** Prupose: Creates datastep keep statement that contains provided column names *******/
/****************************************************************************************/
        
    keep &columns; /* Columns argument invocation */
%mend keep_columns;


    data work.european_cars;
        set sashelp.cars;
        keep make model msrp;
    /* 	%keep_columns(make model msrp); */
    run;

    %macro report_factory(query);
        proc sql;
            &query. ;
        quit;
    %mend report_caftory;

    %report_factory(select * from sashelp.cars);
```

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/f595aca5-298b-49d7-8556-d4ba059e7772



-  Generate SAS Code conditionally by using the %IF-%THEN-%ELSE macro statements or iterative %DO statements.

```sql
    %macro generate_report(version, inobs=10);
        %if &version. = 1 %then %do;
            %let option=noprint;
            %put &version.;
        %end;
        
        %else %do;
            %let option=;
        %end;
        
        proc sql &option. inobs=&inobs.;
            select * from sashelp.cars;
        quit;

    %mend;

    %generate_report(1);
    %generate_report(2);


    %macro multiple_generate_report(n_reports);
        
        %do i=1 %to &n_reports.;
            %generate_report();
        %end;
        
    %mend;

    %multiple_generate_report(4);

```

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/95296317-eaad-4e35-a83f-45a2344d8238



- Use the SAS AUTOCALL facility to permanently store and call macros
https://support.sas.com/documentation/cdl/en/mcrolref/61885/HTML/default/viewer.htm#a001328769.htm

```sql
    /* First step - create catalog */
    libname macrodir '/home/u62703738/macro-store';
    options mstored sasmstore=macrodir mautosource;

    %macro start_system() / STORE;
        %put SYSTEM HAS STARTED!;
    %mend;


    /* Reset Session (f9)  */
    libname macrodir '/home/u62703738/macro-store'; /* Use your path */
    options mstored sasmstore=macrodir mautosource;
    %start_system();

```

<br>


### Moduł 3 - Use macro functions.

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/18e0089a-420f-47f4-bab3-95b3a26c79f1


- Use macro functions. (%SCAN, %SUBSTR, %UPCASE)


```sql
    data work.experssion_table;
    infile datalines delimiter=','; 
    input expression $;
        datalines;
    New York
    ;
    run;

    data work.expression_table_after_changes;
        set work.experssion_table;
        expression_scan = scan(expression, 1);
        expression_substr = substr(expression, 5);
        expression_upcase = upcase(expression);
    run;


    %let column_names = make model;
    %let car_make = Bmw;

    proc sql;
        select 
            %scan(&column_names., 1),
            %scan(&column_names., 2)  
        from sashelp.cars
        where make = "%upcase(&car_make.)";
    /* 	where make = %upcase(&car_make.); */
        
    /* 	select make, model from sashelp.cars where make = "BMW"; */
    quit;
```

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/e1804bf4-867c-4138-96e7-abb22bcbddc0



- Use macro quoting functions. (%NRSTR, %STR)
- Use macro evaluation functions. (%SYSEVALF)
- Use %SYSFUNC to execute DATA step functions within the SAS Macro Language.

```sql
    %put some ; epic ; statement; /* Error */
    %put %str(some ; epic ; statement;);
    %put %str(%some ; &epic ; &&statement;); /* Warning */
    %put %nrstr(%some ; &epic ; &&statement;);


    %put NOTE: syseval_result = %sysevalf(10 + 10 + 100);
    %put NOTE: sysfunc_result = %sysfunc(round(100.99, 1));
    %put NOTE: sysfunc_result_formated = %sysfunc(round(100.99, 1), dollar32.);
    %put NOTE: sysfunc_result_formated_trimed = %trim(%sysfunc(round(100.99, 1), dollar32.));
```

<br>

### Moduł 4 - Debug macros

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/74e2b20a-ce5f-492f-9b91-e5d681ca6cda



- Trace the flow of execution with the MLOGIC option. 
- Examine the generated SAS statements with the MPRINT option.
- Examine macro variable resolution with the SYMBOLGEN option.
- Use the %PUT statement to print information to the log

https://www.diffchecker.com/text-compare/

```sql
    options mlogic mprint symbolgen;
    %macro generate_report(version, inobs=10);
        %if &version. = 1 %then %do;
            %let option=noprint;
            %put &version.;
        %end;
        
        %else %do;
            %let option=;
        %end;
        
        proc sql &option. inobs=&inobs.;
            select * from sashelp.cars;
        quit;

    %mend;

    %generate_report(1);

    %put ERROR: run completed;
```

<br>

### Moduł 5 - Create data-driven programs using SAS Macro Language.

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/2737d0bc-a2c8-4c52-b7ab-4915e8ef19a0


• Create a series of macro variables.
• Use indirect reference to macro variables. (&&, etc.)
• Generate repetitive macro calls.

```sql
    proc sql number;
	select distinct make
		into :brand1 - 
	from sashelp.cars;
    quit;

    %put _user_;

    %macro display_brands();
        %do  i=1 %to 38;
            %put brand&i. = &&brand&i; /* Warning */
        %end;
    %mend display_brands;

    %display_brands();

```

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/d1f4bb14-4acc-4f9b-bc99-bd0641c72788

<br>

- Incorporate DICTONARY tables in data driven macros.

```sql
    proc sql inobs=5;
        create table dict as
        select * from dictionary.columns;
    quit;

    proc sql number;
        select name
        into :column1 -
        from dictionary.columns
        where libname="SASHELP" and upper(memname)="CARS" and type="num";
    quit;

    %macro generate_proc_freqs(last=10);
        %do i=1 %to &last.;
            proc freq data=sashelp.cars;
                table &&column&i.;
            quit;
        %end;
    %mend;

    %generate_proc_freqs();
```

<br>

## Część - Advanced Techniques (30%)

<br>

### Moduł 1 - Process data using 1 and 2 dimensional arrays

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/36333bcd-c456-42d1-9bc7-38eb522db16b


- Define and use character arrays
- Define and use numeric arrays
- Create variables with arrays
- Define arrays as temporary arrays

```sql
data work.arr_demo;
	array temperature[6] mon tue wed thu fri sat (5, 10, 11.5, 10,5, 9);
	array weekday[6] $9 /*_temporary_*/ ("Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday");
	do i = 1 to 6;
		put 'NOTE: ' weekday[i] 'temperature was ' temperature[i];
	end;
	
	monday_name1 = weekday1;
	monday_name2 = weekday[1];
run;
```

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/250c8b66-1abb-4d95-ba52-8d586576e763



- Reference arrays within a DO loop

```sql
data work.arr_demo;
	array open_array[*] a1-a10; /* SAS przeliczy długość tablicy */
	do i = 1 to dim(open_array);
		open_array[i] = i;
	end;
run;
```

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/9112a895-b6f8-45fe-91e5-ede668b7936b



- Load initial values for an array from a SAS data set
```sql
proc sql;
	create table work.cars_msrp_stats as
	select 
		min(msrp) as msrp_min,
		avg(msrp) as msrp_avg,
		max(msrp) as msrp_max
	from sashelp.cars;
quit;

data work.stats_arr;
	set work.cars_msrp_stats sashelp.cars;
	array cars_stats[3] msrp_min msrp_avg msrp_max;

run;

data work.datalines_arr;
/* 	input a1-a3 b1-b3; */
	input a1-a6;
/* 	array values[2, 3] a1-a3 b1-b3; */
	array values[6] a1-a6;
	
datalines;
1 2 3
4 5 6
;

run; 
```

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/066c4309-720d-4f35-814c-b091f0992557



- Specify the array dimension with the DIM function

```sql
data work.dim_demo;
	array matrix[1, 2, 3];
	
	length1 = dim(matrix);
	length2 = dim2(matrix);
	length3 = dim2(matrix);
	
	do i=1 to dim(matrix);
		do j=1 to dim2(matrix);
			do k=1 to dim3(matrix);
				matrix[i, j, k] = rand('Integer', 1, 1000);
			end;
		end;
	end;
	
run;
```

<br>

### Moduł 2 - Process data using hash objects.


<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/9847f97d-dd0f-4ff4-a74c-4b3ec6c4b5ee



https://github.com/DSmolke/SAS_Knowladge/assets/106284705/69a64f6a-292a-45fd-8208-dc56c86268fb




https://github.com/DSmolke/SAS_Knowladge/assets/106284705/64fcf7ea-480f-4482-a5fa-bc6829f2bfed




- Declare hash and hash iterator objects
	- Dataset argument
	- Ordered argument
	- Multidata argument
- Use hash object methods
	- definekey()
	- definedata()
	- definedone()
	- find()
	- add()
	- output()
- Use hash iterator object methods
	- first()
	- next()
	- last()
	- prev()
- Use hash objects as lookup tables.
- Use hash objects to create sorted data sets.
- Use hash iterator objects to access data in forward or reverse key order

  
```sql

proc sql;
	create table work.cars as
	select make, model from sashelp.cars;
quit;

data _null_;
	length make $ 13 model $ 40;
	
	if _n_ = 1 then do;
	    declare hash my_hash(dataset: "work.cars", ordered: 'a');
	    rc = my_hash.definekey('make');
	    rc = my_hash.definedata('make', 'model'); /*ważne - tutaj dajemy także klucz jeżeli robimy output*/
	    my_hash.definedone();
	 end;
	 
	 
/* 	rc = my_hash.add(key: 'Fiat', data: '126p'); */
	
	
	declare hiter my_hash_iter('my_hash');
/* 	first														 last		   */
/* 	[(make, model), (make, model), (make, model), (make, model), (make, model)] */
	bongo_bongo = my_hash_iter.first();
	do while (bongo_bongo = 0);

		put 'NOTE: ' model;
		bongo_bongo = my_hash_iter.next();
	end;
	
	bongo_bongo = my_hash_iter.last();
	do while (bongo_bongo = 0);

		put 'WARNING: ' model;
		bongo_bongo = my_hash_iter.prev();
	end;

run;

data _NULL_;
	if 0 then set work.cars;
		if _n_ = 1 then do;
		    declare hash my_hash(multidata: 'y', ordered: 'a');
		    rc = my_hash.definekey('make');
		    rc = my_hash.definedata('make', 'model');
		    my_hash.definedone();
		    call missing(make,model);
	 	end;
	set work.cars end=end;
	my_hash.add();
	if end then do;
		rc = my_hash.output(dataset:"work.make_model_dict");
		
		rc = my_hash.find(key: 'Audi');
		if (rc = 0) then do;
			put 'WARNING: ' 'Found make: ' make 'Found model: ' model;
			rc = my_hash.find_next();
			do while (rc = 0);
				put 'WARNING: ' 'Found make: ' make 'Found model: ' model;
				rc = my_hash.find_next();
			end;
		end;
	end;
	

	
run;
	
```

<br>

### Moduł 3 - Use SAS utility procedures. 

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/1b21f505-c3ba-4343-9ead-75ce4d03ed45



- Specify a template using the PICTURE statement within the FORMAT Procedure*
- Specify templates for date, time, and datetime values using directives.
- Specify templates for numeric values using digit selectors.
- PICTURE statement options: round, default, datatype, multiplier, prefix
```sql
proc format;
	value centuries '01JAN1980'd - high = " > 1980"
		  low - '31DEC1979'd = " < 1980" ;
run;

proc sql;
	select date, date as date_fromated format centuries. from sashelp.usecon;
quit;

option nolabel;
proc sql;
	select * from dictionary.columns where format like "MON%" ;
quit;

proc format;
	picture pln (round /*default=5*/)  low-high='000,000zł' (multiplier=3.99 prefix='converted to pln: ');
quit;
proc sql;
	select msrp, msrp as pl_msrp format pln. from sashelp.cars;
quit;

```

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/d8de0427-8b38-4b2e-8b72-74f8322b3a9f




- Create custom functions with the FCMP procedure
- Create character and numeric custom functions with single or multiple 
arguments.
- Create custom functions based on conditional processing.
- Use custom functions with the global option CMPLIB=

```sql
proc fcmp outlib=work.funcs.cars;
	function horsepowerToKW(horsepower);
		static kwRatio 0.74;
		if (kwRatio ne .) then do;
			kw = (horsepower * kwRatio);
		end;
		return (kw);
	endsub;
	
	function kwScore(kw) $;
		length score $ 6;
		if (kw < 100) then score = 'weak';
		if (kw >= 100 and kw < 150) then score = 'medium';
		if (kw >= 150) then score = 'strong';
		
		return (score);
	endsub;
run;

option CMPLIB=work.funcs;

proc fcmp data=sashelp.cars out=work.cars;
	length score $ 6;
	kw = horsepowerToKW(horsepower);
	kw_score = kwScore(kw);
run;

```

<br>

### Moduł 4 Use advanced functions.

<br>

https://github.com/DSmolke/SAS_Knowladge/assets/106284705/d33a595c-e63a-4fff-96dd-bb745d9f8e14




- Finding strings or words with the FINDC/FINDW functions.
- Counting strings or words with the COUNT/COUNTC/COUNTW functions.
```sql
	%let vowels = aeiouy;
	
	data work.statistics;
		expression = "She only buys what she likes to eat.";
		first_vowel = findc(expression, "&vowels.");
		first_she = findw(expression, 'she');
		she_count = count(expression, 'she', 'i');
		vowels_count = countc(expression, "&vowels.");
		words_count = countw(expression);
	run;
```

<br>


https://github.com/DSmolke/SAS_Knowladge/assets/106284705/179edb32-de04-4e1b-aa34-c12021da37e3


  
- Retrieve previous values with the LAG function.


```sql

data work.cars;
	set sashelp.cars;
	prev_model = lag(model);
	two_models_before = lag2(model);
run;

proc sql;
	select model, prev_model, two_models_before
	from work.cars;
quit;
```

<br>


https://github.com/DSmolke/SAS_Knowladge/assets/106284705/43921a9e-d669-481c-b55b-c66fa5638a00


 
- Regular expression pattern matching with PRX functions*
- Metacharacters: ()[]{}*+?.|^$\d\D\s\S\w\W
- Functions and call routines: PRXMATCH, PRXPARSE, PRXCHANGE

```sql
data tmp;
	expression1 = "Agent007";
	expression2 = "double007";
	regex1 = prxparse('/^(A)gent\d{3}$/');
	
	does_match1 = prxmatch(regex1, expression1);
	does_match2 = prxmatch(regex1, expression2);
	
	expression3 = prxchange('s/(\D+)(\d+)/$2$1/', -1, expression1);
run;
```
</div>
