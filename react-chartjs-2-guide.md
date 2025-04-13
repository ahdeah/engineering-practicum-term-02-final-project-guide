# 📊 Getting Started with react-chartjs-2

## 🚀 Introduction to react-chartjs-2

React-chartjs-2 is a React wrapper for Chart.js, one of the most popular JavaScript charting libraries. This guide will help you understand how to integrate and use react-chartjs-2 in your React applications, focusing on core concepts and practical examples.

## 📌 Core Concepts

### 🔹 Understanding the Relationship

```
Chart.js (Underlying library)
       ↑
react-chartjs-2 (React wrapper)
       ↑ 
Your React Component (Your code)
```

React-chartjs-2 provides React components that wrap around the Chart.js API. This means:
- You use React components like `<Bar />`, `<Line />`, etc.
- These components internally manage the Chart.js instances
- You avoid direct DOM manipulation that would be required with Chart.js alone

### 🔹 Available Chart Types

React-chartjs-2 provides components for all Chart.js chart types:

1. `<Bar />` - For bar charts
2. `<Line />` - For line charts 
3. `<Pie />` - For pie charts
4. `<Doughnut />` - For doughnut charts
5. `<PolarArea />` - For polar area charts
6. `<Radar />` - For radar charts
7. `<Scatter />` - For scatter charts
8. `<Bubble />` - For bubble charts

### 🔹 Data Structure

Every chart in react-chartjs-2 expects a `data` prop with a specific structure:

```javascript
const data = {
  // Labels for the X-axis (or categories in pie/doughnut)
  labels: ['Label 1', 'Label 2', 'Label 3'],
  
  // The actual data to be displayed
  datasets: [
    {
      label: 'Dataset 1',          // Name for the dataset
      data: [10, 20, 30],          // Actual data values
      backgroundColor: 'rgba(255, 99, 132, 0.5)',  // Bar/point/segment color
      borderColor: 'rgb(255, 99, 132)',            // Border color
      borderWidth: 1,                             // Border width
      // ... many other customization options
    },
    // You can have multiple datasets for some chart types
  ],
};
```

### 🔹 Options Object

The `options` prop lets you customize almost every aspect of your chart:

```javascript
const options = {
  responsive: true,      // Chart resizes with its container
  maintainAspectRatio: false,  // Allow chart to be any height/width
  
  plugins: {
    legend: {
      position: 'top',   // Legend position
    },
    title: {
      display: true,
      text: 'Chart Title',  // Chart title
    },
    tooltip: {
      // Tooltip customization
    },
  },
  
  scales: {
    // Scale customization for charts with axes
    y: {
      beginAtZero: true,
      title: {
        display: true,
        text: 'Y-Axis Label'
      }
    },
    x: {
      title: {
        display: true,
        text: 'X-Axis Label'
      }
    }
  },
  
  // Many other options available
};
```

### 🔹 Chart References

You can get a reference to the underlying Chart.js instance using a ref:

```javascript
import { useRef } from 'react';
import { Bar } from 'react-chartjs-2';

function MyChart() {
  const chartRef = useRef(null);
  
  return <Bar ref={chartRef} data={data} options={options} />;
}
```

> 📝 **Beginner's Note on useRef**: 
> 
> `useRef` is a React Hook that provides a way to access and store a reference to a DOM element or any value that persists across renders without causing re-renders when it changes.
> 
> In the context of charts:
> - It lets you access the underlying Chart.js instance directly
> - This gives you access to Chart.js methods not exposed through props
> - You can use it to call methods like `update()`, `getDatasetMeta()`, etc.
> 
> To use the chart reference:
> ```javascript
> function MyChart() {
>   const chartRef = useRef(null);
>   
>   // Example of using the chart reference
>   const handleSomeAction = () => {
>     // Access the Chart.js instance
>     const chart = chartRef.current;
>     
>     if (chart) {
>       // Now you can use Chart.js methods
>       chart.update(); // Force chart to update
>       chart.data.datasets[0].backgroundColor = 'red'; // Change color
>       chart.update(); // Update again to see changes
>     }
>   };
>   
>   return <Bar ref={chartRef} data={data} options={options} />;
> }
> ```

