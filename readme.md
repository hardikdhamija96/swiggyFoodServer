# Grubly Server

## Description

This is a Node.js server for the Grubly application, which provides endpoints to fetch restaurant listings and details from the Swiggy API. The server uses Express.js and includes CORS support for handling cross-origin requests.

## Issue

When making API requests from the Grubly React application to the Swiggy API, the browser blocks these requests due to CORS (Cross-Origin Resource Sharing) restrictions. The Swiggy API does not have CORS headers configured to allow requests from different origins, leading to issues when trying to fetch data directly from the frontend.

## Server as Solution

The Node.js server is created to act as a backend proxy to handle CORS issues. By setting up this server, we can:

- **Forward Requests**: The server forwards requests from the React application to the Swiggy API.
- **Handle CORS**: The server is configured to include appropriate CORS headers in its responses, which allows the React application to communicate with it without encountering CORS errors.
- **Aggregate Data**: The server can fetch data from the Swiggy API and pass it back to the React application, providing a seamless integration and avoiding direct client-side API calls.

This approach ensures that the React application can retrieve the necessary data without running into CORS-related problems, as all API requests are handled server-side.


## Approach

### 1. Setting Up the Environment

The first step was to set up the Node.js environment and initialize a new project. This involves creating a `package.json` file to manage dependencies.

**Code:**
```bash
npm init -y 
```


### 2. Installing Dependencies

I installed the necessary packages to build the server. The following packages were included:

- **Express**: A minimal and flexible Node.js web application framework that provides a robust set of features for web and mobile applications.
- **CORS**: A middleware for enabling Cross-Origin Resource Sharing (CORS) in Express applications, which is essential for handling cross-origin requests.
- **Cross-Fetch**: A lightweight package that allows making HTTP requests in Node.js, providing a polyfill for the Fetch API.

```bash
npm install express cors cross-fetch

```

### 3. Creating the Server

Next, I created a file called `index.js` where the server logic is defined. This file includes the basic setup for an Express server, enabling CORS, and defining API endpoints.

**Code:**

```javascript
const express = require('express');
const cors = require('cors');
const fetch = require('cross-fetch');

const app = express();
const port = 3000;

// Enable CORS
app.use(cors());

// Basic route
app.get('/', (req, res) => {
    res.json({"test": "Welcome to Grubly Server !!!"});
});

// Start server
app.listen(port, () => {
  console.log(`Server is listening on port ${port}`);
});

```

### Defining API Endpoints

I defined two primary API endpoints for the server:

1. **List of Restaurants**: This endpoint fetches a list of restaurants based on latitude and longitude. It interacts with the Swiggy API to retrieve restaurant data.

2. **Restaurant Details**: This endpoint provides detailed information about a specific restaurant using its ID, latitude, and longitude.

#### Code:

```javascript
// Endpoint for list of restaurants
app.get('/api/restaurants', (req, res) => {
    const { lat, lng } = req.query;
    const url = `https://www.swiggy.com/dapi/restaurants/list/v5?lat=${lat}&lng=${lng}&page_type=DESKTOP_WEB_LISTING`;

    fetch(url, {
      headers: {
        'Content-Type': 'application/json',
        'User-Agent': 'Mozilla/5.0'
      }
    })
    .then(response => response.json())
    .then(data => res.json(data))
    .catch(error => {
      console.error(error);
      res.status(500).send('An error occurred');
    });
});

// Endpoint for restaurant details
app.get('/api/restaurants/restaurantDetail', (req, res) => {
    const { lat, lng, resId } = req.query;
    const url = `https://www.swiggy.com/dapi/menu/pl?page-type=REGULAR_MENU&complete-menu=true&lat=${lat}&lng=${lng}&restaurantId=${resId}`;

    fetch(url, {
      headers: {
        'Content-Type': 'application/json',
        'User-Agent': 'Mozilla/5.0'
      }
    })
    .then(response => response.json())
    .then(data => res

```

### 5. Testing the Server

Once the server was set up, I tested the endpoints to ensure they were working correctly. You can test the server endpoints using a web browser or tools like Postman.

#### List of Restaurants Endpoint

To test the list of restaurants endpoint, open a web browser and enter the following URL with appropriate query parameters:

**Example Request URL:**


[http://localhost:3000/api/restaurants/restaurantDetail?lat=30.3131722&lng=76.38462179999999&resId=147375](http://localhost:3000/api/restaurants/restaurantDetail?lat=30.3131722&lng=76.38462179999999&resId=147375)

**Response should be same as:**

[https://www.swiggy.com/dapi/menu/pl?page-type=REGULAR_MENU&complete-menu=true&lat=30.3131722&lng=76.38462179999999&restaurantId=147375](https://www.swiggy.com/dapi/menu/pl?page-type=REGULAR_MENU&complete-menu=true&lat=30.3131722&lng=76.38462179999999&restaurantId=147375)
