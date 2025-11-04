🏋️‍♀️ README.md
# Data Fundamentals – Admin Roles & Security in Supabase

<div align="center">
  <img width="180" height="180" alt="Fitness Tracker Logo" src="https://github.com/user-attachments/assets/20661293-a214-4004-9042-657102fb0710" />
  <br/>
  <h2><b>Fitness Tracker: Admin Roles & Security Setup</b></h2>
</div>

---

# 📗 Table of Contents

* [📖 About the Project](#about-project)
* [🛠 Built With](#built-with)
* [⚙️ Roles & Security Policies](#roles)
* [🚀 Setup Instructions](#setup)
* [🔒 Example SQL Policies](#sql-policies)
* [🧩 Admin Function](#admin-function)
* [📊 ERD Reference](#erd)
* [📖 Security Notes](#security-notes)
* [👥 Author](#author)
* [📄 License](#license)

---

# 📖 About the Project <a name="about-project"></a>

> The **Fitness Tracker – Admin Roles & Security in Supabase** project demonstrates how to apply **Row Level Security (RLS)**, manage **Admin vs User roles**, and enforce the **Principle of Least Privilege** in a PostgreSQL (Supabase) database.  
> It builds on the base *Fitness Tracker Database* project by integrating real-world access control and secure SQL policies.

---

## 🛠 Built With <a name="built-with"></a>

### Tech Stack

<details>
  <summary>Database & Hosting</summary>
  <ul>
    <li><a href="https://supabase.com">Supabase (PostgreSQL)</a> — used for database design, authentication, and RLS enforcement.</li>
  </ul>
</details>

<details>
  <summary>Security Features</summary>
  <ul>
    <li>Supabase Auth for user management</li>
    <li>Row Level Security (RLS) and SQL Policies</li>
    <li>Admin-only SQL functions</li>
  </ul>
</details>

---

## ⚙️ Roles & Security Policies <a name="roles"></a>

| Role | Permissions | Description |
|------|--------------|-------------|
| **Admin** | Full Access | Can read, insert, update, and delete any record. |
| **User** | Limited Access | Can read and insert only their own data. |

> **RLS** is enabled for all tables, ensuring that each query respects the user’s identity (`auth.uid()`).

---

## 🚀 Setup Instructions <a name="setup"></a>

1. **Open Supabase Project**
   - Use your previous *Fitness Tracker* database as a base.

2. **Enable Row Level Security**
   - In Supabase → Table Editor → Security → Enable RLS for each table.

3. **Add User Roles**
   - In the `users` table, include a `role` column (`'admin'` or `'user'`).

4. **Apply SQL Policies**
   - Run the policies below in Supabase SQL Editor.

---

## 🔒 Example SQL Policies <a name="sql-policies"></a>

### 1️⃣ Users can view and manage only their own data
```sql
create policy "Users can view their own records"
on workouts
for select
using (auth.uid() = user_id);

create policy "Users can insert their own workouts"
on workouts
for insert
with check (auth.uid() = user_id);
2️⃣ Admins can access everything
sql
Copy code
create policy "Admins have full access"
on workouts
for all
using (
  exists (
    select 1 from users
    where id = auth.uid() and role = 'admin'
  )
);
3️⃣ Enable RLS for all tables
sql
Copy code
alter table users enable row level security;
alter table workouts enable row level security;
alter table progress_logs enable row level security;
🧩 Admin Function <a name="admin-function"></a>
Admins have a custom function to securely delete a workout record.

sql
Copy code
create or replace function delete_workout_by_admin(workout_id int)
returns void
language sql
security definer
as $$
  delete from workouts where id = workout_id;
$$;
The security definer ensures only admins can execute the function.

📊 ERD Reference <a name="erd"></a>
<div align="center"> <img width="100%" height="400" alt="ERD Diagram" src="https://github.com/user-attachments/assets/ee605b4d-0928-4287-a5af-c7da767cfddd" /> </div>
Entity Descriptions:

users → stores user profiles and roles

workouts → logs workout sessions

progress_logs → tracks physical progress

📖 Security Notes <a name="security-notes"></a>
🔐 Row Level Security (RLS)
All tables (users, workouts, progress_logs) have RLS enabled.
Each record is linked to the authenticated user's UID (auth.uid()).

👤 Authentication
Only authenticated users can access database resources via Supabase Auth.
Auth ensures each request includes a secure session token.

🧱 Role Separation
Admin → Has full CRUD privileges across all tables.

User → Restricted to personal records (based on UID).

🧩 Security Functions
Admin-only SQL functions use SECURITY DEFINER for privilege escalation.

Regular users cannot call these functions directly.

🧰 Security Practices
Avoid direct access to the Supabase REST API for admin tasks.

Use Policies, RLS, and Auth.uid() checks for data ownership validation.

Keep sensitive columns (e.g., roles, emails) hidden from non-admins.

🧪 Testing Recommendations
Create two test accounts — one Admin and one Regular User.

Insert sample data as each account.

Verify that:

Admin can view and delete all records.

User can only see and manage their own.

👥 Author <a name="author"></a>
👤 Velma
GitHub: @velma
Unit: Data Fundamentals – Admin Roles & Security in Supabase

📄 License <a name="license"></a>
This project is licensed under the MIT License.