This gives you access to Chart.js methods when needed.

## 📌 Setting Up react-chartjs-2

### 🔹 Installation

Since Chart.js v3 (and react-chartjs-2 v4), you need to install both packages:

```bash
npm install chart.js react-chartjs-2
```

### 🔹 Chart.js 3+ Registration System

Chart.js 3 introduced a registration system for components. For each chart type you want to use, you need to register the required controllers and elements:

```javascript
// Import what you need from chart.js
import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  BarElement,
  Title,
  Tooltip,
  Legend,
} from 'chart.js';

// Register what you need
ChartJS.register(
  CategoryScale,
  LinearScale,
  BarElement,
  Title,
  Tooltip,
  Legend
);
```

> 📝 **Note:** Different chart types need different registrations. Check the examples below to see what each chart type requires.

## 📌 Creating a Basic Bar Chart

Let's create a simple bar chart displaying PM2.5 levels across monitoring sites:

### 🔹 Step 1: Import the necessary components

```javascript
import React from 'react';
import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  BarElement,
  Title,
  Tooltip,
  Legend,
} from 'chart.js';
import { Bar } from 'react-chartjs-2';

// Register the components
ChartJS.register(
  CategoryScale,
  LinearScale,
  BarElement,
  Title,
  Tooltip,
  Legend
);
```

### 🔹 Step 2: Set up your data and options

```javascript
function BarChart() {
  // Chart data
  const data = {
    labels: ['Site A', 'Site B', 'Site C', 'Site D', 'Site E'],
    datasets: [
      {
        label: 'PM2.5 Concentration (μg/m³)',
        data: [12.5, 8.2, 15.7, 6.3, 9.8],
        backgroundColor: 'rgba(75, 192, 192, 0.6)',
        borderColor: 'rgba(75, 192, 192, 1)',
        borderWidth: 1,
      },
    ],
  };

  // Chart options
  const options = {
    responsive: true,
    plugins: {
      legend: {
        position: 'top',
      },
      title: {
        display: true,
        text: 'PM2.5 Levels by Monitoring Site',
      },
    },
    scales: {
      y: {
        beginAtZero: true,
        title: {
          display: true,
          text: 'PM2.5 Concentration (μg/m³)'
        }
      },
      x: {
        title: {
          display: true,
          text: 'Monitoring Sites'
        }
      }
    },
  };

  return (
    <div style={{ height: '400px', width: '100%' }}>
      <Bar data={data} options={options} />
    </div>
  );
}

export default BarChart;
```

### 🔹 Step 3: Add it to your application

```javascript
import BarChart from './components/BarChart';

function App() {
  return (
    <div className="App">
      <h1>Air Quality Monitoring</h1>
      <div style={{ width: '80%', margin: '0 auto' }}>
        <BarChart />
      </div>
    </div>
  );
}
```

## 📌 Customizing Your Bar Chart

Let's explore various ways to customize your bar chart:

### 🔹 Changing Colors

For categorical data, you might want different colors for each bar:

```javascript
// Multiple colors for bars
const data = {
  labels: ['Site A', 'Site B', 'Site C', 'Site D', 'Site E'],
  datasets: [
    {
      label: 'PM2.5 Concentration',
      data: [12.5, 8.2, 15.7, 6.3, 9.8],
      backgroundColor: [
        'rgba(255, 99, 132, 0.6)',   // Red
        'rgba(54, 162, 235, 0.6)',   // Blue
        'rgba(255, 206, 86, 0.6)',   // Yellow
        'rgba(75, 192, 192, 0.6)',   // Green
        'rgba(153, 102, 255, 0.6)',  // Purple
      ],
      borderColor: [
        'rgba(255, 99, 132, 1)',
        'rgba(54, 162, 235, 1)',
        'rgba(255, 206, 86, 1)',
        'rgba(75, 192, 192, 1)',
        'rgba(153, 102, 255, 1)',
      ],
      borderWidth: 1,
    },
  ],
};
```

