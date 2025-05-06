## 📌 Server-Side Processing and API Endpoints: Transforming and Delivering Data

After retrieving data from the database, the next crucial steps in our data journey are:
1. Transforming the raw data on the server into the format our frontend needs
2. Creating API endpoints that serve as clear access points for the frontend

### 🔹 Why Process Data on the Server?

Processing data on the server before sending it to the frontend offers several benefits:

1. **Reduced Data Transfer**: We only send the necessary data to the client, making our application faster
2. **Simpler Frontend Logic**: Our React components stay focused on presentation rather than complex data manipulation
3. **Consistent Data Format**: All frontend components receive data in a predictable, ready-to-use format

### 🔹 Common Data Transformations

Let's look at common transformations we need for our air quality visualizations:

#### 1. Site Comparison (Bar Chart)

Our bar chart needs the average PM2.5 reading for each monitoring site. Here's how we transform the raw data:

```javascript
// Raw database response (before transformation)
{
  rows: [
    {
      local_site_name: "Allen Park",
      avg_pm25: "6.432142857142857"
    },
    {
      local_site_name: "East 7 Mile",
      avg_pm25: "7.802631578947368"
    },
    // More rows...
  ]
}

// Transformed data (after processing)
[
  { site: "Allen Park", pm: 6.4 },
  { site: "East 7 Mile", pm: 7.8 },
  // More sites...
]
```

#### 2. Time Trends (Line Chart)

Our line chart needs PM2.5 readings organized by site and date:

```javascript
// Raw database response (before transformation)
{
  rows: [
    {
      date: "2025-01-01",
      local_site_name: "Allen Park",
      daily_mean_pm25: 3.1
    },
    {
      date: "2025-01-02",
      local_site_name: "Allen Park",
      daily_mean_pm25: 4.7
    },
    {
      date: "2025-01-01",
      local_site_name: "East 7 Mile",
      daily_mean_pm25: 2.8
    },
    // More rows...
  ]
}

// Transformed data (after processing)
[
  {
    site: "Allen Park",
    readings: [
      { date: "2025-01-01", pm: 3.1 },
      { date: "2025-01-02", pm: 4.7 },
      // More readings...
    ]
  },
  {
    site: "East 7 Mile",
    readings: [
      { date: "2025-01-01", pm: 2.8 },
      { date: "2025-01-02", pm: 4.9 },
      // More readings...
    ]
  }
  // More sites...
]
```

#### 3. Geographic Map

Our map needs location coordinates and average readings for each site:

```javascript
// Raw database response (before transformation)
{
  rows: [
    {
      site_id: 261630001,
      local_site_name: "Allen Park",
      county: "Wayne",
      site_latitude: 42.228611,
      site_longitude: -83.208333,
      avg_pm25: "6.432142857142857"
    },
    // More rows...
  ]
}

// Transformed data (after processing)
[
  {
    id: 261630001,
    name: "Allen Park",
    county: "Wayne",
    position: [42.228611, -83.208333],
    pm25: 6.4
  },
  // More sites...
]
```

### 🔹 Creating RESTful API Endpoints

We'll use RESTful API principles to organize our endpoints. These are industry standard ways of organizing endpoints that provide a clear structure for your frontend to access data.

The basic structure for our API URLs will be:

```
/api/v1/resource-name
```

- `/api`: Indicates this is an API endpoint
- `/v1`: Version 1 of our API
- `/resource-name`: Describes the data being provided

Let's create endpoints for each of our visualizations:

#### 1. Site Comparison Endpoint (Bar Chart)

```javascript
// In server.js or a routes file
import express from "express";
import * as db from "./db/index.js";

const app = express();

// Endpoint for bar chart data - site averages
app.get("/api/v1/site-comparison", async (req, res) => {
  try {
    // Step 1: Query the database for site averages
    const dbResponse = await db.query(`
      -- Get the average PM2.5 for each monitoring site
      SELECT 
        local_site_name,  -- The name of the monitoring station 
        AVG(daily_mean_pm25) as avg_pm25  -- Calculate average PM2.5 for each site
      FROM air_quality
      GROUP BY local_site_name  -- Group records by site name to get one average per site
    `);

    // Step 2: Format the data for the frontend
    const result = dbResponse.rows.map((row) => ({
      site: row.local_site_name,
      pm: parseFloat(row.avg_pm25.toFixed(1)),
    }));

    // Step 3: Send the transformed data to the client
    res.json(result);
  } catch (error) {
    console.error("Error calculating site averages:", error);
    res.status(500).json({ error: "Server error" });
  }
});
```

#### 2. Time Trends Endpoint (Line Chart)

