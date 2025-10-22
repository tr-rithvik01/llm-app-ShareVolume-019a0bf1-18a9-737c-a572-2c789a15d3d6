# Citizens Financial Group (CFG) Shares Outstanding Tracker

## 1. Project Title and Description
This web application provides an interactive view of "Entity Common Stock Shares Outstanding" data primarily for Citizens Financial Group (CFG) but also supports dynamic lookups for other companies via their CIK. It fetches data from the SEC EDGAR API, processes it to find the maximum and minimum shares outstanding for fiscal years after 2020, and presents this information in a clear and visually appealing manner.

## 2. Setup Instructions

To run this application locally, follow these steps:

1.  **Clone the Repository:**
    ```bash
    git clone <repository-url>
    cd <repository-name>
    ```
    (Replace `<repository-url>` with the actual URL and `<repository-name>` with the appropriate directory name.)

2.  **Serve Static Files:**
    This is a static web application, so you'll need a simple web server to serve the `index.html`, `data.json`, and other files.

    *   **Using Python (Recommended for simplicity):**
        If you have Python installed, navigate to the project's root directory in your terminal and run:
        ```bash
        python -m http.server 8000
        ```
        Then, open your web browser and go to `http://localhost:8000`.

    *   **Using Node.js `serve` package:**
        If you have Node.js installed, you can install the `serve` package globally:
        ```bash
        npm install -g serve
        ```
        Then, in the project's root directory, run:
        ```bash
        serve -p 8000
        ```
        And open `http://localhost:8000` in your browser.

3.  **Understanding `data.json`:**
    The `data.json` file is pre-generated for Citizens Financial Group (CIK 0000759944). It contains the `entityName`, `max` (highest shares outstanding), and `min` (lowest shares outstanding) for fiscal years greater than 2020. This file is loaded by default when `index.html` is opened without any URL parameters.

    *   **How `data.json` was generated (for informational purposes):**
        A script would typically perform the following steps:
        *   Make an HTTP GET request to `https://data.sec.gov/api/xbrl/companyconcept/CIK0000759944/dei/EntityCommonStockSharesOutstanding.json`.
        *   **Important:** The SEC requires a descriptive `User-Agent` header for all API requests. Example: `User-Agent: CFGApp/1.0 (contact@cfg.com)`. This header must be set by the client making the request to the SEC API (e.g., a server-side script or a proxy).
        *   Parse the JSON response.
        *   Extract the `entityName` (which was specified as "Citizens Financial Group").
        *   Filter the `units.shares` array for entries where `fy` (fiscal year) is greater than "2020" and `val` is a numeric value.
        *   Identify the entry with the maximum `val` and the entry with the minimum `val` from the filtered list.
        *   Save the `entityName`, `max` (containing `val` and `fy`), and `min` (containing `val` and `fy`) into `data.json`.

4.  **CORS Proxy for Dynamic CIK Lookup:**
    When using the `?CIK=` URL parameter, the application fetches data directly from the SEC API for a different CIK. Due to Cross-Origin Resource Sharing (CORS) restrictions, a proxy server is required. The provided `index.html` uses `https://api.allorigins.win/raw?url=` as a public CORS proxy for demonstration purposes. For production environments or if this proxy becomes unreliable, it is recommended to set up your own CORS proxy. When using a proxy for dynamic lookups, the proxy server itself is responsible for sending the descriptive `User-Agent` header to the SEC.

## 3. Usage Guide

1.  **View Default Data (Citizens Financial Group):**
    Open `index.html` directly in your web browser (e.g., `http://localhost:8000/index.html`). This will load and display the shares outstanding data for "Citizens Financial Group" from the `data.json` file.

2.  **View Data for Another Company (Dynamic Lookup):**
    To view data for a different company, append a `?CIK=` query parameter to the URL in your browser's address bar, followed by the 10-digit CIK of the desired company.

    *   **Example (Apple Inc., CIK 0001018724):**
        Navigate to: `http://localhost:8000/index.html?CIK=0001018724`

    The page will dynamically fetch the data for the specified CIK (using the CORS proxy), process it, and update the title, heading, and min/max share values without reloading the entire page.

## 4. Code Explanation

The project consists of the following files:

*   **`index.html`**:
    *   This is the main front-end file. It contains the HTML structure, basic CSS for styling, and JavaScript logic.
    *   **HTML Structure**: Defines the layout with an `<h1>` for the entity name and `<span>` elements with specific IDs (`share-max-value`, `share-max-fy`, `share-min-value`, `share-min-fy`) to display the shares outstanding data.
    *   **CSS Styling**: Provides a clean and readable interface for the data.
    *   **JavaScript Logic**:
        *   Executes on `DOMContentLoaded`.
        *   Checks for a `CIK` query parameter in the URL.
        *   If `CIK` is present and valid, it constructs a request URL using a CORS proxy to fetch data from the SEC API for that specific CIK.
        *   If no `CIK` parameter is found, it fetches data from the local `data.json` file.
        *   The `fetchData` function handles the API request, including error handling and displaying loading/error messages.
        *   The `processAndRender` function takes the fetched JSON data, filters the `units.shares` array for `fy > "2020"` and numeric `val`s, calculates the max and min shares outstanding, and then updates all relevant DOM elements (`<title>`, `<h1>`, and `<span>`s).

*   **`data.json`**:
    *   A static JSON file containing the pre-processed "Entity Common Stock Shares Outstanding" data for Citizens Financial Group (CIK 0000759944). This serves as the default data source.

*   **`LICENSE`**:
    *   Contains the full text of the MIT License, under which this project is distributed.

*   **`uid.txt`**:
    *   A simple text file containing a unique identifier as specified in the project requirements.

## 5. License Information
This project is licensed under the MIT License. See the `LICENSE` file for full details.
