# Data-Analysis

<div align="center">
  <img width="200" height="200" alt="Data Analysis Logo" src="https://github.com/user-attachments/assets/20661293-a214-4004-9042-657102fb0710" />
  <br/>
  <h2><b>Data Analysis Project with RStudio Cloud & Supabase</b></h2>
</div>

# 📗 Table of Contents

* [📖 About the Project](#about-project)
  * [🛠 Built With](#built-with)
  * [Key Features](#key-features)
  * [🚀 Live Demo](#live-demo)
* [💻 Getting Started](#getting-started)
  * [Prerequisites](#prerequisites)
  * [Setup](#setup)
  * [Usage](#usage)
  * [Connecting from Posit to Supabase](#posit-supabase-connection)
* [💾 Schema SQL](#schema-sql)
* [📊 R Data Analysis](#r-data-analysis)
* [📖 Data Dictionary](#data-dictionary)
* [👥 Authors](#authors)
* [🔭 Future Features](#future-features)
* [🤝 Contributing](#contributing)
* [⭐️ Show your support](#support)
* [🙏 Acknowledgements](#acknowledgements)
* [❓ FAQ](#faq)
* [📝 License](#license)

---

# 📖 About the Project <a name="about-project"></a>

> This project is a 4-week independent data analysis exercise where students work on a unique dataset using **RStudio Cloud (Posit)** and **Supabase (PostgreSQL)**.  
> The goal is to practice database querying, data manipulation, exploratory data analysis (EDA), and visualization using `ggplot2`.

### Detailed Overview

1. **Database Setup**  
   Students reuse their **Data Tools project database** with 3+ tables, each having at least 5 rows. Relationships are enforced using foreign keys.

2. **Data Insertion**  
   Sample data is added to simulate real-world scenarios.

3. **R Analysis**  
   Use RStudio Cloud to connect to Supabase, query the database, and manipulate data with `dplyr`.

4. **Visualization**  
   Generate meaningful visual insights using `ggplot2` (bar charts, pie charts, histograms).

5. **Documentation**  
   Maintain reproducibility using `README.md`, `data_dictionary.md`, and R scripts.

### Key Objectives

* Connect R to Supabase/Postgres  
* Perform **EDA** with R  
* Create visual insights using `ggplot2`  
* Document database schema, queries, and analysis process  

---

## 🛠 Built With <a name="built-with"></a>

### Tech Stack

<details>
  <summary>Database & Hosting</summary>
  <ul>
    <li><a href="https://supabase.com">Supabase (PostgreSQL)</a> – backend database for tables, relationships, and sample data.</li>
  </ul>
</details>

<details>
  <summary>R Data Analysis</summary>
  <ul>
    <li><a href="https://posit.cloud/">Posit / RStudio Cloud</a> – online IDE for connecting to Supabase, performing queries, and generating visualizations.</li>
    <li>R Packages: <code>DBI</code>, <code>RPostgres</code>, <code>dplyr</code>, <code>ggplot2</code></li>
  </ul>
</details>

---

### Key Features <a name="key-features"></a>

* Relational database with **users, workouts, progress_logs** tables  
* Clear **foreign key relationships**  
* **R-based analysis**: grouping, summarizing, filtering, and visualization  
* Visual insights using **bar charts, pie charts, histograms**  

---

## 🚀 Live Demo <a name="live-demo"></a>

> Backend-only demo. Run SQL queries directly in Supabase.

* [Supabase Project Link](https://supabase.com/dashboard/project)

---

# 💻 Getting Started <a name="getting-started"></a>

### Prerequisites

* Supabase account  
* Posit / RStudio Cloud  
* R packages: `DBI`, `RPostgres`, `dplyr`, `ggplot2`  

---

### Setup

Clone the repository:

```bash
git clone https://github.com/YOUR_USERNAME/data-analysis-project.git
cd data-analysis-project
```
##Usage
- Open Supabase and create a new project
- Execute the schema file:

```sql

\i schema.sql
Connect from RStudio Cloud:
```

```r
source("connect_db.R")
con <- connect_db()
df <- dbGetQuery(con, "SELECT * FROM users;")
head(df)
```
Connecting from Posit to Supabase <a name="posit-supabase-connection"></a>

```r

install.packages(c("DBI", "RPostgres", "dplyr", "ggplot2"))
```
```r
library(DBI)

connect_db <- function() {
  dbConnect(
    RPostgres::Postgres(),
    dbname = "postgres",
    host = "your_project.supabase.co",
    port = 5432,
    user = "postgres",
    password = "your_password",
    sslmode = "require"
  )
}

con <- connect_db()
dbListTables(con)
```

## Successful coneection to supabase project

<img width="1366" height="629" alt="image" src="https://github.com/user-attachments/assets/2f68d7c1-0e4d-4857-8621-d61d6653bfe4" />


## 💾 Schema SQL <a name="schema-sql"></a>

<details>
  <summary>Click to expand full schema.sql</summary>

```sql
-- USERS TABLE
CREATE TABLE users (
    id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
    full_name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    age INT CHECK (age > 0),
    gender VARCHAR(10),
    created_at TIMESTAMP DEFAULT NOW()
);

-- WORKOUTS TABLE
CREATE TABLE workouts (
    id SERIAL PRIMARY KEY,
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    workout_type VARCHAR(50) NOT NULL,
    duration_minutes INT CHECK (duration_minutes > 0),
    calories_burned INT CHECK (calories_burned >= 0),
    workout_date DATE DEFAULT CURRENT_DATE
);

-- PROGRESS LOGS TABLE
CREATE TABLE progress_logs (
    id SERIAL PRIMARY KEY,
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    weight_kg DECIMAL(5,2),
    body_fat_percentage DECIMAL(5,2),
    recorded_date DATE DEFAULT CURRENT_DATE
);

-- SAMPLE DATA
INSERT INTO users (full_name, email, age, gender) VALUES
('Brian Otieno', 'brian.otieno@example.com', 26, 'Male'),
('Faith Njeri', 'faith.njeri@example.com', 30, 'Female'),
('Dennis Kiptoo', 'dennis.kiptoo@example.com', 24, 'Male'),
('Velma Wanjiku', 'velma.wanjiku@example.com', 27, 'Female'),
('George Mwangi', 'george.mwangi@example.com', 32, 'Male');

INSERT INTO workouts (user_id, workout_type, duration_minutes, calories_burned, workout_date)
SELECT u.id, temp.workout_type, temp.duration, temp.calories, temp.workout_date::date
FROM (VALUES
    ('Brian Otieno', 'Cardio', 50, 320, '2025-11-01'),
    ('Faith Njeri', 'Yoga', 60, 280, '2025-11-02'),
    ('Dennis Kiptoo', 'Strength', 45, 390, '2025-11-03'),
    ('Velma Wanjiku', 'HIIT', 35, 360, '2025-11-04'),
    ('George Mwangi', 'Cycling', 40, 330, '2025-11-05')
) AS temp(name, workout_type, duration, calories, workout_date)
JOIN users u ON u.full_name = temp.name;

INSERT INTO progress_logs (user_id, weight_kg, body_fat_percentage, recorded_date)
SELECT u.id, temp.weight, temp.fat, temp.recorded_date::date
FROM (VALUES
    ('Brian Otieno', 78.5, 17.9, '2025-11-01'),
    ('Faith Njeri', 63.2, 21.4, '2025-11-02'),
    ('Dennis Kiptoo', 72.6, 19.8, '2025-11-03'),
    ('Velma Wanjiku', 59.0, 20.3, '2025-11-04'),
    ('George Mwangi', 81.7, 22.5, '2025-11-05')
) AS temp(name, weight, fat, recorded_date)
JOIN users u ON u.full_name = temp.name;
```
</details>
---

## 📊 R Data Analysis <a name="r-data-analysis"></a>
<details><summary>Click to expand full R analysis code</summary></details>

```r 
source("connect_db.R")
library(DBI)
library(dplyr)
library(ggplot2)

con <- connect_db()

# 1. User Progress Summary
progress <- dbGetQuery(con, "
  SELECT u.full_name, p.weight_kg, p.body_fat_percentage, p.recorded_date
  FROM users u
  JOIN progress_logs p ON u.id = p.user_id
  ORDER BY p.recorded_date DESC;
")
head(progress)

# 2. Total Calories Burned per User
calories <- dbGetQuery(con, "
  SELECT u.full_name, SUM(w.calories_burned) AS total_calories
  FROM users u
  JOIN workouts w ON u.id = w.user_id
  GROUP BY u.full_name
  ORDER BY total_calories DESC;")

ggplot(calories, aes(x=reorder(full_name, total_calories), y=total_calories, fill=full_name)) +
  geom_col(show.legend=FALSE) +
  coord_flip() +
  theme_minimal() +
  labs(title="Total Calories Burned per User", x="User", y="Calories Burned") ```
  
---
## Output and visualizations in posit
**  User Progress Summary **
```r
progress <- dbGetQuery(con, "
  SELECT u.full_name, p.weight_kg, p.body_fat_percentage, p.recorded_date
  FROM users u
  JOIN progress_logs p ON u.id = p.user_id
  ORDER BY p.recorded_date DESC;
")
head(progress)
```
** Visual for weight progress over time **
```r
library(ggplot2)

ggplot(progress, aes(x = recorded_date, y = weight_kg, color = full_name, group = full_name)) +
  geom_line(size = 1) +
  geom_point(size = 2) +
  theme_minimal() +
  labs(
    title = "📉 Weight Progress Over Time",
    x = "Date Recorded",
    y = "Weight (kg)",
    color = "User"
  )

```
<img width="1366" height="638" alt="image" src="https://github.com/user-attachments/assets/00f3ee5a-a702-4b47-8d76-10c7c312dc63" />

** Latest Weight Per user **
```r
library(dplyr)
library(ggplot2)

# Get the most recent record for each user
latest_progress <- progress %>%
  group_by(full_name) %>%
  slice_max(recorded_date, n = 1)

# Bar chart of latest weights
ggplot(latest_progress, aes(x = reorder(full_name, weight_kg), y = weight_kg, fill = full_name)) +
  geom_col(show.legend = FALSE) +
  coord_flip() +
  theme_minimal() +
  labs(
    title = "🏋️ Latest Recorded Weight per User",
    x = "User",
    y = "Weight (kg)"
  )
```

<img width="1366" height="634" alt="image" src="https://github.com/user-attachments/assets/02122938-5bff-491e-83c7-8405d7f706c9" />

## 👥 Authors <a name="authors"></a>
**Dennis Murithi**  
GitHub: [@dennismurithi](https://github.com/dennismurithi)  
LinkedIn: [LinkedIn Profile](https://www.linkedin.com/in/dennis-muthuri/)

---

## 🔭 Future Features <a name="future-features"></a>
- Add analytics for progress trends  
- Connect to Power BI dashboards  
- Include more workout types and user goals  

---

## 🤝 Contributing <a name="contributing"></a>
Contributions are welcome. Fork the repo, create a branch, and submit a pull request.

---

## ⭐️ Show your support <a name="support"></a>
If you like this project, give it a ⭐️ on GitHub!

---

## 🙏 Acknowledgements <a name="acknowledgements"></a>
- [Supabase](https://supabase.com/) – PostgreSQL hosting and testing  
- [Posit](https://posit.cloud/) – RStudio Cloud environment  

---

## ❓ FAQ <a name="faq"></a>
**1. How do I run this project in Posit?**  
Open the repository in Posit, install dependencies, and run the R scripts step by step.

**2. What dependencies are needed?**  
```r
install.packages(c("DBI", "RPostgres", "dplyr", "ggplot2"))
