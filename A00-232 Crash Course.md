# SAS_Knowladge

#### Środowisko SAS OnDemand for Academics dostępne dla wszystkich https://welcome.oda.sas.com/



# A00-232 Exam

#### Pełny opis wymaganych zagadnień https://www.sas.com/en_us/certification/exam-content-guides/advanced-programmer-sas94.html

#### Opis zbiorów sashelp https://support.sas.com/documentation/tools/sashelpug.pdf


##Część 1 - Accessing Data Using SQL (35%)

### Moduł 1 - Generate detail reports by working with a single table, joining tables, or using set operators in SQL



https://github.com/DSmolke/SAS_Knowladge/assets/106284705/24cf1498-791d-4d2d-9074-0fc5711edc1e



- Use PROC SQL to perform SQL queries.
- Select columns in a table with a SELECT statement and FROM clause.
- Create a table from a query result set.

```
    proc sql;
        create table baseball_1 as
        select name, team, natbat from sashelp.baseball;
    quit;
```



https://github.com/DSmolke/SAS_Knowladge/assets/106284705/2a64e53f-7d01-4f70-ae67-5c72da0b7652



- Create new calculated columns
- Assign an alias with the AS keyword

```
    proc sql;
        select name, (crhome / cratbat) * 100 as HomeRunsPercentage
        from sashelp.baseball;
    quit;
```



https://github.com/DSmolke/SAS_Knowladge/assets/106284705/25b09645-66c7-48a2-843e-4e34e4ae2b5c



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
        where crAtBat < 200;
        
    quit;

```



https://github.com/DSmolke/SAS_Knowladge/assets/106284705/8548f525-d632-42dd-be1c-a58f51c562a3



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






https://github.com/DSmolke/SAS_Knowladge/assets/106284705/47785274-c372-432c-837b-bc40569d3b48





```
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



https://github.com/DSmolke/SAS_Knowladge/assets/106284705/e405d151-e1d9-480a-a9bf-8e916b52778b



```
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



https://github.com/DSmolke/SAS_Knowladge/assets/106284705/7fada16c-d33b-42a0-b395-8caeb3dbaa9d



```
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

- Combine tables using set operators - union, outer union, except, intersect.
https://support.sas.com/documentation/cdl/en/sqlproc/63043/HTML/default/viewer.htm#n0vo2lglyrnexwn14emi8m0jqvrj.htm
(błędna formuła dokumentacji)



https://github.com/DSmolke/SAS_Knowladge/assets/106284705/293d961a-5aa8-47e8-97f7-61c4ed892bf1



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

title 'Union All (all set 1 values followed by all set 2 values)';
proc sql;
	select * from set1
	union all
	select * from set2;
quit;
```




https://github.com/DSmolke/SAS_Knowladge/assets/106284705/2e850c61-8e5c-4e27-b7be-f384facbaa36





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



https://github.com/DSmolke/SAS_Knowladge/assets/106284705/bc38d1bf-4a8e-4c1f-a4ab-7ff154d5042a



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



https://github.com/DSmolke/SAS_Knowladge/assets/106284705/e66a9b3c-9169-42ac-b714-d9095b6fda94



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



https://github.com/DSmolke/SAS_Knowladge/assets/106284705/15240043-0392-4fe5-876f-473414b637a4



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



https://github.com/DSmolke/SAS_Knowladge/assets/106284705/67232200-aa89-4871-b17e-c960f23db10f



- Subset data by using non-correlated subqueries.
- Reference an in-line view with other views or tables (multiple tables). 

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

### Moduł 4 -  Use SAS SQL procedure enhancements.


https://github.com/DSmolke/SAS_Knowladge/assets/106284705/7100e6ac-a7dc-4428-bc62-6a2036f38f4d


- Use SAS data set options with PROC SQL (KEEP=, DROP=, RENAME=, OBS=).

```
    proc sql;
	    select * from sashelp.cars(keep=make model msrp where=(msrp > 15000) rename=(make=Brand));
    quit;
```



https://github.com/DSmolke/SAS_Knowladge/assets/106284705/0d3d36a2-180f-45df-ba6f-cb42efb6d7ab



- Use PROC SQL invocation options (INOBS=, OUTOBS=, NUMBER)
```
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


https://github.com/DSmolke/SAS_Knowladge/assets/106284705/e16125c7-1ff4-4a8c-a676-43917f6cc667


- Use PROC SQL invocation options (NOPRINT)
invoke - wywołać

```
    proc sql /*noprint*/;
        select * from sashelp.cars;
    quit;

```


https://github.com/DSmolke/SAS_Knowladge/assets/106284705/d3e08018-417a-45bb-8906-d0acd86dbaf4



- Use SAS functions (SCAN, SUBSTR, LENGTH)

