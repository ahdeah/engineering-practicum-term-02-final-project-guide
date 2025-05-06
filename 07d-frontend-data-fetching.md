## 📌 Frontend Data Fetching: Bringing Data to React

Now that our server is prepared to send properly formatted data, we need to fetch this data from our React components. This is where our data journey enters the frontend.

### 🔹 Understanding Asynchronous Data Flow

To create responsive applications, we need to understand how asynchronous data fetching works:

1. **Component Renders**: Your React component first renders with initial/empty state
2. **Data Fetch Starts**: A request is sent to the API endpoint
3. **Component Continues**: While waiting for data, your component should show a loading state
4. **Data Arrives**: When data arrives (potentially seconds later), component updates to show the data
5. **Errors Handled**: If something goes wrong, component should show an error message

This flow ensures your application remains responsive even when data takes time to load.

### 🔹 The Fetch API and React Hooks

#### The Fetch API

Modern browsers include a built-in `fetch()` function for making HTTP requests:

```javascript
// Basic fetch request
fetch("/api/v1/site-comparison")
  .then((response) => response.json())  // Convert response to JSON
  .then((data) => console.log(data))    // Use the data
  .catch((error) => console.error("Error:", error));  // Handle errors
```

#### React's useState Hook

The `useState` hook is used to create and update state variables in your components:

```javascript
// Import the hook
import { useState } from 'react';

function MyComponent() {
  // Create a state variable called "data" with initial value of null
  // setData is the function we'll use to update this state
  const [data, setData] = useState(null);
  
  // More code...
  
  // Later, we can update the state:
  setData(newData);
  
  // And we can use the state in our rendering:
  return (
    <div>
      {data ? <div>{data.someValue}</div> : <div>No data yet</div>}
    </div>
  );
}
```

#### React's useEffect Hook: A Beginner's Guide

The `useEffect` hook is one of React's most powerful features but can be confusing for beginners. Think of `useEffect` as a way to tell React: "Hey, I need to do something after my component renders."

```javascript
// Import the hook
import { useEffect } from 'react';

function MyComponent() {
  // The useEffect hook takes two arguments:
  // 1. A function that contains the "effect" (the code to run)
  // 2. A dependency array that controls when the effect runs
  
  useEffect(() => {
    // Code inside here runs after the component renders
    console.log("Component has rendered!");
    
    // Optional: Return a cleanup function
    return () => {
      console.log("Component is being removed or re-rendered!");
    };
  }, []); // Empty dependency array means "run once after the first render"
  
  // Rest of your component...
}
```

**When does useEffect run?**

The dependency array (the second argument to `useEffect`) controls when your effect runs:

- **Empty array `[]`**: Run once after the first render (like componentDidMount in class components)
- **Array with values `[value1, value2]`**: Run after first render AND after any render where these values changed
- **No array provided**: Run after every render (rarely what you want for data fetching)

For data fetching, we typically use an empty array `[]` because we want to fetch data once when the component first appears:

```javascript
useEffect(() => {
  // Fetch data when component first renders
  fetch("/api/endpoint")
    .then(response => response.json())
    .then(data => setData(data));
}, []); // Empty array = run once on mount
```

### 🔹 Handling Loading and Error States

A good user experience requires proper handling of loading and error states:

```javascript
function DataDisplay() {
  // Create state variables for data, loading state, and errors
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    // Set loading to true before fetching
    setLoading(true);
    // Clear any previous errors
    setError(null);
    
    fetch('/api/endpoint')
      .then(response => {
        // Check if response is OK (status 200-299)
        if (!response.ok) {
          throw new Error(`HTTP error! Status: ${response.status}`);
        }
        return response.json();
      })
      .then(data => {
        // Update state with the fetched data
        setData(data);
        // Set loading to false since we have the data
        setLoading(false);
      })
      .catch(error => {
        // Handle any errors
        console.error('Fetch error:', error);
        setError('Failed to load data. Please try again later.');
        // Also set loading to false since we're done (even though it failed)
        setLoading(false);
      });
  }, []); // Empty dependency array = run once on mount
  
  // Render different UI based on the state
  if (loading) {
    return <div>Loading data...</div>;
  }
  
  if (error) {
    return <div>Error: {error}</div>;
  }
  
  // Only reached if loading is false and there's no error
  return (
    <div>
      {/* Render your data here */}
      Data loaded successfully!
    </div>
  );
}
```

This pattern gives users clear feedback about what's happening:
- Shows a loading indicator while data is being fetched
- Shows a helpful error message if something goes wrong
- Shows the actual data once it's successfully loaded

### 🔹 Implementing Data Fetching in Our Components

Now let's implement data fetching for each of our visualization components:

