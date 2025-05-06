## 📌 Database Layer: The Origin of the Data Journey

### 🔹 Understanding Your PostgreSQL Data Structure

Before we can effectively transform data, we need to understand how it's structured in our database. For our Air Quality Analyzer project, we have a single table called `air_quality` with the following schema:

```
                         Table "public.air_quality"
      Column      |          Type          | Collation | Nullable | Default
------------------+------------------------+-----------+----------+---------
 date             | date                   |           |          |
 site_id          | integer                |           |          |
 poc              | integer                |           |          |
 daily_mean_pm25  | double precision       |           |          |
 units            | character varying(15)  |           |          |
 daily_aqi_value  | integer                |           |          |
 local_site_name  | character varying(100) |           |          |
 county_fips_code | integer                |           |          |
 county           | character varying(50)  |           |          |
 site_latitude    | double precision       |           |          |
 site_longitude   | double precision       |           |          |
```

This table contains daily air quality measurements from different monitoring sites across the Detroit metropolitan area. Each row represents one day of measurements at a specific site.

### 🔹 Writing Effective PostgreSQL Queries

When working with databases, the journey begins with how you query the data. Let's explore some common query patterns for our air quality data:

#### 1. Basic Data Retrieval

The simplest query retrieves all records, but this is rarely what we want in production (when our app is actually being used by people on the world wide web):

```sql
SELECT * FROM air_quality;
```

This would return every single measurement in the database, which could be thousands of rows. For a web application, this would be:

- Inefficient (transferring unnecessary data)
- Slow (processing large datasets)
- Overwhelming (too much data to display meaningfully)

#### 2. Filtered Queries

A better approach is to filter data based on what your specific visualization needs:

```sql
-- Get data for a specific monitoring site
SELECT * FROM air_quality
WHERE local_site_name = 'Allen Park'
ORDER BY date;
```

```sql
-- Get data for a specific date range
SELECT * FROM air_quality
WHERE date BETWEEN '2025-01-01' AND '2025-01-31'
ORDER BY date;
```

#### 3. Aggregated Queries

For many visualizations, we want to aggregate data to show summaries or trends:

```sql
-- Calculate average PM2.5 level by monitoring site
SELECT local_site_name, county,
       AVG(daily_mean_pm25) as avg_pm25,
       COUNT(*) as total_readings
FROM air_quality
GROUP BY local_site_name, county
ORDER BY avg_pm25 DESC;
```

```sql
-- Calculate monthly averages for a specific site
SELECT
    EXTRACT(MONTH FROM date) as month,
    AVG(daily_mean_pm25) as avg_pm25
FROM air_quality
WHERE local_site_name = 'Allen Park'
GROUP BY EXTRACT(MONTH FROM date)
ORDER BY month;
```

### 🔹 Understanding Data Formats

When data is retrieved from PostgreSQL, it gets packaged into a specific format by the `pg` library, which we use in our Node.js backend. The query response looks something like this:

```javascript
// Copy paste this into perplexity and ask it to explain this if you need : )
{
  command: 'SELECT',   // The SQL command that was executed
  rowCount: 22,        // Number of rows returned
  oid: null,           // Object identifier
  rows: [              // The actual data rows
    {
      date: '2025-01-01',
      site_id: 261630001,
      poc: 1,
      daily_mean_pm25: 3.1,
      units: 'ug/m3 LC',
      daily_aqi_value: 17,
      local_site_name: 'Allen Park',
      county_fips_code: 163,
      county: 'Wayne',
      site_latitude: 42.228611,
      site_longitude: -83.208333
    },
    // More rows here...
  ],
  fields: [            // Metadata about the columns
    // Field definitions here...
  ]
}
```

The most important part for our data journey is the `rows` array, which contains objects representing each row from the database. Each object has properties corresponding to the column names in our PostgreSQL table.

### 🔹 From Database to Server: The Node.js Connection

We've already set up our database connection in the `backend/db/index.js` file. This establishes a pool of connections to our PostgreSQL database:

```javascript
import pg from "pg";
const { Pool } = pg;

// Creates a connection pool using environment variables
const pool = new Pool();

// Our query function that we'll use throughout our server
export const query = (text, params, callback) => {
  console.log("Hello from your db!");
  return pool.query(text, params, callback);
};
```

This query function will be our bridge between the database and the server. It allows us to:

- Execute SQL queries against our PostgreSQL database
- Pass parameters safely to avoid SQL injection
- Receive the query results for further processing

Here's how we use this function in our Express routes:

```javascript
import * as db from "./db/index.js";

// Example route that queries the database
app.get("/api/v1/air-quality-data", async (req, res) => {
  try {
    const dbResponse = await db.query("SELECT * FROM air_quality LIMIT 100");
    res.json(dbResponse.rows);
  } catch (error) {
    console.error("Database error:", error);
    res.status(500).json({ error: "Database error" });
  }
});
```

### 🔹 Key Considerations for the Database Layer

When working with your PostgreSQL database, keep these important points in mind:

1. **Request Only What You Need**

   - Select specific columns instead of using `SELECT *` when possible
   - Use `WHERE` clauses to filter out data you don't need
   - Use `LIMIT` to restrict the number of rows returned

2. **Error Handling**

   - Use try/catch blocks around database operations

   ```javascript
   try {
     const dbResponse = await db.query("SELECT * FROM air_quality LIMIT 5");
     res.json(dbResponse.rows);
   } catch (error) {
     console.error("Database error:", error);
     res.status(500).json({ error: "Database error" });
   }
   ```

   - Log database errors to help with debugging

3. **Understand Your Data**
   - Know which columns you need for each visualization
   - Use simple aggregations like `AVG()`, `COUNT()`, and `GROUP BY` when needed
   - Always check your query results to make sure they match your expectations

By making thoughtful choices about how you query your database, you create a solid foundation for your data journey. The data you retrieve here will flow through the rest of your application.
