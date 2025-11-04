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

**Users can view only their own tasks:**

```sql
CREATE POLICY "Users can view their own tasks"
ON tasks
FOR SELECT
USING (auth.uid() = user_id);
```

**Admins have full access to all tasks:**

```sql
CREATE POLICY "Admins have full access to tasks"
ON tasks
FOR ALL
USING (
  EXISTS (
    SELECT 1 FROM users
    WHERE id = auth.uid() AND role = 'admin'
  )
);
```

These policies enforce that regular users can only see their own data, while admins can manage everything.

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
CREATE OR REPLACE FUNCTION delete_project(project_id UUID)
RETURNS VOID
LANGUAGE SQL
SECURITY DEFINER
AS $$
  DELETE FROM projects WHERE id = project_id;
$$;
```

> The `SECURITY DEFINER` clause ensures that the function runs with the privileges of its creator (admin only).

---

## 🧩 Example ERD Reference

![ERD Diagram](https://github.com/user-attachments/assets/ee605b4d-0928-4287-a5af-c7da767cfddd)

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