### 🔹 Add Multiple Datasets

To compare different metrics, you can add multiple datasets:

```javascript
const data = {
  labels: ['Site A', 'Site B', 'Site C', 'Site D', 'Site E'],
  datasets: [
    {
      label: 'PM2.5 Concentration',
      data: [12.5, 8.2, 15.7, 6.3, 9.8],
      backgroundColor: 'rgba(75, 192, 192, 0.6)',
      borderColor: 'rgba(75, 192, 192, 1)',
      borderWidth: 1,
    },
    {
      label: 'PM10 Concentration',
      data: [22.1, 18.5, 25.2, 16.9, 20.3],
      backgroundColor: 'rgba(153, 102, 255, 0.6)',
      borderColor: 'rgba(153, 102, 255, 1)',
      borderWidth: 1,
    },
  ],
};
```

### 🔹 Horizontal Bar Chart

To create a horizontal bar chart, add the `indexAxis: 'y'` option:

```javascript
const options = {
  responsive: true,
  indexAxis: 'y',  // This makes the bars horizontal
  plugins: {
    // ... other options
  },
  scales: {
    // ... scale options
  },
};
```

### 🔹 Adding Error Bars

For scientific data, you might want to add error bars. You need an additional plugin for this:

```javascript
// Install the plugin: npm install chartjs-plugin-error-bars
import { ErrorBarsPlugin } from 'chartjs-plugin-error-bars';
ChartJS.register(ErrorBarsPlugin);

// Then add error data to your dataset
const data = {
  labels: ['Site A', 'Site B', 'Site C', 'Site D', 'Site E'],
  datasets: [
    {
      label: 'PM2.5 Concentration',
      data: [12.5, 8.2, 15.7, 6.3, 9.8],
      backgroundColor: 'rgba(75, 192, 192, 0.6)',
      borderColor: 'rgba(75, 192, 192, 1)',
      borderWidth: 1,
      // Error bars configuration
      errorBars: {
        y: {
          plus: [1.2, 0.8, 1.5, 0.7, 1.1],
          minus: [1.2, 0.8, 1.5, 0.7, 1.1],
        }
      }
    },
  ],
};
```

### 🔹 Color based on thresholds

For air quality data, you might want to color bars based on EPA thresholds:

```javascript
const data = {
  labels: ['Site A', 'Site B', 'Site C', 'Site D', 'Site E'],
  datasets: [
    {
      label: 'PM2.5 Concentration',
      data: [12.5, 8.2, 15.7, 6.3, 9.8],
      // Dynamically set colors based on values
      backgroundColor: [12.5, 8.2, 15.7, 6.3, 9.8].map(value => {
        // EPA colors for different PM2.5 levels
        if (value <= 12) return 'rgba(0, 228, 0, 0.6)';      // Good
        if (value <= 35.4) return 'rgba(255, 255, 0, 0.6)';  // Moderate
        if (value <= 55.4) return 'rgba(255, 126, 0, 0.6)';  // Unhealthy for sensitive groups
        if (value <= 150.4) return 'rgba(255, 0, 0, 0.6)';   // Unhealthy
        if (value <= 250.4) return 'rgba(143, 63, 151, 0.6)'; // Very unhealthy
        return 'rgba(126, 0, 35, 0.6)';  // Hazardous
      }),
      borderColor: [12.5, 8.2, 15.7, 6.3, 9.8].map(value => {
        if (value <= 12) return 'rgba(0, 228, 0, 1)';
        if (value <= 35.4) return 'rgba(255, 255, 0, 1)';
        if (value <= 55.4) return 'rgba(255, 126, 0, 1)';
        if (value <= 150.4) return 'rgba(255, 0, 0, 1)';
        if (value <= 250.4) return 'rgba(143, 63, 151, 1)';
        return 'rgba(126, 0, 35, 1)';
      }),
      borderWidth: 1,
    },
  ],
};
```