```javascript
// Endpoint for line chart data - time trends
app.get("/api/v1/time-trends", async (req, res) => {
  try {
    // Step 1: Query the database for all readings with dates
    const dbResponse = await db.query(`
      -- Get PM2.5 readings with dates for all monitoring sites
      SELECT 
        date,  -- The date of the reading
        local_site_name,  -- The name of the monitoring station
        daily_mean_pm25  -- The PM2.5 reading for that day
      FROM air_quality
      WHERE daily_mean_pm25 IS NOT NULL  -- Skip any readings with missing data
      ORDER BY date  -- Sort by date for time-series data
    `);

    // Step 2: Organize data by site
    const siteData = {};

    // Loop through all rows
    dbResponse.rows.forEach((row) => {
      const siteName = row.local_site_name;
      const date = row.date;
      const pmReading = row.daily_mean_pm25;

      // Format the date as YYYY-MM-DD for consistency
      const formattedDate = new Date(date).toISOString().split("T")[0];

      // If this is the first time seeing this site, initialize its data array
      if (!siteData[siteName]) {
        siteData[siteName] = [];
      }

      // Add this reading to the site's data
      siteData[siteName].push({
        date: formattedDate,
        pm: pmReading,
      });
    });

    // Step 3: Convert to the format expected by the frontend
    const result = Object.keys(siteData).map((siteName) => {
      return {
        site: siteName,
        readings: siteData[siteName],
      };
    });

    // Step 4: Send the transformed data to the client
    res.json(result);
  } catch (error) {
    console.error("Error processing time trends:", error);
    res.status(500).json({ error: "Server error" });
  }
});
```

#### 3. Map Data Endpoint

```javascript
// Endpoint for map data - site locations and averages
app.get("/api/v1/map-data", async (req, res) => {
  try {
    // Step 1: Query the database for site info including coordinates
    const dbResponse = await db.query(`
      -- Get location information and average PM2.5 for each monitoring site
      SELECT 
        site_id,  -- Unique identifier for the monitoring station
        local_site_name,  -- Name of the monitoring station
        county,  -- County where the station is located
        site_latitude,  -- Latitude coordinate for the map
        site_longitude,  -- Longitude coordinate for the map
        AVG(daily_mean_pm25) as avg_pm25  -- Calculate average PM2.5 reading
      FROM air_quality
      GROUP BY  -- Group by all the columns that aren't being aggregated
        site_id, 
        local_site_name, 
        county, 
        site_latitude, 
        site_longitude
    `);

    // Step 2: Format the data for the map component
    const mapData = dbResponse.rows.map((row) => {
      return {
        id: row.site_id,
        name: row.local_site_name,
        county: row.county,
        position: [row.site_latitude, row.site_longitude],
        pm25: parseFloat(row.avg_pm25.toFixed(1)),
      };
    });

    // Step 3: Send the transformed data to the client
    res.json(mapData);
  } catch (error) {
    console.error("Error processing map data:", error);
    res.status(500).json({ error: "Server error" });
  }
});
```

### 🔹 Handling Data Types and Formats

When transforming data, pay attention to these common formats:

1. **Dates**: Format consistently (e.g., "YYYY-MM-DD")
2. **Numbers**: Parse numeric values, especially those that come as strings from PostgreSQL
3. **Coordinates**: Format as arrays ([latitude, longitude]) for mapping libraries
4. **Precision**: Round values to an appropriate number of decimal places

For example, notice how we format the PM2.5 values to one decimal place:

```javascript
pm25: parseFloat(row.avg_pm25.toFixed(1))
```

### 🔹 Error Handling Best Practices

Robust error handling is critical in server-side processing:

```javascript
app.get("/api/v1/endpoint", async (req, res) => {
  try {
    // Database operations and data processing
    const dbResponse = await db.query("SELECT * FROM air_quality");
    const result = process(dbResponse.rows);
    res.json(result);
  } catch (error) {
    // Log the error for developers
    console.error("Detailed error:", error);
    
    // Send a friendly message to the client
    res.status(500).json({ 
      error: "Failed to retrieve data. Please try again later."
    });
  }
});
```

This pattern:
1. Attempts the operation in a try block
2. Catches any errors that occur
3. Logs detailed error information for debugging
4. Sends a user-friendly error message to the frontend

### 🔹 Organizing Your Server-Side Code

As your application grows, keep your code maintainable by separating concerns:

```javascript
// routes/airQuality.js - Define routes
import express from "express";
import { getSiteAverages, getTimeTrends, getMapData } from "../controllers/airQualityController.js";

const router = express.Router();

router.get("/site-comparison", getSiteAverages);
router.get("/time-trends", getTimeTrends);
router.get("/map-data", getMapData);

export default router;

// controllers/airQualityController.js - Handle business logic
import * as db from "../db/index.js";

export const getSiteAverages = async (req, res) => {
  try {
    // Query and transform data for site averages
    // ...
  } catch (error) {
    // Error handling
  }
};

// server.js - Main server file
import express from "express";
import airQualityRoutes from "./routes/airQuality.js";

const app = express();
app.use("/api/v1", airQualityRoutes);
// ...
```

This organization:
1. Keeps your server.js file clean
2. Groups related endpoints together
3. Separates route definitions from business logic
4. Makes it easier to add new endpoints in the future

### 🔹 Testing Your API Endpoints

Before connecting to the frontend, test your API endpoints using Postman:

1. Start your Express server: `npm start` in the backend directory
2. Open Postman
3. Create a new GET request to `http://localhost:3000/api/v1/site-comparison`
4. Send the request and examine the response

Make sure your endpoints:
- Return data in the expected format
- Handle errors gracefully
- Return appropriate HTTP status codes

> 🎯 **Success!** You now understand how to process data on the server and create API endpoints that deliver the right data format to your frontend. In the next section, we'll learn how to fetch this data from the React frontend.
