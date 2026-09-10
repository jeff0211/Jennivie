# **Jennivié PWA: Application Structure**

This document outlines the architecture and structural flow of the **Jennivié** Progressive Web App (PWA). The application is built entirely within a single HTML file using vanilla JavaScript, Tailwind CSS for styling, and Chart.js for data visualization.

## **1\. Core Technologies**

* **Structure:** HTML5  
* **Styling:** Tailwind CSS (via CDN)  
* **Icons:** Font Awesome 6  
* **Charts:** Chart.js  
* **Logic & State:** Vanilla JavaScript (ES6)

## **2\. Data Models & State Management**

The application does not use a backend database; instead, it relies on an in-memory state object to manage data during runtime.

### **The Catalog**

The menu is hardcoded based on the "Jennivie spreadsheet.xlsx" references:

* id: Unique identifier.  
* name: Drink name.  
* cost: Internal cost (used for profit calculation).  
* price: Selling price (used for revenue calculation).  
* img: Placeholder image URL.

### **The Application State (state object)**

let state \= {  
    orders: \[\],              // Array storing all completed orders (historical data)  
    logs: \[\],                // Array storing activity logs (History Tab)  
    currentCart: \[\],         // Array holding items currently pending in the Order Tab  
    privacyMode: true,       // Boolean toggling the visibility of profit metrics  
    selectedDrinkForModal: null, // Holds the current drink selected for quantity adjustment  
    modalQty: 1              // Holds the temporary quantity before adding to cart  
};

## **3\. UI Layout & View Structure**

The UI uses a Single Page Application (SPA) approach. All "pages" exist within the HTML but are hidden or revealed using CSS classes (hidden) controlled by JavaScript.

* **App Container (\#app-container):** Constrains the app to a mobile-friendly width (max 480px) and hides overflow to mimic a native app feel.  
* **Header (\<header\>):** Sticky top bar showing the app name.  
* **Main Content (\<main\>):** The scrollable area where the specific views are rendered.  
* **Bottom Navigation (\<nav\>):** Fixed bottom bar with 4 tabs controlling which view is currently active.

### **The 4 Main Views**

1. **Dashboard (\#view-dashboard)**  
   * **Controls:** Timeframe selector (Today, Week, Month, All) and Privacy Toggle.  
   * **KPI Cards:** Total Revenue and Total Profit (obfuscated if Privacy Mode is ON).  
   * **Charts:**  
     * Revenue Overview (Bar Chart)  
     * Profit & Loss Overview (Bar Chart, hidden if Privacy Mode is ON)  
     * Quantity Sold (Volume) Overview (Bar Chart, includes a dropdown filter for specific drinks).  
2. **Order Tab (\#view-order)**  
   * **Catalog Grid:** Displays all available drinks with images and prices.  
   * **Current Order (Cart):** Displays selected items, their quantities, and total price.  
   * **Actions:** "Cancel" and "Complete" buttons.  
3. **History Tab (\#view-history)**  
   * Displays a chronological list of activities (Orders completed, Orders cancelled).  
   * Reads directly from the state.logs array.  
4. **Report Tab (\#view-report)**  
   * **Controls:** Timeframe selector.  
   * **Output:** Generates a raw CSV string in a read-only textarea with a "Copy" button for easy export to Excel/Sheets.

## **4\. Modals & Notifications**

To avoid disruptive browser default alerts (alert(), prompt()), the app uses custom HTML overlays:

* **Quantity Modal (\#modal-overlay):** Slides up from the bottom when a drink is tapped, allowing the user to increment/decrement the quantity before adding it to the cart.  
* **Toast Notification (\#toast):** A small, temporary banner that drops down from the top of the screen to confirm actions (e.g., "Added to Order", "Order Completed", "CSV Copied").

## **5\. Core Functions (JavaScript Logic)**

* init(): Bootstraps the app, generates mock historical data, populates filters, and renders the initial views.  
* switchTab(tabId): Handles the logic of hiding all views and revealing the selected view, updating navigation styling, and triggering view-specific updates (like redrawing charts).  
* updateDashboard(): Filters the state.orders array based on the selected timeframe, recalculates KPIs, aggregates data for the charts, and calls renderChart().  
* renderCatalog() / renderCart(): Dynamically generates HTML for the menu and the shopping cart based on the DRINKS\_CATALOG and state.currentCart.  
* completeOrder(): Calculates final revenue and profit for the cart, pushes the data to state.orders, logs the activity, and clears the cart.  
* generateCSV(): Iterates through filtered state.orders to format a structured CSV string containing aggregated totals and line-by-line item data.