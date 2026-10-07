# SQL Cheat Sheet

## 1. Getting into the database (normal terminal)

| Command                           | What it does                                     |
|-----------------------------------|--------------------------------------------------|
| `cd ~/Documents/Work/sql-practice` | go to the folder with the database              |
| `sqlite3 soc.db`                  | open the database (prompt changes to `sqlite>`)  |

## 2. SQLite's own commands (start with a dot, no `;` needed)

| Command         | What it does                                  |
|-----------------|-----------------------------------------------|
| `.headers on`   | show column names above results               |
| `.mode column`  | line results up in neat columns               |
| `.tables`       | list all tables (the tabs)                    |
| `.schema users` | show the columns of a table                   |
| `.quit`         | leave SQLite, back to the normal terminal     |

## 3. SQL (always end with `;`)

Show a whole table:

    SELECT * FROM users;

Show only some columns:

    SELECT username, department FROM users;

Filter rows (WHERE):

    SELECT * FROM logins WHERE success = 0;

Two conditions (AND), not-equal (!=):

    SELECT * FROM logins WHERE success = 0 AND country != 'RO';

Count rows (COUNT):

    SELECT COUNT(*) FROM logins WHERE success = 0;

Count different values (COUNT DISTINCT):

    SELECT COUNT(DISTINCT src_ip) FROM logins;

Make groups and count each one (GROUP BY):

    SELECT country, COUNT(*) FROM logins GROUP BY country;

Filter rows, then make groups:

    SELECT src_ip, COUNT(*) FROM logins WHERE success = 0 GROUP BY src_ip;

Keep only some groups (HAVING):

    SELECT country, COUNT(*) FROM logins GROUP BY country HAVING COUNT(*) > 1;

## 4. Symbols

| Symbol      | Meaning                                    |
|-------------|--------------------------------------------|
| `=`         | equal                                      |
| `!=`        | not equal                                  |
| `>` / `<`   | greater / less than                        |
| `>=` / `<=` | greater or equal / less or equal           |
| `*`         | "all" (all columns, or count all rows)     |
| `,`         | separates items in a list                  |
| `'text'`    | text value, always in single quotes        |
| `;`         | end of statement                           |

## 5. If something goes wrong

| Problem                       | Fix                                   |
|-------------------------------|---------------------------------------|
| Stuck at `...>`               | type `;` and press Enter              |
| Messed up, want to start over | press Ctrl+C                          |
| `no such column: RO`          | forgot the quotes, write `'RO'`       |

## 6. Order of the pieces

You don't always need all of them, but they always go in this order:

    SELECT ... FROM ... WHERE ... GROUP BY ... HAVING ... ;

## 7. Template: "each X and how many"

Whenever the question says "each X and how many", the query is always this shape:

    SELECT X, COUNT(*) FROM table GROUP BY X;

X shows up TWICE:
- after SELECT   -> so you see the label (which group is which)
- after GROUP BY -> so the groups actually get made

If X is in one place but not the other, something's wrong.

| Question                              | X            | table    |
|---------------------------------------|--------------|----------|
| each country and how many logins      | `country`    | `logins` |
| each user_id and how many logins      | `user_id`    | `logins` |
| each department and how many users    | `department` | `users`  |

Need a filter too? Put WHERE before GROUP BY:

    SELECT X, COUNT(*) FROM table WHERE ... GROUP BY X;

Only want some groups? Put HAVING after GROUP BY:

    SELECT X, COUNT(*) FROM table GROUP BY X HAVING COUNT(*) >= 2;

## 8. Key words in the question -> what to write

| Question says                       | Write        |
|-------------------------------------|--------------|
| "how many"                          | `COUNT(*)`   |
| "each"                              | `GROUP BY`   |
| "only" / a specific value           | `WHERE`      |
| "only ... with 2 or more" (a count) | `HAVING`     |
| `success = 0`                       | "success? no" = FAILED |
| `success = 1`                       | "success? yes" = WORKED |

## 9. Remember

- SELECT = "show me"
- WHERE  = throw away rows, BEFORE grouping
- GROUP BY = sort rows into groups (and count each group)
- HAVING = throw away whole groups, AFTER counting
- Read your query out loud as a sentence to check it.
- Check the result answers the question you meant to ask.

## 10. JOIN (combine two tables)

    SELECT users.username, logins.src_ip, logins.success
    FROM logins
    JOIN users ON logins.user_id = users.user_id;

- `JOIN users`  = bring in the users table too
- `ON ...`      = the matching rule: login's user_id must equal the user's user_id
- `users.user_id` = "the user_id column FROM users" (dot = which table)

What happens:
1. For each login row, find the user with the same user_id
2. Glue that user's WHOLE row onto the login row -> one big wide table
3. SELECT picks the columns you want from the big table (from either side)

Column names (renamed 2026-10-07 to be clearer):
- users.user_id   = the person's ID number (never changes, NOT a row counter)
- logins.login_id = the login event's ID number
- logins.user_id  = WHO did the login -> points to users.user_id

In other databases you'll often see plain `id` instead. Then the pattern is:
a column called `something_id` matches the `id` of the `something` table.

See the whole glued table:

    SELECT * FROM logins JOIN users ON logins.user_id = users.user_id;