### 🔹 Adding Reference Lines

To add horizontal lines (like EPA standard thresholds):

```javascript
const options = {
  responsive: true,
  plugins: {
    // ... other options
  },
  scales: {
    y: {
      beginAtZero: true,
      title: {
        display: true,
        text: 'PM2.5 Concentration (μg/m³)'
      }
    },
    x: {
      // ... x-axis options
    }
  },
  // Add threshold lines
  plugins: {
    // ... other plugin options
    annotation: {
      annotations: {
        line1: {
          type: 'line',
          yMin: 12,
          yMax: 12,
          borderColor: 'rgb(0, 228, 0)',
          borderWidth: 2,
          label: {
            display: true,
            content: 'Good/Moderate Threshold (12)',
            position: 'start'
          }
        },
        line2: {
          type: 'line',
          yMin: 35.4,
          yMax: 35.4,
          borderColor: 'rgb(255, 126, 0)',
          borderWidth: 2,
          label: {
            display: true,
            content: 'Moderate/Unhealthy Threshold (35.4)',
            position: 'start'
          }
        }
      }
    }
  }
};
```

> 📝 **Note:** For annotations, you need to install and register the annotation plugin: `npm install chartjs-plugin-annotation`

## 📌 Working with Real Data

### 🔹 Handling API Data

When working with real data from an API, you'll typically:

1. Fetch the data
2. Transform it into the format react-chartjs-2 expects
3. Update the chart when data changes

Here's an example:

```javascript
import React, { useState, useEffect } from 'react';
import { Bar } from 'react-chartjs-2';
// ... other imports and registrations

function AirQualityChart() {
  const [chartData, setChartData] = useState({
    labels: [],
    datasets: []
  });
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    async function fetchData() {
      try {
        setLoading(true);
        // Replace with your actual API endpoint
        const response = await fetch('/api/v1/air-quality-data');
        const data = await response.json();
        
        // Process the data into chart format
        const sites = [...new Set(data.rows.map(row => row.local_site_name))];
        
        // Calculate average PM2.5 for each site
        const averages = sites.map(site => {
          const siteData = data.rows.filter(row => row.local_site_name === site);
          const sum = siteData.reduce((acc, curr) => acc + parseFloat(curr.daily_mean_pm2_5_concentration), 0);
          return sum / siteData.length;
        });
        
        // Update chart data
        setChartData({
          labels: sites,
          datasets: [
            {
              label: 'Average PM2.5 Concentration',
              data: averages,
              backgroundColor: 'rgba(75, 192, 192, 0.6)',
              borderColor: 'rgba(75, 192, 192, 1)',
              borderWidth: 1,
            }
          ]
        });
      } catch (error) {
        console.error('Error fetching data:', error);
      } finally {
        setLoading(false);
      }
    }
    
    fetchData();
  }, []);
  
  const options = {
    // ... your options
  };
  
  if (loading) {
    return <div>Loading chart data...</div>;
  }
  
  return (
    <div style={{ height: '400px', width: '100%' }}>
      <Bar data={chartData} options={options} />
    </div>
  );
}

export default AirQualityChart;
```

## 📌 Advanced Customization

### 🔹 Custom Tooltips

You can create custom tooltips for more informative hover effects:

```javascript
const options = {
  // ... other options
  plugins: {
    tooltip: {
      callbacks: {
        label: function(context) {
          const label = context.dataset.label || '';
          const value = context.parsed.y;
          let status = '';
          
          // Add air quality status based on value
          if (value <= 12) status = ' (Good)';
          else if (value <= 35.4) status = ' (Moderate)';
          else if (value <= 55.4) status = ' (Unhealthy for Sensitive Groups)';
          else if (value <= 150.4) status = ' (Unhealthy)';
          else if (value <= 250.4) status = ' (Very Unhealthy)';
          else status = ' (Hazardous)';
          
          return `${label}: ${value} μg/m³${status}`;
        }
      }
    }
  }
};
```

### 🔹 Animations

You can customize the animation behavior:

