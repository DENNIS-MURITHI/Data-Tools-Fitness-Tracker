🏋️‍♂️ README.md
# Data-Tools

<div align="center">
  <img width="200" height="200" alt="Fitness Tracker Logo" src="https://github.com/user-attachments/assets/20661293-a214-4004-9042-657102fb0710" />
  <br/>
  <h2><b>Fitness Tracker Database Project</b></h2>
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
* [📊 ERD Diagram](#erd-diagram)
* [💾 Schema SQL](#schema-sql)
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

> The **Fitness Tracker Database** models how a health and fitness app stores user data, workout sessions, and progress logs.  
> It demonstrates database normalization, relationships, and analytical query design using PostgreSQL on **Supabase**.

---

## 🛠 Built With <a name="built-with"></a>

### Tech Stack <a name="tech-stack"></a>

<details>
  <summary>Database & Hosting</summary>
  <ul>
    <li><a href="https://supabase.com">Supabase (PostgreSQL)</a> – used to design schema, insert data, and run SQL queries</li>
  </ul>
</details>

<details>
  <summary>SQL Queries</summary>
  <ul>
    <li>Database schema creation, sample data, and analytical queries</li>
  </ul>
</details>

---

### Key Features <a name="key-features"></a>

* Tracks users, their workouts, and their physical progress.
* Demonstrates **one-to-many** relationships between users and workouts.
* Enables analytics like total calories burned and average workout durations.
* Includes complete schema and documentation for easy setup.

---

## 🚀 Live Demo <a name="live-demo"></a>

> This project is backend-only (database). You can view or interact with it via Supabase.

* [Supabase Project Link (Demo)](https://supabase.com/dashboard/project/fitness-tracker-demo/sql)

---

# 💻 Getting Started <a name="getting-started"></a>

### Prerequisites
* Supabase account  
* SQL client (Supabase SQL editor)

---

### Setup

Clone the repository:

```bash
git clone https://github.com/DENNIS-MURITHI/fitness-tracker-database.git
cd fitness-tracker-database
```

### Usage

Open Supabase → create a new project → go to SQL Editor.

Run the schema.sql file:

```sql
\i schema.sql
```

Execute example queries to explore user performance analytics.

---

📊 ERD Diagram <a name="erd-diagram"></a>

<div align="center">
  <img width="100%" height="480" alt="Fitness Tracker ERD" src="https://github.com/user-attachments/assets/ee605b4d-0928-4287-a5af-c7da767cfddd" />
</div>

**Entity Descriptions:**

- **Users** → base entity containing profile info.  
- **Workouts** → stores individual workout sessions.  
- **Progress Logs** → tracks body metrics and user changes.

---

💾 Schema SQL <a name="schema-sql"></a>

<details>
  <summary>Click to expand the full schema.sql</summary>

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

-- ===============================
-- SAMPLE DATA INSERTION
-- ===============================

INSERT INTO users (full_name, email, age, gender) VALUES
('Brian Otieno', 'brian.otieno@example.com', 26, 'Male'),
('Faith Njeri', 'faith.njeri@example.com', 30, 'Female'),
('Dennis Kiptoo', 'dennis.kiptoo@example.com', 24, 'Male'),
('Velma Wanjiku', 'velma.wanjiku@example.com', 27, 'Female'),
('George Mwangi', 'george.mwangi@example.com', 32, 'Male');

--  FIXED: Cast workout_date as DATE to prevent type error
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

--  FIXED: Cast recorded_date as DATE to prevent type error
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

-- ===============================
-- ANALYTICAL EXAMPLE QUERIES
-- ===============================

-- 1️ User Progress Summary
SELECT 
    u.full_name, 
    p.weight_kg, 
    p.body_fat_percentage, 
    p.recorded_date
FROM users u
JOIN progress_logs p ON u.id = p.user_id
ORDER BY p.recorded_date DESC;

-- 2️ Total Calories Burned by Each User
SELECT 
    u.full_name, 
    SUM(w.calories_burned) AS total_calories
FROM users u
JOIN workouts w ON u.id = w.user_id
GROUP BY u.full_name
ORDER BY total_calories DESC;

-- 3️ Average Workout Duration by Type
SELECT 
    workout_type, 
    ROUND(AVG(duration_minutes), 2) AS avg_duration
FROM workouts
GROUP BY workout_type
ORDER BY avg_duration DESC;

```
</details>

---

📖 Data Dictionary <a name="data-dictionary"></a>

📄 View Full File: **data_dictionary.md**

---

👥 Authors <a name="authors"></a>

👤 **Dennis Murithi**  
GitHub: [@DENNIS-MURITHI](https://github.com/DENNIS-MURITHI)  
LinkedIn: [Dennis Murithi](https://www.linkedin.com/in/dennis-murithi)

---

🔭 Future Features <a name="future-features"></a>

- Add `exercise_types` table for detailed exercise info  
- Integrate with Power BI dashboard for analytics  
- Implement leaderboard for total calories burned

---

🤝 Contributing <a name="contributing"></a>

Contributions and feedback are welcome!  
Feel free to fork the repo, make improvements, and submit a PR.

---

⭐️ Show your support <a name="support"></a>

If you like this project, give it a ⭐️ on GitHub!

---

🙏 Acknowledgements <a name="acknowledgements"></a>

- [Supabase](https://supabase.com) — for providing a free PostgreSQL platform.  
- [Draw.io](https://app.diagrams.net) — for ERD visualization.  
- [OpenAI GPT-5](https://openai.com) — for schema documentation assistance.

---

❓ FAQ <a name="faq"></a>

**How do I connect this to Power BI?**  
Export data using Supabase API or CSV and load into Power BI.

**Can I extend this schema?**  
Yes, you can add more entities like goals or trainers.

**What if my SQL import fails?**  
Ensure you run the schema in order: `users` → `workouts` → `progress_logs`.

---

📝 License <a name="license"></a>

This project is licensed under the **MIT License** — see the LICENSE file.
