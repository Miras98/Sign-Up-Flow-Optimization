# **Sign-Up Flow Analysis**

##  **Project Overview**
This project analyzes the **sign-up flow** of users for a platform using **MySQL**. 
The goal is to extract meaningful insights regarding user behavior, sign-up errors, and conversion rates. 

**The dataset consists of five key tables:** 

- **`actions`** – Tracks user actions throughout the sign-up process.  
- **`error_messages`** – Logs any errors encountered during the sign-up process.  
- **`sessions`** – Records user sessions, helping to track user engagement.  
- **`students`** – Contains details of registered users.  
- **`students_purchases`** – Captures purchase activities of registered users.  
- **`visitors`** – Contains information on visitors landing on the platform.

---
## **Objective**
The primary objective of this analysis is to understand and optimize the sign-up process by:
- Analyzing **conversion rates** from visitor to registered user.
- Identifying the **number of registered users** over time.
- Investigating **sign-up errors** and their frequency.
- Categorizing the **types of sign-up errors** affecting user registration.

---
## **Tech Stack**
- **Database**: MySQL
- **Query Language**: SQL
- **Visualization**: Tableau
---
## Business Questions:
1- What is the total number of vistitors, registered users, and free registered users?
```sql
use signup_flow;
with total_visitors as(
select 
    v.visitor_id, 
    v.first_visit_date,
    s.date_registered as registration_date,
    p.purchase_date
from
    visitors as v
left join
    students as s on v.user_id = s.user_id
left  join
    student_purchases as p on v.user_id = p.user_id
group by visitor_id
    ),
count_visitors as 
(
select 
    first_visit_date as date_session ,
    count(*) as count_total_visitors
from 
    total_visitors
group by date_session
    ),
count_registered as 
(
	select first_visit_date as date_session ,
    count(*) as count_registered 
    from 
    total_visitors
    where registration_date is not null
    group by date_session
    ), count_registered_free as 
     (
    select first_visit_date as date_session ,
    count(*) as count_registered_free
    from 
    total_visitors
    where registration_date is not null
    and(purchase_date is null
    or timestampdiff(minute, registration_date , purchase_date) > 30)
    group by date_session
    )

select
v.date_session as date_session,
v.count_total_visitors,
ifnull(r.count_registered, 0) as count_registered ,
ifnull(fr.count_registered_free, 0) as free_registered_users
from 
count_visitors v
left join
count_registered r on v.date_session = r.date_session
left join 
count_registered_free fr on v.date_session = fr.date_session
where v.date_session <'2023-02-01'
order by v.date_session ;
```
## 📊 **Key Insights** (To Be Updated)
- The **conversion rate** from visitors to registered users is **X%**.
- The total number of **registered users** over the observed period is **Y**.
- The most frequent **sign-up error** encountered is **Z**, accounting for **W%** of total errors.
- The primary cause of failed sign-ups is **[Error Type]**, which suggests improvements in the user onboarding process.

---

## 📁 **Project Structure**
```
📂 Sign-Up Flow Analysis
 ├── 📜 README.md (Project Documentation)
 ├── 📂 SQL Queries (Contains SQL scripts for analysis)
 ├── 📊 Reports (Visualizations & Key Insights)
 ├── 📂 Data (If sample data is available)
```