```javascript
const options = {
  // ... other options
  animation: {
    duration: 2000,  // 2 seconds
    easing: 'easeOutBounce',
    delay: function(context) {
      // Delay each bar's animation by 100ms * its index
      return context.dataIndex * 100;
    }
  }
};
```

### 🔹 Responsive Design

Ensure your charts work on all screen sizes:

```javascript
// Container component for responsive charts
function ResponsiveChart({ children }) {
  return (
    <div style={{ 
      position: 'relative', 
      height: '50vh', 
      width: '100%',
      maxHeight: '600px',
      minHeight: '300px' 
    }}>
      {children}
    </div>
  );
}

// Usage
function MyChart() {
  return (
    <ResponsiveChart>
      <Bar data={data} options={{
        responsive: true,
        maintainAspectRatio: false,
        // ... other options
      }} />
    </ResponsiveChart>
  );
}
```

## 📌 Common Gotchas and Troubleshooting

### 🔹 Chart Not Rendering or Updating

If your chart isn't rendering or updating properly:

1. **Check Chart Registration**: Make sure you've registered all necessary components for your chart type.

2. **React State Updates**: When using state to update chart data, remember that state updates are asynchronous:
   ```javascript
   // Wrong
   const [chartData, setChartData] = useState(initialData);
   chartData.datasets[0].data = newData;  // This won't trigger a re-render!
   
   // Correct
   setChartData({
     ...chartData,
     datasets: [
       {
         ...chartData.datasets[0],
         data: newData
       }
     ]
   });
   ```

3. **Check Console Errors**: Look for errors in your browser console.

4. **Component Re-rendering**: If the chart disappears on re-render, you might be recreating the Chart instance:
   - Consider memoizing your options object with `useMemo`
   - Use a stable reference for the chart with `useRef`

   > 📝 **Beginner's Note on useRef**: 
   > 
   > `useRef` is a React Hook that gives you a persistent reference to an element or value that stays the same between renders.
   > 
   > Think of it like putting a sticky note on something so you can find it again later.
   > 
   > ```javascript
   > // Create a ref
   > const chartRef = useRef(null);
   > 
   > // Use the ref with your chart
   > return <Bar ref={chartRef} data={data} options={options} />;
   > ```
   > 
   > This is useful with charts because:
   > 1. It gives you a consistent reference to the chart instance
   > 2. You can access the chart's methods when needed
   > 3. It prevents the chart from being unnecessarily recreated
   > 
   > Unlike state variables that cause re-renders when they change, changing a ref's value doesn't trigger a re-render.

### 🔹 Chart Size Issues

If your chart isn't sizing correctly:

1. **Container Size**: Make sure the container has a defined height. Charts can't size properly inside a height-less container.

2. **Responsive Options**: Check that you've set `responsive: true` and `maintainAspectRatio: false` if you want full control over sizing.

3. **CSS Issues**: Check for any CSS conflicts affecting your chart container.

## 📌 Best Practices

1. **Keep Data Transformation Separate**: Separate your data fetching and transformation logic from your chart component.

2. **Use Loading States**: Show loading indicators while data is being fetched.

3. **Memoize Options**: Use `useMemo` for options objects to prevent unnecessary re-renders.

4. **Add Context**: Include proper titles, labels, and legends to make your chart self-explanatory.

5. **Use Color Meaningfully**: Choose colors that convey information, not just for decoration.

6. **Accessibility**: Consider accessibility when choosing colors (color-blind friendly palettes) and add descriptive aria-labels.

7. **Fallbacks**: Provide a table or alternative data representation for users who can't see the chart.

## 📌 Step-by-Step Checklist for Creating Charts

Now that you understand the core concepts, here's a practical checklist to follow when creating any chart with react-chartjs-2:

1. ✅ **Install the packages**
   - Install Chart.js and react-chartjs-2: `npm install chart.js react-chartjs-2`

2. ✅ **Import required components**
   - Import the chart type you need (e.g., `Bar`, `Line`, `Pie`)
   - Import necessary Chart.js components for registration