#### 1. Bar Chart Component

```javascript
// BarChart.jsx
import React, { useState, useEffect } from "react";
import { Bar } from "react-chartjs-2";
// ... Chart.js imports

function BarChart() {
  // State to store the fetched data
  const [chartData, setChartData] = useState({
    labels: [],
    datasets: [],
  });

  // Loading state to show a loading indicator
  const [loading, setLoading] = useState(true);

  // Error state to display error messages
  const [error, setError] = useState(null);

  // useEffect hook to fetch data when component mounts
  useEffect(() => {
    // Define an async function inside useEffect
    async function fetchData() {
      try {
        // Set loading to true before fetching
        setLoading(true);

        // Clear any previous errors
        setError(null);

        // Fetch data from our API
        const response = await fetch("/api/v1/site-comparison");

        // Check if the response is ok (status 200-299)
        if (!response.ok) {
          throw new Error(`HTTP error! Status: ${response.status}`);
        }

        // Parse the JSON response
        const data = await response.json();

        // Transform API data into Chart.js format
        const formattedData = {
          labels: data.map((item) => item.site),
          datasets: [
            {
              label: "PM2.5 Concentration (μg/m³)",
              data: data.map((item) => item.pm),
              backgroundColor: data.map((value) => {
                // EPA colors for different PM2.5 levels
                if (value.pm <= 12) return "rgba(0, 228, 0, 0.6)"; // Good
                if (value.pm <= 35.4) return "rgba(255, 255, 0, 0.6)"; // Moderate
                if (value.pm <= 55.4) return "rgba(255, 126, 0, 0.6)"; // Unhealthy for sensitive groups
                if (value.pm <= 150.4) return "rgba(255, 0, 0, 0.6)"; // Unhealthy
                if (value.pm <= 250.4) return "rgba(143, 63, 151, 0.6)"; // Very unhealthy
                return "rgba(126, 0, 35, 0.6)"; // Hazardous
              }),
              borderColor: "rgba(75, 192, 192, 1)",
              borderWidth: 1,
            },
          ],
        };

        // Update state with the formatted data
        setChartData(formattedData);
      } catch (err) {
        // Store any errors in state
        setError(err.message);
        console.error("Error fetching data:", err);
      } finally {
        // Set loading to false when done (whether successful or error)
        setLoading(false);
      }
    }

    // Call the fetch function
    fetchData();

    // The empty dependency array means this useEffect runs once when the component mounts
  }, []);

  // Chart.js options
  const options = {
    // ... same options as before
  };

  // Show loading indicator while data is being fetched
  if (loading) {
    return <div>Loading chart data...</div>;
  }

  // Show error message if something went wrong
  if (error) {
    return <div>Error loading chart data: {error}</div>;
  }

  // Render the chart when data is available
  return (
    <div style={{ width: "80%", margin: "0 auto" }}>
      <Bar data={chartData} options={options} />
    </div>
  );
}

export default BarChart;
```

#### 2. Line Chart Component

```javascript
// LineChart.jsx
import React, { useState, useEffect } from "react";
import { Line } from "react-chartjs-2";
// ... Chart.js imports

function LineChart() {
  // State to store the fetched data
  const [chartData, setChartData] = useState({
    labels: [],
    datasets: [],
  });

  // Loading and error states
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  // useEffect hook to fetch data when component mounts
  useEffect(() => {
    async function fetchData() {
      try {
        setLoading(true);
        setError(null);

        // Fetch time trends data from our API
        const response = await fetch("/api/v1/time-trends");

        if (!response.ok) {
          throw new Error(`HTTP error! Status: ${response.status}`);
        }

        const data = await response.json();

        // Process the data for the line chart
        // First, get all unique dates across all sites
        const allDates = new Set();
        data.forEach((site) => {
          site.readings.forEach((reading) => {
            allDates.add(reading.date);
          });
        });

        // Convert to array and sort
        const sortedDates = Array.from(allDates).sort();

        // Create datasets for each site
        const datasets = data.map((site, index) => {
          // Create a map for quick lookup of readings by date
          const readingMap = {};
          site.readings.forEach((reading) => {
            readingMap[reading.date] = reading.pm;
          });

          // Generate color based on index
          const hue = (index * 30) % 360;
          const color = `hsla(${hue}, 70%, 50%, 0.7)`;

          // Create dataset with readings for all dates
          return {
            label: site.site,
            data: sortedDates.map((date) => readingMap[date] || null),
            fill: false,
            borderColor: color,
            backgroundColor: color,
            tension: 0.1,
          };
        });

        // Update the chart data state
        setChartData({
          labels: sortedDates,
          datasets: datasets,
        });
      } catch (err) {
        setError(err.message);
        console.error("Error fetching time trends:", err);
      } finally {
        setLoading(false);
      }
    }

    fetchData();
  }, []);

  // Chart.js options
  const options = {
    responsive: true,
    plugins: {
      legend: {
        position: "top",
      },
      title: {
        display: true,
        text: "PM2.5 Levels Over Time by Monitoring Site",
      },
    },
    scales: {
      y: {
        beginAtZero: true,
        title: {
          display: true,
          text: "PM2.5 Concentration (μg/m³)",
        },
      },
      x: {
        title: {
          display: true,
          text: "Date",
        },
      },
    },
  };

  if (loading) {
    return <div>Loading chart data...</div>;
  }

  if (error) {
    return <div>Error loading chart data: {error}</div>;
  }

  return (
    <div>
      <Line data={chartData} options={options} />
    </div>
  );
}

export default LineChart;
```

