# SQL Login Investigation Lab

A small hands-on lab where I use SQL to investigate login logs and find a suspicious account takeover, the same kind of thing a SOC analyst does when looking through authentication logs.

> **Work in progress.** I'm learning SQL and updating this lab as I go.

## The scenario

A small company has six user accounts and a log of every login attempt over two days. Each attempt records who tried to log in, when, from which IP address and country, and whether it worked.

Somewhere in those logs, something isn't right. The goal is to use SQL queries (not scrolling through by eye) to find out:

- Which account was targeted?
- Where did the attack come from?
- Did the attacker get in?
- Which other logins look unusual, and which ones only *look* scary but are actually normal?

## The database

Two tables:

**`users`**: the people (and service accounts) in the company

| Column | Meaning |
|---|---|
| `user_id` | the person's ID number (stays the same, it's not a row counter) |
| `username` | their login name |
| `department` | which department they work in |
| `is_admin` | `1` = admin account, `0` = normal account |

**`logins`**: every login attempt

| Column | Meaning |
|---|---|
| `login_id` | the ID number of this login attempt |
| `user_id` | **who** tried to log in (points to `users.user_id`) |
| `ts` | when it happened |
| `src_ip` | the IP address it came from |
| `country` | country code of that IP |
| `success` | `1` = login worked, `0` = login failed |

All names and data are made up. External IPs use the `203.0.113.0/24` range, which is reserved for documentation.

## How to run it

You need [SQLite](https://sqlite.org) installed (`sqlite3`).

```
git clone https://github.com/iacob-mihail/sql-login-investigation-lab.git
cd sql-login-investigation-lab
sqlite3 soc.db < setup.sql
sqlite3 soc.db
```

Then inside SQLite, make the output readable:

```
.headers on
.mode column
```

Start with `SELECT * FROM logins;` and see what you notice.

## What this lab practices

- `SELECT`, `FROM`, `WHERE`, `AND`: pulling out specific rows (e.g. only failed logins)
- `COUNT`, `GROUP BY`: counting things per IP, per user, per country
- `HAVING`: keeping only groups over a threshold, which is basically how a brute-force alert rule works
- `JOIN`: combining two tables so the results show usernames, not just ID numbers
- `UPDATE`: changing data safely (always with a `WHERE`!)

## Why this matters

SOC analysts and IT auditors constantly need to pull answers out of large amounts of data: who logged in, from where, how many times, what failed. SQL is one of the main tools for that, and SIEM query languages (like Splunk's SPL or Microsoft's KQL) work on the same ideas.

## Files

| File | What it is |
|---|---|
| `setup.sql` | builds the database (tables + data) |
| `cheatsheet.md` | my notes on every SQL command used in the lab |
