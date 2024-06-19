# The data will be analyzed in year 2022 in California

# 1. Number of victims by relationship
```
SELECT nr.relationship_name, count(distinct nv.victim_id) as victim_count FROM nibrs_victim_offender_rel nvor
join nibrs_relationship nr on nvor.relationship_id = nr.relationship_id
join nibrs_offender nio on nvor.offender_id = nio.offender_id
join nibrs_victim nv on nvor.victim_id = nv.victim_id
group by nr.relationship_name
order by victim_count desc
;
```
#### Output
![Screen Shot 2024-06-17 at 8 16 25 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/12a3a6cb-3cc5-4982-8766-488bd8a0496f)

-> The majority of the relationship are unknowns or strangers, if they are known it is most likely the victims are offenders' boyfriends/girlfriends, acquaintance, or otherwise known.

# 2. Number of victims by gender
```
SELECT nv.sex_code, count(distinct nv.victim_id) as victim_count FROM nibrs_victim nv
group by nv.sex_code
order by victim_count desc
;
```
#### Output
![Screen Shot 2024-06-17 at 8 21 27 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/5dfb37aa-0870-49df-bacb-f91c4923a958)

-> Most of the victims are males, followed by females. There are also X genders as well, while a small number of 13344 victims are unknown/unidentified.

# 3. Number of victims by race
```
SELECT rr.race_desc, count(distinct nv.victim_id) as victim_count FROM nibrs_victim nv
join ref_race rr on rr.race_id = nv.race_id
group by rr.race_desc
order by victim_count desc
;
```
#### Output
![Screen Shot 2024-06-17 at 8 25 49 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/fefc4ab3-9b4f-4ae4-a078-42f981002673)

-> Most of the victims are whites, and a lot more are not specified or unknown, followed by Asian victims. 

# 4. Number of victims by victim type
```
SELECT nvt.victim_type_name, count(distinct nv.victim_id) as victim_count FROM nibrs_victim nv
join nibrs_victim_type nvt on nv.victim_type_id = nvt.victim_type_id
group by nvt.victim_type_name
order by victim_count desc
;
```
#### Output
![Screen Shot 2024-06-17 at 8 28 07 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/c0d2dd8d-a647-4bc4-96e1-31ddd7855434)

-> A lot of the victims are individuals, business, and society/public. Other victims spread out and diverse in the type of victims as well.

# 5. Number of victims by assignment type
```
SELECT nat.assignment_type_name, nvt.victim_type_name, count(distinct nv.victim_id) as victim_count FROM nibrs_victim nv
join nibrs_assignment_type nat on nv.assignment_type_id = nat.assignment_type_id
join nibrs_victim_type nvt on nv.victim_type_id = nvt.victim_type_id
group by nat.assignment_type_name, nvt.victim_type_name
order by victim_count desc
;
```
#### Output
![Screen Shot 2024-06-17 at 8 31 22 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/2c6d0c1b-5fec-4257-aee1-1f96e0c078cb)

-> Among the victims that are law enforcement officers, lots of them died while on assignment alone or assisted, and they are on duty as one officer. Hence, it seems there's more risks doing activities with only one officer. 

# 6. Number of victims by activity type
```
SELECT nat.activity_type_name, nvt.victim_type_name, count(distinct nv.victim_id) as victim_count FROM nibrs_victim nv
join nibrs_activity_type nat on nv.activity_type_id = nat.activity_type_id
join nibrs_victim_type nvt on nv.victim_type_id = nvt.victim_type_id
group by nat.activity_type_name, nvt.victim_type_name
order by victim_count desc
;
```
#### Output
![Screen Shot 2024-06-17 at 8 39 16 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/1dabbad2-950d-463d-bb4c-7b2dadf25f5b)

-> Most officers were victims while responding to disturbance call, attempting other arrests, or handling custody of prisoners. Other activities are present as well and less common.

