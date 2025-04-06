# PostgreSQL Guide: Importing CSV Data for Beginners

This guide will walk you through the process of creating a PostgreSQL database, creating a table, and importing data from a CSV file. We'll be using an air quality dataset from the East Village Association as an example, but the steps are designed for you to adapt to your own unique datasets. Think of this as a template you can follow with your own data!

## Prerequisites
- PostgreSQL installed on your computer
- Basic knowledge of using a terminal or command prompt
- Your own CSV file that you want to import
  - This guide uses "East_Village_Associationlast_30_days.csv" as an example, but you'll substitute this with your own dataset
  - Your CSV might have completely different columns and data than our example!

## Step 1: Access PostgreSQL

Open your terminal or command prompt and connect to your PostgreSQL database:

```
psql
```

## Step 2: Create a New Database

Once connected to PostgreSQL, create a new database for your data:

```sql
CREATE DATABASE air_quality_db;
```

You should see a confirmation message like `CREATE DATABASE`.

**For your own project:** Choose a database name that reflects your data. For example:
```sql
CREATE DATABASE my_project_db;
```
```sql
CREATE DATABASE weather_analysis;
```
```sql
CREATE DATABASE student_survey_results;
```

## Step 3: Connect to Your New Database

Disconnect from the default database and connect to your new one:

```
\c air_quality_db
```

You should see a message like `You are now connected to database "air_quality_db" as user "postgres"`.

**For your own project:** Use the name of the database you created:
```
\c my_project_db
```

## Step 4: Create a Table

Now, create a table to store your air quality data:

```sql
CREATE TABLE air_quality (
    date DATE,
    pm2_5 FLOAT,
    pm10 FLOAT
);
```

This creates a table with three columns:
- `date`: The date of the measurement
- `pm2_5`: The PM2.5 particulate matter measurement
- `pm10`: The PM10 particulate matter measurement

### Verifying Your Table Structure

After creating your table, it's important to verify that it has the expected structure. Use the `\d` command (for "describe"):

```
\d air_quality
```

This will show you:
- All column names
- The data type of each column
- Any constraints (like primary keys)

Example output:
```
                      Table "public.air_quality"
 Column |  Type   | Collation | Nullable | Default 
--------+---------+-----------+----------+---------
 date   | date    |           |          | 
 pm2_5  | double  |           |          | 
 pm10   | double  |           |          | 
```

**For your own project:** 
```
\d your_table_name
```

If you need to see all tables in your database, simply use:
```
\dt
```

### Table and Column Naming Rules

When creating tables and columns in PostgreSQL, follow these important syntax rules to avoid errors:

**Column Naming Rules:**
1. **NO SPACES allowed in column names!** This is the most common mistake.
   - ❌ Bad: `student name` (has a space)
   - ✅ Good: `student_name` (uses underscore)

2. **Start with a letter or underscore**, followed by letters, numbers, or underscores.
   - ❌ Bad: `2nd_column` (starts with number)
   - ✅ Good: `column_2` (starts with letter)

3. **Case sensitivity matters:** By default, PostgreSQL converts all column names to lowercase unless you use double quotes.
   - Without quotes: `CREATE TABLE test (StudentName TEXT);` creates column `studentname` (all lowercase)
   - With quotes: `CREATE TABLE test ("StudentName" TEXT);` preserves case exactly as `StudentName`
   - **For beginners**: Stick to all lowercase with underscores to avoid confusion.

4. **Length limit:** Column names can be up to 63 characters long.

5. **Avoid reserved words** like SELECT, FROM, WHERE, etc. as column names.
   - If you must use a reserved word, put double quotes around it
   - ❌ Bad: `table` (this is a reserved word)
   - ✅ Good: `table_name` or `"table"` (with quotes)

**Examples of good and bad column names:**
- ✅ Good: `first_name`, `student_id`, `test_score_1`
- ❌ Bad: `first name`, `student ID`, `test score #1`

