# 📘 Data Dictionary — Fitness Tracker Database Project

This data dictionary provides a detailed definition of all tables, columns, data types, and relationships in the **Fitness Tracker Database** implemented on **Supabase (PostgreSQL)**. It ensures transparency, consistency, and integrity across the database architecture.

---

## 🧩 Table: `users`

| **Column Name** | **Data Type** | **Description** | **Example Value** |
|-----------------|----------------|-----------------|------------------|
| `id` | `UUID` | Unique identifier for each user (auto-generated). | `0d8c86bb-05a7-4b2d-9f5d-3a947eabc123` |
| `full_name` | `VARCHAR(100)` | Full name of the user. | `Grace Wambui` |
| `email` | `VARCHAR(100)` | User’s unique email address for identification. | `grace@example.com` |
| `age` | `INT` | User’s age; must be greater than zero. | `29` |
| `gender` | `VARCHAR(10)` | User’s gender or identity label. | `Female` |
| `created_at` | `TIMESTAMP` | Date and time when the user account was created. | `2025-11-04 10:32:15` |

---

## 🏋️ Table: `workouts`

| **Column Name** | **Data Type** | **Description** | **Example Value** |
|-----------------|----------------|-----------------|------------------|
| `id` | `SERIAL` | Primary key for each workout session. | `1` |
| `user_id` | `UUID` | Foreign key linking to `users(id)`; defines who performed the workout. | `0d8c86bb-05a7-4b2d-9f5d-3a947eabc123` |
| `workout_type` | `VARCHAR(50)` | The type of exercise performed. | `HIIT` |
| `duration_minutes` | `INT` | Total duration of the workout in minutes. | `45` |
| `calories_burned` | `INT` | Total calories expended during the workout. | `350` |
| `workout_date` | `DATE` | The date the workout occurred. | `2025-11-04` |

**Relationships:**  
- Each user can have multiple workouts (`1-to-many` relationship).  
- Deletion of a user cascades to all related workouts.

---

## 📈 Table: `progress_logs`

| **Column Name** | **Data Type** | **Description** | **Example Value** |
|-----------------|----------------|-----------------|------------------|
| `id` | `SERIAL` | Primary key for each progress log entry. | `1` |
| `user_id` | `UUID` | Foreign key linking to `users(id)`; defines who recorded progress. | `0d8c86bb-05a7-4b2d-9f5d-3a947eabc123` |
| `weight_kg` | `DECIMAL(5,2)` | The user’s body weight in kilograms. | `72.45` |
| `body_fat_percentage` | `DECIMAL(5,2)` | Body fat percentage at the time of record. | `19.85` |
| `recorded_date` | `DATE` | Date when the measurement was taken. | `2025-11-04` |

**Relationships:**  
- Each user can have multiple progress logs (`1-to-many`).  
- Designed to track trends over time for analytics.

---

## 📊 Analytical Metrics & Power BI Integration

| **Metric** | **Description** | **Data Source** | **Example Insight** |
|-------------|----------------|------------------|--------------------|
| `Total Calories Burned` | Sum of all calories per user. | `workouts` | John Doe → 3,200 kcal |
| `Average Workout Duration` | Average exercise length grouped by workout type. | `workouts` | Yoga → 55 minutes |
| `Weight Progress Over Time` | Trend of user’s body weight using time-series data. | `progress_logs` | Grace Wambui → −2.6 kg over 4 weeks |
| `Body Fat Improvement` | Percentage reduction in fat per user. | `progress_logs` | Ali Hassan → −3.2 % |
| `User Engagement` | Count of active users in a given period. | `users`, `workouts` | 5 active users (Nov 2025) |

---

## 📊 Power BI Dashboard Preview  

> *(Placeholder — replace with your exported dashboard image once analytics are created)*  

<div align="center">
  <img width="90%" height="480" alt="Power BI Dashboard Preview" src="https://github.com/user-attachments/assets/placeholder_powerbi_dashboard.png" />
</div>