3. ✅ **Register the Chart.js components**
   - Register scales, elements, plugins needed for your chart type

4. ✅ **Set up your data structure**
   - Create labels array (categories/x-axis values)
   - Create datasets array with data values and styling

5. ✅ **Configure chart options**
   - Set responsive behavior
   - Configure scales (axes)
   - Set up plugins (title, legend, tooltip)

6. ✅ **Create a container with defined dimensions**
   - Ensure the container has a set width and height

7. ✅ **Render the chart component**
   - Add the chart component with data and options props

8. ✅ **Add loading state handling** (for real data)
   - Show loading indicator while data is being fetched
   - Handle errors gracefully

9. ✅ **Implement interactivity** (if needed)
   - Add event handlers for click/hover interactions
   - Configure tooltips for data exploration

10. ✅ **Add accessibility features**
    - Include descriptive titles and labels
    - Use accessible color schemes
    - Add ARIA attributes where necessary

Follow this checklist for each chart you create to ensure you don't miss any critical steps!

## 📌 Chart Type Component Reference

Here's what you need to register for each specific chart type:

### 🔹 Bar Chart
```javascript
// Import the Chart.js components
import {
  Chart as ChartJS,
  CategoryScale,    // For x-axis categories
  LinearScale,      // For y-axis numeric values
  BarElement,       // The bars themselves
  Title,            // For chart title
  Tooltip,          // For hover tooltips
  Legend,           // For the legend
} from 'chart.js';

// Import the Bar component from react-chartjs-2
import { Bar } from 'react-chartjs-2';

// Register the components
ChartJS.register(
  CategoryScale,
  LinearScale,
  BarElement,
  Title,
  Tooltip,
  Legend
);
```

### 🔹 Line Chart
```javascript
// Import the Chart.js components
import {
  Chart as ChartJS,
  CategoryScale,    // For x-axis categories
  LinearScale,      // For y-axis numeric values
  PointElement,     // For data points
  LineElement,      // For the lines
  Title,
  Tooltip,
  Legend,
} from 'chart.js';

// Import the Line component from react-chartjs-2
import { Line } from 'react-chartjs-2';

// Register the components
ChartJS.register(
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Title,
  Tooltip,
  Legend
);
```

### 🔹 Pie/Doughnut Chart
```javascript
// Import the Chart.js components
import {
  Chart as ChartJS,
  ArcElement,       // For pie/doughnut segments
  Tooltip,
  Legend,
} from 'chart.js';

// Import the Pie and/or Doughnut component from react-chartjs-2
import { Pie, Doughnut } from 'react-chartjs-2';

// Register the components
ChartJS.register(
  ArcElement,
  Tooltip,
  Legend
);
```

### 🔹 Scatter Chart
```javascript
// Import the Chart.js components
import {
  Chart as ChartJS,
  LinearScale,      // For both x and y axes
  PointElement,     // For data points
  Tooltip,
  Legend,
} from 'chart.js';

// Import the Scatter component from react-chartjs-2
import { Scatter } from 'react-chartjs-2';

// Register the components
ChartJS.register(
  LinearScale,
  PointElement,
  Tooltip,
  Legend
);
```

### 🔹 Radar Chart
```javascript
// Import the Chart.js components
import {
  Chart as ChartJS,
  RadialLinearScale, // For radial axes
  PointElement,
  LineElement,
  Filler,            // For area filling
  Tooltip,
  Legend,
} from 'chart.js';

// Import the Radar component from react-chartjs-2
import { Radar } from 'react-chartjs-2';

// Register the components
ChartJS.register(
  RadialLinearScale,
  PointElement,
  LineElement,
  Filler,
  Tooltip,
  Legend
);
```

## 📌 Next Steps

Now that you understand how to use react-chartjs-2, you can:

1. Experiment with different chart types for your data
2. Combine multiple charts to create a dashboard
3. Add interactivity by connecting chart events to your application
4. Explore plugins for additional functionality

Happy charting! 📊
