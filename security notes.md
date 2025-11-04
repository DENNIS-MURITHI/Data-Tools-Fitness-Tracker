# 🔒 Security Notes – Fitness Tracker Admin Roles & Supabase Policies

<div align="center">
  <img width="180" height="180" alt="Security Logo" src="https://github.com/user-attachments/assets/20661293-a214-4004-9042-657102fb0710" />
  <br/>
  <h2><b>Data Fundamentals Project – Admin Roles & Security in Supabase</b></h2>
</div>

---

## 🧭 Overview

This document summarizes the **security model**, **role permissions**, and **policies** implemented in the **Fitness Tracker Supabase database**.  
It focuses on enforcing **Row Level Security (RLS)**, managing **Admin/User roles**, and applying the **Principle of Least Privilege** across all tables.

---

## ⚙️ Security Architecture

| Component | Description |
|------------|-------------|
| **Database** | PostgreSQL hosted on Supabase |
| **Authentication** | Supabase Auth (Email/Password) |
| **Authorization** | Custom SQL policies (RLS) |
| **Roles** | Admin, User |
| **Access Model** | Role-based access + RLS enforcement |

RLS ensures that **users can only interact with their own records**, while **admins** have unrestricted access.

---

## 👥 Roles & Permissions

| Role | Access Type | Privileges |
|------|--------------|-------------|
| **Admin** | Full Access | Can select, insert, update, delete any record |
| **User** | Limited Access | Can select and insert only their own records |

---

## 🧩 Row Level Security (RLS)

RLS was **enabled on all core tables** in the Fitness Tracker schema:

```sql
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
ALTER TABLE workouts ENABLE ROW LEVEL SECURITY;
ALTER TABLE progress_logs ENABLE ROW LEVEL SECURITY;
```

Each table has **policies** controlling how Admins and Users can access data.

---

## 📜 Policy Examples

### 1. User Policies

```sql
-- Users can view only their own records
CREATE POLICY "Users can view their own records"
ON workouts
FOR SELECT
USING (auth.uid() = user_id);

-- Users can insert their own workout logs
CREATE POLICY "Users can insert their own workouts"
ON workouts
FOR INSERT
WITH CHECK (auth.uid() = user_id);
```

### 2. Admin Policies

```sql
-- Admins have full control over all data
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

---

## 🧠 Additional Security Features

### 🔑 Authentication
Supabase Auth is configured to require **email/password** sign-ins for all users.  
Unauthenticated users are **denied access** by default.

### 🧰 Admin Function (Secure Operation)
The following SQL function allows Admins to delete a workout securely:

```sql
CREATE OR REPLACE FUNCTION delete_workout_by_admin(workout_id INT)
RETURNS VOID
LANGUAGE SQL
SECURITY DEFINER
AS $$
  DELETE FROM workouts WHERE id = workout_id;
$$;
```

The `SECURITY DEFINER` clause ensures that only an authenticated admin can execute this function.

---

## 🧩 Security Testing Checklist

| Check | Description | Status |
|-------|--------------|--------|
| ✅ | RLS enabled on all tables | ✔️ Done |
| ✅ | Admin and User roles defined | ✔️ Done |
| ✅ | Authentication configured | ✔️ Done |
| ✅ | Policies tested via SQL Editor | ✔️ Done |
| ⚙️ | Error handling for unauthorized actions | Pending (handled at API level) |

---

## 🚀 Implementation Steps Summary

1. Enable RLS on all tables  
2. Define `users` table with role column  
3. Create `admin` and `user` role-based policies  
4. Test access using Supabase Auth sessions  
5. Add secure admin-only SQL function  

---

## 🔐 Best Practices

- Never disable RLS once active.  
- Keep policies specific and least-privileged.  
- Always test policies using different user roles.  
- Use `SECURITY DEFINER` only for trusted admin functions.  
- Keep Supabase keys (anon, service) secret.

---

## 🧾 Audit and Monitoring

- **Database logs**: Track who modifies records.  
- **Policy checks**: Regularly review RLS policies for misconfigurations.  
- **Function execution logs**: Audit usage of admin-only functions.

---

## 👩‍💻 Author

**Velma**  
Data Fundamentals – Admin Roles & Security in Supabase  
GitHub: [@velma](https://github.com/velma)

---

## 📄 License

This project follows the **MIT License**.  
Feel free to modify or extend for educational purposes.