# 7. What were the circumstances and justifiable force for killing the criminals during arrest? 
```
### circumstances
SELECT nc.circumstances_name, count(distinct nv.victim_id) as victim_count FROM nibrs_victim nv
join nibrs_victim_circumstances nvc on nv.victim_id = nvc.victim_id
join nibrs_circumstances nc on nvc.circumstances_id = nc.circumstances_id
group by nc.circumstances_name
order by victim_count desc
;

### justifiable force
SELECT nf.justifiable_force_name, count(distinct nv.victim_id) as victim_count FROM nibrs_victim nv
join nibrs_victim_circumstances nvc on nv.victim_id = nvc.victim_id
join nibrs_justifiable_force nf on nf.justifiable_force_id = nvc.justifiable_force_id
group by nf.justifiable_force_name
order by victim_count desc
;
```
#### Output
![Screen Shot 2024-06-17 at 8 45 02 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/b2639316-e056-4787-8eb1-7c74c75b17e6)
![Screen Shot 2024-06-17 at 8 51 58 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/22765bb0-d05c-476f-8c19-9cc9bcf141ce)

-> Under special circumstances, criminals were killed by either private citizen or police officer, namely because the criminals posed a danger during the incident.

# 8. How many offenses per offense category?
```
select niot.offense_category_name, count(distinct nio.offense_id) as offense_count from nibrs_offense nio
join nibrs_offense_type niot on nio.offense_code = niot.offense_code
group by niot.offense_category_name
order by offense_count desc
;
```
#### Output
![Screen Shot 2024-06-17 at 8 55 39 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/84d4efa8-65db-4e85-bcff-a71e6ce34231)

-> Most of the offenses are theft offenses, assault offenses, and offenses involved drugs. It's interesting to see that Motor Vehicle Theft is a theft offense as well but it has its own seperate category.

# 9. Number of incidents by California county
```
select a.county_name, count(distinct ni.incident_id) as incident_count from agencies a
join nibrs_incident ni on a.agency_id = ni.agency_id
group by a.county_name
order by incident_count desc
;
```
#### Output 
![Screen Shot 2024-06-17 at 9 02 18 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/48f17145-8224-4674-b785-cfbcee7bb662)

-> Look at the top, we can see that San Diego has the highest number of incidents compared to other counties, it could possibly be because of its close proximity to the border with nearby countries. 

# 10. Number of incidents per offense category
```
select niot.offense_category_name, count(distinct ni.incident_id) as incident_count from nibrs_incident ni
join nibrs_offense nio on nio.incident_id = ni.incident_id
join nibrs_offense_type niot on niot.offense_code = nio.offense_code
group by niot.offense_category_name
order by incident_count desc
;
```
#### Output
![Screen Shot 2024-06-17 at 9 06 18 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/6728ced3-0ce1-45f9-89d6-bd99ecb5e041)

-> Theft offenses top the list. The statistics are slightly different compared to number of offenses per offense category because there could be multiple offenses per one incident. Hence, in one vandalism incident for example, there could be several drug offenses involved. 

# 11 Average number of offenses per incident?
```
with cte as (SELECT incident_id, count(distinct offense_id) as offense_count FROM nibrs_offense
group by incident_id
order by offense_count desc)
select round(avg(offense_count), 2) as average_offense_number from cte
;
```
#### Output
![Screen Shot 2024-06-17 at 9 14 20 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/4d09e96a-51e4-4a0b-a73b-9231e283b493)

-> On average, there is typically one offense committed in each incident.

# 12. What's the most common number of offenses committed per incident?
```
with cte as (SELECT incident_id, count(distinct offense_id) as offense_count FROM nibrs_offense
group by incident_id
order by offense_count desc)
select offense_count, count(offense_count) as offense_count from cte
group by offense_count
order by offense_count desc
;
```
#### Output
![Screen Shot 2024-06-17 at 9 20 28 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/3a50c2dd-694c-4855-88d6-a3b03a894770)

-> Typically, there's one offense in each incident, while there are 3 incidents with maximum 8 offenses each.

# 13. Injuries sustained by victims
```
select ni.injury_name, count(distinct nvj.victim_id) as victim_count from nibrs_victim_injury nvj
join nibrs_injury ni on nvj.injury_id = ni.injury_id
group by ni.injury_name
order by victim_count desc
;
```
#### Output
![Screen Shot 2024-06-17 at 9 25 18 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/400d1923-4774-487c-af79-846e4b207c8d)