```
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




https://github.com/DSmolke/SAS_Knowladge/assets/106284705/397015f9-9e73-4246-bff1-356a8b6f1c61



- Access SAS system information by using DICTIONARY tables (members, tables, 
columns)

```
    proc sql feedback;
        select *
        from dictionary.columns
        where libname = 'SASHELP' and memname = 'CARS';
    quit;

```

-  Use the CALCULATED keyword.

```
    proc sql;
        select *, msrp + 1000 as inflation_msrp
        from sashelp.cars
        where calculated inflation_msrp > 35000;
    quit;
```

### Część 2 - Macro Processing (35%)

## Moduł 1 - Create and use user-defined and automatic macro variables within the SAS Macro Language.



https://github.com/DSmolke/SAS_Knowladge/assets/106284705/1631fa5c-9ee8-47c6-864e-47cd0566a715



- Define and use macro variables.
- Use macro variable name delimiter. (.)

```
    %let country = Poland;
    %put &country;
    %put &country.&country;
```



https://github.com/DSmolke/SAS_Knowladge/assets/106284705/024fae83-a7c7-41f6-b494-dbe4b85b75c7



- Use INTO clause of the SELECT statement in SQL to create a single variable or a list of variables. 

```
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



https://github.com/DSmolke/SAS_Knowladge/assets/106284705/7cfb251c-2d65-4991-b3b2-d319d7f6cca6



- Use the SYMPUTX routine in a DATA Step to create a single variable or a list of variables.

```
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



https://github.com/DSmolke/SAS_Knowladge/assets/106284705/1b5efe14-0265-4d1b-ba72-7217a57e7e5b



- Control variable scope with:
    - %GLOBAL statement 
    - %LOCAL statement
    - SYMPUTX scope parameter

```
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

### Moduł 2 - Automate programs by defining and calling macros using the SAS Macro Language.



https://github.com/DSmolke/SAS_Knowladge/assets/106284705/153f539c-5600-4dea-97e2-60ff18b904da



- Define a macro using the %MACRO and %MEND statements.
- Calling a macro with and without parameters.
- Document macro functionality with comments

```
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



https://github.com/DSmolke/SAS_Knowladge/assets/106284705/f595aca5-298b-49d7-8556-d4ba059e7772



-  Generate SAS Code conditionally by using the %IF-%THEN-%ELSE macro statements or iterative %DO statements.

```
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

- Use the SAS AUTOCALL facility to permanently store and call macros
https://support.sas.com/documentation/cdl/en/mcrolref/61885/HTML/default/viewer.htm#a001328769.htm

```
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

### Moduł 3 - Use macro functions.

- Use macro functions. (%SCAN, %SUBSTR, %UPCASE)


```
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

- Use macro quoting functions. (%NRSTR, %STR)
- Use macro evaluation functions. (%SYSEVALF)
- Use %SYSFUNC to execute DATA step functions within the SAS Macro Language.

```
    %put some ; epic ; statement; /* Error */
    %put %str(some ; epic ; statement;);
    %put %str(%some ; &epic ; &&statement;); /* Warning */
    %put %nrstr(%some ; &epic ; &&statement;);


    %put NOTE: syseval_result = %sysevalf(10 + 10 + 100);
    %put NOTE: sysfunc_result = %sysfunc(round(100.99, 1));
    %put NOTE: sysfunc_result_formated = %sysfunc(round(100.99, 1), dollar32.);
    %put NOTE: sysfunc_result_formated_trimed = %trim(%sysfunc(round(100.99, 1), dollar32.));
```

### Moduł 4 - Debug macros

- Trace the flow of execution with the MLOGIC option. 
- Examine the generated SAS statements with the MPRINT option.
- Examine macro variable resolution with the SYMBOLGEN option.
- Use the %PUT statement to print information to the log

https://www.diffchecker.com/text-compare/

Obskoczyć po kolei różnice w logu na diff toolu po dodaniu kolejnej opcji
```
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

### Moduł 5 - Create data-driven programs using SAS Macro Language.

• Create a series of macro variables.
• Use indirect reference to macro variables. (&&, etc.)
• Generate repetitive macro calls.

```tsql
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

- Incorporate DICTONARY tables in data driven macros.

```tsql
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

## Część - Advanced Techniques (30%)

### Moduł 1 - Process data using 1 and 2 dimensional arrays

- Define and use character arrays.
- Define and use numeric arrays
- Create variables with arrays

```tsql
    data work.tmp;
    set sashelp.cars;
/* 	array CO2_emition {2} fuel electric (0.432, 0); */
    array CO2_emition {2} _TEMPORARY_ (0.432, 0);
    
    if (electric = 0) then total_emition = horsepower * CO2_emition{1};
    else total_emition = horsepower * CO2_emition{2};
/* 	array test{4} _TEMPORARY_ (90 80 70 70); */
    run;
```
