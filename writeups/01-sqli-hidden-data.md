# 01 — SQL Injection: Retrieving Hidden Data

*Platform:* PortSwigger Web Security Academy (Apprentice lab)
*Date:* 23 Oct 2026
*Skill:* SQL injection in a WHERE clause

## Scenario
A shop filters products by category. Behind the scenes it runs:
`SELECT * FROM products WHERE category = 'Gifts' AND released = 1`
The goal: make the app show unreleased products.

## Attack
I changed the `category` parameter in the URL to:
`%27+OR+1=1--`

## Result
The page listed every product, including unreleased ones, and the lab was marked solved.
<img width="2503" height="1119" alt="Portswigger 1" src="https://github.com/user-attachments/assets/2eb55524-8c1b-4b3d-be3b-6834c8344e16" />

## Why it worked
- `'` closed the category string early
- `OR 1=1` added a condition that is always true
- `--` commented out the rest of the query, so `AND released = 1` never ran
The app put my input straight into the SQL query, so I could change the query's logic.

## Detection
- Quotes, `OR`, and `--` appearing in a parameter that should only hold a category name
- Database errors or sudden jumps in returned rows
- WAF / web server log rules for common SQLi patterns

## Fix
- Use parameterised queries (prepared statements), so input is treated as data, never as SQL
- Allow-list valid category values
- Give the database account least privilege

## What I learned
One unsanitised input can change what a query returns. Testing every parameter matters.