-> The majority of victims sustained none or minor injuries.

# 14. Any weapons used in each incident?
```
select nwt.weapon_name, count(distinct nio.incident_id) as incident_count from nibrs_weapon nw
join nibrs_weapon_type nwt on nw.weapon_id = nwt.weapon_id
join nibrs_offense nio on nio.offense_id = nw.offense_id
group by nwt.weapon_name
order by incident_count desc
;
```
#### Output
![Screen Shot 2024-06-17 at 9 27 42 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/75d3595f-3816-47e1-80aa-057b83e32691)

-> A variety of weapons were used, most of them were personal weapons or other types of weapons. 

# 15. Any bias if there's no injury? 
```
select nbl.bias_name, count(distinct nvj.victim_id) as offense_count from nibrs_bias_list nbl
join nibrs_bias_motivation nbm on nbl.bias_id = nbm.bias_id
join nibrs_offense nio on nio.offense_id = nbm.offense_id
join nibrs_victim nv on nv.incident_id = nio.incident_id
join nibrs_victim_injury nvj on nvj.victim_id = nv.victim_id
join nibrs_injury ni on ni.injury_id = nvj.injury_id
where ni.injury_name = 'None'
group by nbl.bias_name
order by offense_count desc
;
```
#### Output
![Screen Shot 2024-06-17 at 9 30 35 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/7addbccf-e695-4969-bf1a-68843676acd6)

-> It's rare for a bias to occur, and bias is subjective so it's hard to classify. Hence, the majority of biases are none or unknown. 

# 16. Age of offenders and victims
```
# victim count
select DISTINCT
    CASE
        when age_num = 'NS' then 'Not Specified'
        when age_num = 'BB' then '7-364 Days Old'
        when age_num = 'NB' then '1-6 Days Old'
        when age_num = 'NN' then 'Under 24 Hours'
        when age_num = 00 then 'Unknown'
        when age_num = '99' then 'Over 98 Years Old'
        WHEN age_num < 20 THEN 'Under 20'
        WHEN age_num BETWEEN 20 AND 29 THEN '20-29'
        WHEN age_num BETWEEN 30 AND 39 THEN '30-39'
        WHEN age_num BETWEEN 40 AND 49 THEN '40-49'
        WHEN age_num BETWEEN 50 AND 59 THEN '50-59'
        WHEN age_num >= 60 THEN '60 and above'
    END Age_Group, count(distinct victim_id) as victim_count from nibrs_victim
group by Age_group
order by victim_count desc;

# offender count
select DISTINCT
    CASE
        when age_num = 'NS' then 'Not Specified'
        when age_num = 'BB' then '7-364 Days Old'
        when age_num = 'NB' then '1-6 Days Old'
        when age_num = 'NN' then 'Under 24 Hours'
        when age_num = 00 then 'Unknown'
        when age_num = '99' then 'Over 98 Years Old'
        WHEN age_num < 20 THEN 'Under 20'
        WHEN age_num BETWEEN 20 AND 29 THEN '20-29'
        WHEN age_num BETWEEN 30 AND 39 THEN '30-39'
        WHEN age_num BETWEEN 40 AND 49 THEN '40-49'
        WHEN age_num BETWEEN 50 AND 59 THEN '50-59'
        WHEN age_num >= 60 THEN '60 and above'
    END Age_Group, count(distinct offender_id) as offender_count from nibrs_offender
group by Age_group
order by offender_count desc;
```
#### Output
![Screen Shot 2024-06-17 at 9 35 02 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/14c9a9b3-c5ac-47a6-9092-345c9904a73e)
![Screen Shot 2024-06-17 at 9 35 38 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/f0130ea5-7729-4b57-9d78-db2d639ebd2b)

-> Age of victims and offenders are not specified most of the time. If their age were specified, the age range was most likely in the range 20 to 49.
    