#### 3. Map Component

```javascript
// Map.jsx
import React, { useState, useEffect } from "react";
import { MapContainer, TileLayer, Marker, Popup } from "react-leaflet";
import "leaflet/dist/leaflet.css";
import L from "leaflet";

// Fix for default marker icons in React-Leaflet
delete L.Icon.Default.prototype._getIconUrl;
L.Icon.Default.mergeOptions({
  iconRetinaUrl:
    "https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.7.1/images/marker-icon-2x.png",
  iconUrl:
    "https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.7.1/images/marker-icon.png",
  shadowUrl:
    "https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.7.1/images/marker-shadow.png",
});

function Map() {
  // State for storing map data
  const [stations, setStations] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  // Center of Detroit
  const detroitPosition = [42.331429, -83.045753];

  // Fetch data when component mounts
  useEffect(() => {
    async function fetchMapData() {
      try {
        setLoading(true);
        setError(null);

        // Fetch map data from our API
        const response = await fetch("/api/v1/map-data");

        if (!response.ok) {
          throw new Error(`HTTP error! Status: ${response.status}`);
        }

        const data = await response.json();

        // Update state with the received data
        setStations(data);
      } catch (err) {
        setError(err.message);
        console.error("Error fetching map data:", err);
      } finally {
        setLoading(false);
      }
    }

    fetchMapData();
  }, []);

  if (loading) {
    return <div>Loading map data...</div>;
  }

  if (error) {
    return <div>Error loading map data: {error}</div>;
  }

  return (
    <div style={{ height: "500px", width: "100%" }}>
      <MapContainer
        center={detroitPosition}
        zoom={10}
        style={{ height: "100%", width: "100%" }}
      >
        <TileLayer
          attribution='&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
          url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
        />

        {/* Map over the stations array to create multiple markers */}
        {stations.map((station) => (
          <Marker
            key={station.id}
            position={station.position}
          >
            <Popup>
              <div>
                <h4>{station.name}</h4>
                <p>County: {station.county}</p>
                <p>PM2.5: {station.pm25} μg/m³</p>
              </div>
            </Popup>
          </Marker>
        ))}
      </MapContainer>
    </div>
  );
}

export default Map;
```

### 🔹 Common Data Fetching Patterns

Here are some common patterns you'll use in your data fetching code:

1. **Basic Data Fetching**: The patterns shown above, with `useState`, `useEffect`, and `fetch`

2. **Reusable Fetch Hooks**: Create custom hooks for reusable fetching logic:

```javascript
// A custom hook to fetch data
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    async function fetchData() {
      try {
        setLoading(true);
        setError(null);
        
        const response = await fetch(url);
        
        if (!response.ok) {
          throw new Error(`HTTP error! Status: ${response.status}`);
        }
        
        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }
    
    fetchData();
  }, [url]);

  return { data, loading, error };
}

// Using the custom hook
function MyComponent() {
  const { data, loading, error } = useFetch('/api/v1/site-comparison');
  
  // Then use the data, loading, and error states as needed
}
```

3. **Handling Loading States with Placeholders**: Use skeleton loading states for better UX:

```jsx
function BarChartWithSkeleton() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  
  // Fetch logic in useEffect...
  
  if (loading) {
    return (
      // These are not actual classes that exist by default. You would have to make this.
      <div className="chart-skeleton">
        <div className="skeleton-header"></div>
        <div className="skeleton-bars">
          <div className="skeleton-bar"></div>
          <div className="skeleton-bar"></div>
          <div className="skeleton-bar"></div>
          <div className="skeleton-bar"></div>
        </div>
      </div>
    );
  }
  
  return <BarChart data={data} />;
}
```

> 🎯 **Success!** You now understand how to fetch data from your API endpoints and use it in your React components. In the next section, we'll explore how to integrate this fetched data with your visualization components.