**What if your CSV has spaces in column headers?**
If your CSV file already has headers with spaces, you have two options:
1. Create your table with proper column names, then map the columns during import:
```
\copy air_quality(date, pm2_5, pm10) FROM 'file.csv' WITH CSV HEADER
```
2. Use double quotes around problematic column names (not recommended for beginners):
```sql
CREATE TABLE survey_results (
    "Student Name" TEXT,
    "Test Score" INTEGER
);
```

### Choosing the Right Data Types

When creating your own table for different data, you'll need to choose appropriate data types for each column. Here's a guide to common PostgreSQL data types:

**For text data:**
- `VARCHAR(n)`: Text with variable length, up to n characters (e.g., `name VARCHAR(100)`)
- `TEXT`: Text with unlimited length (for longer text)
- `CHAR(n)`: Fixed-length text, always n characters

**For numeric data:**
- `INTEGER` or `INT`: Whole numbers without decimals (e.g., counts, IDs)
- `FLOAT`: Numbers with decimal points when precision isn't critical
- `NUMERIC(p,s)` or `DECIMAL(p,s)`: Precise decimal numbers where p is total digits and s is digits after decimal point (e.g., `price NUMERIC(10,2)` for money)

**For date and time:**
- `DATE`: Date only (YYYY-MM-DD)
- `TIME`: Time only (HH:MM:SS)
- `TIMESTAMP`: Date and time together
- `TIMESTAMPTZ`: Date and time with timezone

**For other types:**
- `BOOLEAN`: True/False values
  - PostgreSQL accepts various values for booleans:
  - For TRUE: 'true', 't', 'yes', 'y', '1'
  - For FALSE: 'false', 'f', 'no', 'n', '0'
  - Example: If your CSV has "Yes"/"No" values, you can use BOOLEAN type and PostgreSQL will convert them automatically

**Example for a survey with Yes/No responses:**
```sql
CREATE TABLE survey_responses (
    respondent_id INTEGER,
    completed BOOLEAN,  -- Will store Yes/No as true/false
    opted_in BOOLEAN    -- Will store Yes/No as true/false
);
```

When importing "Yes"/"No" values as BOOLEAN:
```
\copy survey_responses FROM 'survey_data.csv' WITH CSV HEADER
```

PostgreSQL will automatically convert:
- "Yes" → TRUE
- "No" → FALSE

**Example for creating a student grades table:**
```sql
CREATE TABLE student_grades (
    student_id INTEGER,
    student_name VARCHAR(100),
    assignment_date DATE,
    score NUMERIC(5,2),
    passed BOOLEAN
);
```

## Step 5: Import Data from the CSV File

Use the `\copy` command to import data from your CSV file:

```
\copy air_quality FROM '/path/to/East_Village_Associationlast_30_days.csv' WITH CSV HEADER DELIMITER ','
```

Make sure to replace `/path/to/` with the actual path to your CSV file. For example:
- On Windows: `C:/Users/YourName/Downloads/East_Village_Associationlast_30_days.csv`
- On Mac/Linux: `/home/YourName/Downloads/East_Village_Associationlast_30_days.csv`

The command options mean:
- `WITH CSV`: The file is in CSV format
- `HEADER`: The first row of the CSV contains column names (not data)
- `DELIMITER ','`: Values in the file are separated by commas

### Template for Your Own Dataset

To adapt this for your own dataset, follow these steps:

**Step 1: Examine your CSV file**
Before importing, look at your CSV file to understand:
- What are the column names?
- What type of data is in each column?
- Does it have a header row?
- What delimiter is used? (comma, tab, semicolon, etc.)

**Step 2: Create a matching table**
Create a table with columns that match your CSV file structure.

**Step 3: Use the \copy command template**
```
\copy YOUR_TABLE_NAME FROM '/path/to/YOUR_FILENAME.csv' WITH CSV HEADER DELIMITER ','
```

**Examples for different datasets:**

1. **Student grades dataset:**
```
\copy student_grades FROM 'C:/School/class_grades.csv' WITH CSV HEADER DELIMITER ','
```