# 17. Victims that are less than 7 days old, what weapons were used against them? 
```
with cte as (select DISTINCT
    CASE
        when age_num = 'NS' then 'Not Specified'
        when age_num = 'BB' then '7-364 Days Old'
        when age_num = 'NB' then '1-6 Days Old'
        when age_num = 'NN' then 'Under 24 Hours'
        when age_num = 00 then 'Unknown'
        when age_num = '99' then 'Over 98 Years Old'
        WHEN age_num < 20 THEN 'Under 20'
        WHEN age_num BETWEEN 20 AND 29 THEN '20-29'
        WHEN age_num BETWEEN 30 AND 39 THEN '30-39'
        WHEN age_num BETWEEN 40 AND 49 THEN '40-49'
        WHEN age_num BETWEEN 50 AND 59 THEN '50-59'
        WHEN age_num >= 60 THEN '60 and above'
    END Age_Group, victim_id from nibrs_victim)
select cte.Age_Group, nwt.weapon_name, count(distinct cte.victim_id) as victim_count from cte 
join nibrs_victim_offense nvo on cte.victim_id = nvo.victim_id
join nibrs_weapon nw on nw.offense_id = nvo.offense_id
join nibrs_weapon_type nwt on nwt.weapon_id = nw.weapon_id
where Age_Group in ('1-6 Days Old', 'Under 24 Hours', '7-364 Days Old')
group by cte.Age_Group, nwt.weapon_name
order by victim_count desc
;
```
#### Output
![Screen Shot 2024-06-17 at 9 45 11 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/647c8cb9-4292-446f-946f-e855790e9cc9)

-> Most weapons were personal weapons 

# 18. Victims that are less than 7 days old, what injuries did they sustain? 
```
with cte as (select DISTINCT
    CASE
        when age_num = 'NS' then 'Not Specified'
        when age_num = 'BB' then '7-364 Days Old'
        when age_num = 'NB' then '1-6 Days Old'
        when age_num = 'NN' then 'Under 24 Hours'
        when age_num = 00 then 'Unknown'
        when age_num = '99' then 'Over 98 Years Old'
        WHEN age_num < 20 THEN 'Under 20'
        WHEN age_num BETWEEN 20 AND 29 THEN '20-29'
        WHEN age_num BETWEEN 30 AND 39 THEN '30-39'
        WHEN age_num BETWEEN 40 AND 49 THEN '40-49'
        WHEN age_num BETWEEN 50 AND 59 THEN '50-59'
        WHEN age_num >= 60 THEN '60 and above'
    END Age_Group, victim_id from nibrs_victim)
select cte.Age_Group, ni.injury_name, count(distinct cte.victim_id) as victim_count from cte 
join nibrs_victim_injury nvi on cte.victim_id = nvi.victim_id
join nibrs_injury ni on ni.injury_id = nvi.injury_id
where Age_Group in ('1-6 Days Old', 'Under 24 Hours', '7-364 Days Old')
group by cte.Age_Group, ni.injury_name
order by victim_count desc
;
```
#### Output
![Screen Shot 2024-06-17 at 9 47 31 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/25a2775d-af6c-4987-af79-e81ea4757c61)

-> Most of them had no injuries.

# 19. Location of the crime
```
select nlt.location_name, count(distinct nio.offense_id) as offense_count from nibrs_offense nio
join nibrs_location_type nlt on nio.location_id = nlt.location_id
group by nlt.location_name 
order by offense_count desc
;
```
#### Output
![Screen Shot 2024-06-17 at 9 49 24 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/431d38c0-3a23-41dc-b856-d37fa7d962ac)

-> Look at the top 3, most crimes took place at a residence, on the street, and parking areas.

# 20. Number of incidents and arrests by month 
```
# incidents
select date_format(incident_date, '%m') as converted_month, count(distinct incident_id) as incident_count from nibrs_incident
group by converted_month
order by incident_count desc
;

# arrests
select date_format(arrest_date, '%m') as converted_month, count(distinct arrestee_id) as arrestee_count from nibrs_arrestee
group by converted_month
order by arrestee_count desc
;
```
#### Output
![Screen Shot 2024-06-17 at 9 52 18 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/6c8c4dc4-dbe6-4c2f-9787-5790d5195386)
![Screen Shot 2024-06-17 at 9 52 30 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/92bad808-5b41-4f03-910f-6f7047a4085d)

