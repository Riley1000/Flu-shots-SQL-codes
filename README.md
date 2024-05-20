# Hospital Data Analysis

## Table of Contents

- [Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Tools](#tools)
- [Data Cleaning and Exploratory Data Analysis](#data-cleaning-and-exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Results and Findings](#results-and-findings)

### Project Overview

This is a Data analysis project that takes a dive into "Healthcare Analysis".
The management of a hospital (name withheld) needed information about only patients that had received Flu shots in 2022 and came up with the following tasks; "Come up with a flushots dashboard that shows/does the following
1- Total percentage of patients getting flu shots stratified by
 a.) Age
 b.) Race
 c.) County (using a map)
 d.) Overall
 
2- Running total of flu shots over the course of 2022 (by month)
3- Total number of flushots given in 2022
4- A list of patients that show whether or not they recieved the flu shots

NB: Patient must have been ACTIVE at the hospital for a period of 2 years and over 6 months of age"

### Data Sources 
The data used is from four .txt files titled "encounters", "Patients", "immunization", and "conditions". Final data (after SQL cleaning and analysis) can be found as "Flu_Shots_data.csv"

### Tools
- SQL server (Postgres/pgAdmin 4) - Data analysis
- Tableau - Dashboard creation/Viz

### Data Cleaning and Exploratory Data Analysis

### Data Analysis
The following lines of SQL code were used to obtain results that fulfilled the tasks above

``` SQL
with active_patients as
(
	select distinct patient
	from encounters as e
	join patients as pat
	on e.patient = pat.id
	where start between '2020-01-01 00:00' and '2022-12-31 23:59'
	and pat.deathdate is null
	and extract(month from age ('2022-12-31', pat.birthdate)) >= 6
),
	
flu_shot_2022 as
(
select patient, min(date) as earliest_flu_shot_2022
from immunizations 
where code = '5302'
	and date between '2022-01-01 00:00' and '2022-12-31 23:59'
	group by patient
)

select pat.first,
       pat.last,
	   pat.birthdate,
	   pat.race,
	   pat.county,
	   pat.id,
	   flu.earliest_flu_shot_2022,
	   flu.patient,
	   case when flu.patient is not null then 1
	   else 0
	   end as flu_shot_2022
from patients as pat
left join flu_shot_2022 as flu
       on pat.id = flu.patient
where 1=1
and pat.id in (select patient from active_patients)
```
### Results and Findings
Visit the [Tableau dashboard](https://public.tableau.com/shared/4M4BSP64T?:display_count=n&:origin=viz_share_link) to see results and key findings