2. **Weather data (tab-separated):**
```
\copy weather_data FROM '/home/student/weather_2023.csv' WITH CSV HEADER DELIMITER E'\t'
```

3. **Sales data (with specified columns):**
```
\copy sales(date, product, quantity, price) FROM '/Downloads/sales_data.csv' WITH CSV HEADER
```

4. **Data with missing values (marked as 'N/A'):**
```
\copy survey_results FROM '/Documents/survey.csv' WITH CSV HEADER DELIMITER ',' NULL 'N/A'
```

## Step 6: Verify the Import

Check that your data was imported correctly by viewing the first few rows:

```sql
SELECT * FROM air_quality LIMIT 5;
```

You should see the first 5 rows of your data displayed in the terminal.

To count how many rows were imported:

```sql
SELECT COUNT(*) FROM air_quality;
```

For our example dataset, this should show 22 rows.

**For your own project:** 
Replace "air_quality" with your own table name:

```sql
SELECT * FROM your_table_name LIMIT 5;
```

```sql
SELECT COUNT(*) FROM your_table_name;
```

Always verify that the number of rows matches what you expect from your CSV file!

## Step 7: Basic Data Analysis

Now that your data is in PostgreSQL, you can run some basic analyses.

**Example queries for our air quality data:**

Calculate the average PM2.5 and PM10 values:
```sql
SELECT 
    AVG(pm2_5) AS average_pm2_5, 
    AVG(pm10) AS average_pm10 
FROM air_quality;
```

Find the day with the highest PM2.5 reading:
```sql
SELECT date, pm2_5 
FROM air_quality 
ORDER BY pm2_5 DESC 
LIMIT 1;
```

**For your own project:**

These are general patterns you can adapt to your own data:

Count rows in your table:
```sql
SELECT COUNT(*) FROM your_table_name;
```

Calculate averages for numeric columns:
```sql
SELECT AVG(your_numeric_column) FROM your_table_name;
```

Find maximum and minimum values:
```sql
SELECT 
    MAX(your_column) AS highest_value,
    MIN(your_column) AS lowest_value
FROM your_table_name;
```

Group and summarize data:
```sql
SELECT 
    your_category_column,
    COUNT(*) AS count,
    AVG(your_numeric_column) AS average
FROM your_table_name
GROUP BY your_category_column;
```

## Step 8: Exit PostgreSQL

When you're finished, you can exit PostgreSQL:

```
\q
```

## Troubleshooting Tips

1. **Path Error**: If you get a "file not found" error, double-check your file path. Remember that paths are case-sensitive on most systems.

2. **Permission Error**: Make sure you have permission to read the CSV file.

3. **Date Format Issues**: If your dates aren't importing correctly, you might need to update them after import:
   ```sql
   UPDATE air_quality SET date = TO_DATE(date, 'MM/DD/YYYY');
   ```

4. **Connection Issues**: If you can't connect to PostgreSQL, make sure the service is running and you're using the correct username and password.

## Additional Resources

- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [Importing CSV data in PostgreSQL](https://www.postgresql.org/docs/current/sql-copy.html)
- [psql Commands Cheat Sheet](https://www.postgresqltutorial.com/postgresql-cheat-sheet/)
- [Choosing PostgreSQL Data Types](https://www.postgresql.org/docs/current/datatype.html)
- [SQL Tutorial for Beginners](https://www.w3schools.com/sql/)

Remember, the best way to learn is by practicing with your own data! Use this guide as a starting point and adapt it to your specific needs.

## Glossary

- **PostgreSQL**: An open-source relational database management system
- **psql**: The interactive terminal for working with PostgreSQL
- **CSV**: Comma-Separated Values, a common format for storing tabular data
- **SQL**: Structured Query Language, used to communicate with databases
- **PM2.5/PM10**: Particulate Matter measurements for air quality (PM2.5 are fine particles less than 2.5 micrometers in diameter)