-> Interestingly, most incidents and arrests made were found in the second half of the year which reached its peak in the month of October and December. It could be because there were more holidays and summer seasons during this time period, hence resulting in more crimes committed since there were more human interactions.  

# 21. On average per offense, county, race, and gender, how long does it take to submit the report since the incident and arrest date? 
# How long does it take to arrest the offender since the incident? 
# per offense
```
select niot.offense_name, round(avg(datediff(date(ni.submission_date), date(ni.incident_date))), 2) 
as avg_days_submitted_since_the_incident, 
round(avg(datediff(date(ni.submission_date), date(na.arrest_date))), 2) as avg_days_submitted_since_the_arrest, 
round(avg(datediff(date(na.arrest_date), date(ni.incident_date))), 2) as avg_days_arrested_since_the_incident
from nibrs_incident ni
join nibrs_arrestee na on ni.incident_id = na.incident_id
join nibrs_offense_type niot on niot.offense_code = na.offense_code
group by niot.offense_name
order by niot.offense_name 
;
```
#### Output
![Screen Shot 2024-06-17 at 10 02 38 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/a8d62ede-cc5c-415d-9da1-14bcda6ff4dd)

-> On average, Welfare Fraud took the longest to arrest the criminals since the incident. Human Trafficking took the longest to submit the report since the incident. Negligent Manslaughter took the longest to submit the report since the arrest. The more complicated a crime is, the more likely reports took longer to prepare and submit. Difficult and hard-to-track cases or require long investigations also took long time to arrest since the incident as well.

# per county
```
select a.county_name, round(avg(datediff(date(ni.submission_date), date(ni.incident_date))), 2) 
as avg_days_submitted_since_the_incident, 
round(avg(datediff(date(ni.submission_date), date(na.arrest_date))), 2) as avg_days_submitted_since_the_arrest, 
round(avg(datediff(date(na.arrest_date), date(ni.incident_date))), 2) as avg_days_arrested_since_the_incident
from nibrs_incident ni
join nibrs_arrestee na on ni.incident_id = na.incident_id
join agencies a on a.agency_id = ni.agency_id
group by a.county_name
order by a.county_name
;
```
#### Output
![Screen Shot 2024-06-17 at 10 14 18 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/886687bf-ae4c-4219-8300-24783908a630)

-> SAN JOAQUIN county took the longest to make the arrest since the incident. CALAVERAS county took the longest to submit the reports since the incident and arrest. 

# per race
```
select rr.race_desc, round(avg(datediff(date(ni.submission_date), date(ni.incident_date))), 2) 
as avg_days_submitted_since_the_incident, 
round(avg(datediff(date(ni.submission_date), date(na.arrest_date))), 2) as avg_days_submitted_since_the_arrest, 
round(avg(datediff(date(na.arrest_date), date(ni.incident_date))), 2) as avg_days_arrested_since_the_incident
from nibrs_incident ni
join nibrs_arrestee na on ni.incident_id = na.incident_id
join ref_race rr on rr.race_id = na.race_id
group by rr.race_desc
order by rr.race_desc
;
```
#### Output
![Screen Shot 2024-06-17 at 10 21 46 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/9fcdf200-4517-4952-a318-a32fb7c2b1a0)

-> Native Hawaiian or Other Pacific Islander took the longest to arrest since the incident. American Indian or Alaska Native took the longest to submit the reports since the incident and arrest, followed by Native Hawaiian or Other Pacific Islander. It could be because they live in tribal community or hard-to-reach places so it takes time to track the cases.

# per gender
```
select na.sex_code, round(avg(datediff(date(ni.submission_date), date(ni.incident_date))), 2) 
as avg_days_submitted_since_the_incident, 
round(avg(datediff(date(ni.submission_date), date(na.arrest_date))), 2) as avg_days_submitted_since_the_arrest, 
round(avg(datediff(date(na.arrest_date), date(ni.incident_date))), 2) as avg_days_arrested_since_the_incident
from nibrs_incident ni
join nibrs_arrestee na on ni.incident_id = na.incident_id
group by na.sex_code
order by na.sex_code
;
```
#### Output
![Screen Shot 2024-06-17 at 10 26 04 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/eab875cc-dcf4-4685-9c2b-2c6da59dacf0)

