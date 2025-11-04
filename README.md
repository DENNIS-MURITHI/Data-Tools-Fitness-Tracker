# 🧠 Data Fundamentals Project: Admin Roles & Security in Supabase

<div align="center">
  <img width="200" height="200" alt="Supabase Logo" src="https://github.com/user-attachments/assets/20661293-a214-4004-9042-657102fb0710" />
  <br/>
  <h2><b>Data Fundamentals – Admin Roles & Security Implementation</b></h2>
</div>

---

## 📖 Overview

This project is part of the **Data Tools** unit, designed to help learners implement secure role-based access control using **Supabase (PostgreSQL)**.

The main objectives are to:
- Define and manage **Admin** and **User** roles  
- Apply **Row Level Security (RLS)** and SQL **Policies**
- Enforce the **Principle of Least Privilege**
- Secure database functions with **role-based permissions**
- Document and deploy through **GitHub**

---

## 🏗 Project Scheme

| Component | Description |
|------------|-------------|
| **Database** | Supabase PostgreSQL with at least 3 tables and 5 rows each (from Data Tools final project) |
| **Roles** | Admin and Regular User |
| **Security** | Row Level Security (RLS) enabled on all tables |
| **Policies** | SQL policies for read, insert, update, and delete based on role |
| **Documentation** | README.md + security_notes.md |
| **Submission** | GitHub Pull Request to lecturer’s repository |

---

## ⚙️ Database Setup

1. Use your **existing Supabase project** from the Data Tools Final Project.  
2. Enable **Row Level Security (RLS)** for all tables.

```sql
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
ALTER TABLE workouts ENABLE ROW LEVEL SECURITY;
ALTER TABLE progress_logs ENABLE ROW LEVEL SECURITY;
```

3. Confirm RLS is active in the Supabase dashboard under **Table Editor → Security**.

---

## 👥 Roles Definition

Add a `role` column to the `users` table to distinguish between admin and standard users.

```sql
ALTER TABLE users ADD COLUMN role TEXT CHECK (role IN ('admin', 'user')) DEFAULT 'user';
```

| Role | Permissions |
|------|--------------|
| **Admin** | Full access (read, insert, update, delete) |
| **User** | Restricted access (read and insert only their own data) |

---

## 🔒 Example Policies

**Users can view only their own workouts:**

```sql
-- Users can view only their own workouts
CREATE POLICY "Users can view their own workouts"
ON workouts
FOR SELECT
USING (auth.uid() = user_id);

-- Users can insert their own workouts
CREATE POLICY "Users can insert their own workouts"
ON workouts
FOR INSERT
WITH CHECK (auth.uid() = user_id);
```
## You can alter table to implement roles

```sql
ALTER TABLE users
ADD COLUMN role VARCHAR(20) DEFAULT 'user';

INSERT INTO users (full_name, email, age, gender, role) VALUES
('James Mwangi', 'james.mwangi@example.com', 33, 'Male', 'admin'),
('Ryan Otieno', 'ryan.otieno@example.com', 24, 'Male', 'user');
```
**Admins have full access to all workouts:**

```sql
-- Admins have full access to all workouts
CREATE POLICY "Admins have full access to workouts"
ON workouts
FOR ALL
USING (
  EXISTS (
    SELECT 1 FROM users
    WHERE id = auth.uid() AND role = 'admin'
  )
);
```

These policies enforce that regular users can only see their own data, while admins can manage everything.

## Test queries for roles

```sql
-- 1️⃣ Setup: create sample projects
INSERT INTO projects (id, name, owner_id) VALUES
(gen_random_uuid(), 'Project Alpha', auth.uid()),
(gen_random_uuid(), 'Project Beta', auth.uid());

-- 2️⃣ Verify projects exist
SELECT * FROM projects;

-- 3️⃣ Attempt deletion as a regular user
-- Assume auth.uid() is a non-admin user
SELECT delete_project(project_id)
FROM projects
WHERE name = 'Project Alpha';

-- Check if the project was deleted
SELECT * FROM projects;
-- ❌ Project Alpha should still exist for non-admin

-- 4️⃣ Attempt deletion as an admin
-- Simulate login as an admin (auth.uid() must be an admin)
SELECT delete_project(project_id)
FROM projects
WHERE name = 'Project Alpha';

-- Verify deletion
SELECT * FROM projects;
-- ✅ Project Alpha should now be deleted

-- 5️⃣ Optional: Check audit logging (if implemented)
SELECT * FROM admin_audit_log
ORDER BY executed_at DESC;

```

---

## 🔐 Authentication Setup

Enable user authentication with **Supabase Auth** (Email/Password or Magic Link).

**Steps:**
1. In the Supabase dashboard, navigate to **Authentication → Providers**.  
2. Enable **Email** or **Magic Link** authentication.  
3. Ensure only **authenticated users** can access database resources.

---

## ⚡ Custom Admin Function

Create a SQL function that can only be executed by an admin:

```sql
-- Ensure the function executes with the privileges of its creator (SECURITY DEFINER)
-- and can only be called by an admin user.

CREATE OR REPLACE FUNCTION delete_project(project_id UUID)
RETURNS VOID
LANGUAGE SQL
SECURITY DEFINER
AS $$
  -- Only allow deletion if the current user is an admin
  DELETE FROM projects
  WHERE id = project_id
    AND EXISTS (
      SELECT 1 FROM users
      WHERE id = auth.uid()
        AND role = 'admin'
    );
$$;

```

> The `SECURITY DEFINER` clause ensures that the function runs with the privileges of its creator (admin only).

---

**Entities Overview**
- **users** → stores user information and role  
- **workouts** → logs activities per user  
- **progress_logs** → tracks user performance or body metrics  

---

## 🧠 Example Policy Recap

| Table | Policy | Access |
|--------|--------|--------|
| `users` | Authenticated users can only see their record | `auth.uid() = id` |
| `workouts` | Admins can view all, users only their own | `role = 'admin' OR auth.uid() = user_id` |
| `progress_logs` | Restricted to user who owns the data | `auth.uid() = user_id` |

---

## ✅ Deliverables

- Supabase project with 3+ tables and populated data  
- Admin and User roles implemented  
- RLS and SQL policies applied  
- At least one custom admin-only function  
- Proper documentation (`README.md` + `security_notes.md`)  
- GitHub repository with Pull Request submission  

---

## 👥 Author

**👤 Velma**  
*Data Fundamentals – Admin Roles & Security in Supabase*  
GitHub: [@velma](https://github.com/velma)

---

## 📄 License

This project is licensed under the **MIT License**.  
You are free to use, modify, and share for learning or research purposes.

---
