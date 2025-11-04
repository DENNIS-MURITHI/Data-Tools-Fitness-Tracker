# 🧠 Data Fundamentals – Admin Roles & Security in Supabase

<div align="center">
  <img width="180" height="180" alt="Fitness Tracker Logo" src="https://github.com/user-attachments/assets/20661293-a214-4004-9042-657102fb0710" />
  <br/>
  <h2><b>Fitness Tracker: Admin Roles & Security Setup</b></h2>
</div>

---

## 📗 Table of Contents

* [📖 About the Project](#about-project)
* [🛠 Built With](#built-with)
* [⚙️ Roles & Security Policies](#roles)
* [🚀 Setup Instructions](#setup)
* [🔒 Example SQL Policies](#sql-policies)
* [🧩 Admin Function](#admin-function)
* [📊 ERD Reference](#erd)
* [👥 Author](#author)
* [📄 License](#license)

---

## 📖 About the Project <a name="about-project"></a>

The **Fitness Tracker – Admin Roles & Security in Supabase** project demonstrates how to apply **Row Level Security (RLS)**, manage **Admin vs User roles**, and enforce the **Principle of Least Privilege** in a PostgreSQL (Supabase) database.  
It builds on the base *Fitness Tracker Database* project by integrating real-world access control and secure SQL policies.

---

## 🛠 Built With <a name="built-with"></a>

- **Supabase (PostgreSQL)** — database hosting and authentication  
- **SQL Policies** — for row-level access control  
- **Supabase Auth** — for managing users and sessions  

---

## ⚙️ Roles & Security Policies <a name="roles"></a>

| Role | Permissions | Description |
|------|--------------|-------------|
| **Admin** | Full Access | Can read, insert, update, and delete any record. |
| **User** | Limited Access | Can read and insert only their own data. |

Row Level Security (RLS) is **enabled on all tables**, and Supabase authentication ensures that every request is made by a verified, logged-in user.

---

## 🚀 Setup Instructions <a name="setup"></a>

1. **Open Supabase Project**  
   Use your existing *Fitness Tracker* database or create a new Supabase project.

2. **Enable RLS**  
   Navigate to:  
   **Table Editor → Security → Enable RLS** for each table.

3. **Define Roles**  
   Add a `role` column in the `users` table to distinguish between `'admin'` and `'user'`.

4. **Apply SQL Policies**  
   Paste the SQL examples below into the Supabase SQL Editor.

5. **Test Access**  
   Test CRUD actions as both a user and an admin using Supabase authentication.

---

## 🔒 Example SQL Policies <a name="sql-policies"></a>

### 1. Users can view and manage only their own data

```sql
CREATE POLICY "Users can view their own records"
ON workouts
FOR SELECT
USING (auth.uid() = user_id);

CREATE POLICY "Users can insert their own workouts"
ON workouts
FOR INSERT
WITH CHECK (auth.uid() = user_id);
```

### 2. Admins can access everything

```sql
CREATE POLICY "Admins have full access"
ON workouts
FOR ALL
USING (
  EXISTS (
    SELECT 1 FROM users
    WHERE id = auth.uid() AND role = 'admin'
  )
);
```

### 3. Enable RLS on all tables

```sql
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
ALTER TABLE workouts ENABLE ROW LEVEL SECURITY;
ALTER TABLE progress_logs ENABLE ROW LEVEL SECURITY;
```

---

## 🧩 Admin Function <a name="admin-function"></a>

Admins have access to a special SQL function that allows secure deletion of workout records.

```sql
CREATE OR REPLACE FUNCTION delete_workout_by_admin(workout_id INT)
RETURNS VOID
LANGUAGE SQL
SECURITY DEFINER
AS $$
  DELETE FROM workouts WHERE id = workout_id;
$$;
```

The **`SECURITY DEFINER`** clause ensures that the function runs with admin privileges only.

---

## 📊 ERD Reference <a name="erd"></a>

![ERD Diagram](https://github.com/user-attachments/assets/ee605b4d-0928-4287-a5af-c7da767cfddd)

**Entities Overview:**

- **users** → stores user profiles and roles  
- **workouts** → logs user workout sessions  
- **progress_logs** → tracks progress over time  

---

## 👥 Author <a name="author"></a>

**👤 Velma**  
*Data Fundamentals – Admin Roles & Security in Supabase*  
GitHub: [@velma](https://github.com/velma)

---

## 📄 License <a name="license"></a>

This project is licensed under the **MIT License**.  
You are free to use, modify, and distribute it for educational or personal purposes.

---