-> On average, male criminals took the longest to arrest and submit the reports compared to female criminals.

# 22. Number of offenses by month and offense category
```
select date_format(ni.incident_date, '%m') as converted_month, niot.offense_category_name, count(distinct nio.offense_id) as offense_count 
from nibrs_incident ni 
join nibrs_offense nio on nio.incident_id = ni.incident_id 
join nibrs_offense_type niot on niot.offense_code = nio.offense_code
group by converted_month, niot.offense_category_name
order by offense_count desc
limit 10
;
```
#### Output
![Screen Shot 2024-06-17 at 10 29 16 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/b1f5ed14-376f-4ea5-bc3e-d2abd8d98c02)

-> Look at the top 10, we can see that most of the offenses were theft offenses and took place in the second half of the year. 

# 23. Why some counties took longer to submit and arrest? Let's find out statistics related to suburban area, total officers, and total civilian by county.

## On average, how many counties took longer than 100 days to submit and longer than 5 days to arrest if the area is suburban or not? 
### took longer than 100 days to submit
```
with cte as (select a.county_name, a.suburban_area_flag, round(avg(datediff(date(ni.submission_date), date(ni.incident_date))), 2) 
as avg_days_submitted_since_the_incident, 
round(avg(datediff(date(ni.submission_date), date(na.arrest_date))), 2) as avg_days_submitted_since_the_arrest, 
round(avg(datediff(date(na.arrest_date), date(ni.incident_date))), 2) as avg_days_arrested_since_the_incident
from nibrs_incident ni
join nibrs_arrestee na on ni.incident_id = na.incident_id
join agencies a on a.agency_id = ni.agency_id
group by a.county_name, a.suburban_area_flag
order by a.county_name)
select suburban_area_flag, count(suburban_area_flag) as counties_count from cte 
where avg_days_submitted_since_the_incident > 100 and avg_days_submitted_since_the_arrest > 100 
group by suburban_area_flag
order by counties_count desc
;
```
#### Output
![Screen Shot 2024-06-18 at 5 00 35 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/bf93d1ad-2223-4132-ab70-e6f5e83a3ecd)

### took longer than 100 days to submit and 5 days to arrest
```
with cte as (select a.county_name, a.suburban_area_flag, round(avg(datediff(date(ni.submission_date), date(ni.incident_date))), 2) 
as avg_days_submitted_since_the_incident, 
round(avg(datediff(date(ni.submission_date), date(na.arrest_date))), 2) as avg_days_submitted_since_the_arrest, 
round(avg(datediff(date(na.arrest_date), date(ni.incident_date))), 2) as avg_days_arrested_since_the_incident
from nibrs_incident ni
join nibrs_arrestee na on ni.incident_id = na.incident_id
join agencies a on a.agency_id = ni.agency_id
group by a.county_name, a.suburban_area_flag
order by a.county_name)
select suburban_area_flag, count(suburban_area_flag) as counties_count from cte 
where avg_days_submitted_since_the_incident > 100 and avg_days_submitted_since_the_arrest > 100 
and avg_days_arrested_since_the_incident > 5
group by suburban_area_flag
order by counties_count desc
;
```
#### Output
![Screen Shot 2024-06-18 at 5 01 23 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/c8813329-98ca-4174-abed-bc737929d908)

-> On average, if the area is not suburban, it is likely that the days submitted and arrested took longer than if the area is suburban. While this insight helps us discover facts regarding suburban areas, it still has not solved the mystery of why some counties such as Santa Clara took longer to submit and arrest even if the area is suburban. So let's look at other metrics 

### total officer/total civilian ratio per county and the correlation
```
with cte2 as (with cte1 as (with cte as (select county_name, sum(male_officer) as total_male_officer_count, sum(male_civilian) as total_male_civilian_count, 
sum(male_total) as total_male_count, sum(female_officer) as total_female_officer_count, sum(female_civilian) as total_female_civilian_count, 
sum(female_total) as total_female_count
from agencies
group by county_name)
select county_name, total_male_officer_count + total_female_officer_count as total_officer_count, total_male_civilian_count + 
total_female_civilian_count as total_civilian_count
from cte
order by county_name)
select county_name, round(total_officer_count/total_civilian_count, 2) as officer_to_civilian_ratio from cte1),
cte3 as (select a.county_name, round(avg(datediff(date(ni.submission_date), date(ni.incident_date))), 2) 
as avg_days_submitted_since_the_incident, 
round(avg(datediff(date(ni.submission_date), date(na.arrest_date))), 2) as avg_days_submitted_since_the_arrest, 
round(avg(datediff(date(na.arrest_date), date(ni.incident_date))), 2) as avg_days_arrested_since_the_incident
from nibrs_incident ni
join nibrs_arrestee na on ni.incident_id = na.incident_id
join agencies a on a.agency_id = ni.agency_id
group by a.county_name
order by a.county_name)
select cte2.county_name, cte2.officer_to_civilian_ratio, cte3.avg_days_submitted_since_the_incident, 
cte3.avg_days_submitted_since_the_arrest, cte3.avg_days_arrested_since_the_incident from cte2
join cte3 on cte2.county_name = cte3.county_name
;
```
#### Output
![Screen Shot 2024-06-18 at 5 02 54 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/a037d627-3a8f-4442-b3b2-794364f1fde7)


## correlation
### officer_to_civilian_ratio and avg_days_submitted_since_the_incident
```
select @ax := avg(officer_to_civilian_ratio), 
       @ay := avg(avg_days_submitted_since_the_incident), 
       @div := (stddev_samp(officer_to_civilian_ratio) * stddev_samp(avg_days_submitted_since_the_incident))
from officer_to_civilian_ratio_per_county;

select sum((officer_to_civilian_ratio - @ax) * (avg_days_submitted_since_the_incident - @ay) )/((count(officer_to_civilian_ratio) -1) * @div) 
as correlation
from officer_to_civilian_ratio_per_county;
```
#### Output
![Screen Shot 2024-06-18 at 5 03 38 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/2a4c8c53-0805-42f9-b262-3307b86b541b)

-> They have a weak negative relationship

### officer_to_civilian_ratio and avg_days_submitted_since_the_arrest
```
select @ax := avg(officer_to_civilian_ratio), 
       @ay := avg(avg_days_submitted_since_the_arrest), 
       @div := (stddev_samp(officer_to_civilian_ratio) * stddev_samp(avg_days_submitted_since_the_arrest))
from officer_to_civilian_ratio_per_county;

select sum((officer_to_civilian_ratio - @ax) * (avg_days_submitted_since_the_arrest - @ay) )/((count(officer_to_civilian_ratio) -1) * @div) 
as correlation
from officer_to_civilian_ratio_per_county;
```
#### Output
![Screen Shot 2024-06-18 at 5 04 40 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/ec1c34fc-2d39-415f-bb00-bde5b555ec83)

-> They have a weak negative relationship

### officer_to_civilian_ratio and avg_days_arrested_since_the_incident
```
select @ax := avg(officer_to_civilian_ratio), 
       @ay := avg(avg_days_arrested_since_the_incident), 
       @div := (stddev_samp(officer_to_civilian_ratio) * stddev_samp(avg_days_arrested_since_the_incident))
from officer_to_civilian_ratio_per_county;

select sum((officer_to_civilian_ratio - @ax) * (avg_days_arrested_since_the_incident - @ay) )/((count(officer_to_civilian_ratio) -1) * @div) 
as correlation
from officer_to_civilian_ratio_per_county;
```
#### Output
![Screen Shot 2024-06-18 at 5 05 21 PM](https://github.com/quocduyenanhnguyen/California-Crime-Data-Analysis/assets/92205707/a542e187-0abe-4a44-bc04-f2db5592347e)

-> They have a weak positive relationship

->> In conclusion, the ratio has nothing to do with the processing time of submission and arrest. So it seems it is hard to find out exactly reasons why some counties took longer other than the reasons related to suburban areas explained above.


