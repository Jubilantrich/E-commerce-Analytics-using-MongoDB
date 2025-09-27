# MongoDB Aggregation Project Implementation: E-commerce Technet Reports

## 1. Project Overview

The e-commerce store **Technet** requires product performance reports to support decision-making. The goal is to use **MongoDB’s Aggregation Framework** to efficiently calculate key metrics such as revenue, product popularity, and order statistics.

We designed and implemented the solution using a custom database, `ecom_technet_db`, with a collection named `orders`. The dataset simulates customer purchases, including items purchased, product categories, prices, and ratings.

---

## 2. Database and Collection Setup

### Step 1: Start MongoDB Shell

```bash
mongosh
```

### Step 2: Create and Switch to Database

```js
use ecom_technet_db
```

### Step 3: Create `orders` Collection and Insert Sample Data

```js
db.orders.insertMany([
  { 
    orderId: 1,
    customerId: "CT0001",
    contact: "+233 546789022",
    orderDate: ISODate("2025-09-25"),
    items: [
      { productId: "PT001", name: "Dell Laptop", Category: "DELL", price: 356, quantity: 1, rating: 4.8 },
      { productId: "PT002", name: "Wireless Mouse", Category: "Lenovo", price: 33, quantity: 1, rating: 4.2 },
      { productId: "PT003", name: "AAA Baterries", Category: "Energizer", price: 16, quantity: 5, rating: 4.0 }
    ]
  },
  { 
    orderId: 2,
    customerId: "CT0002",
    contact: "+233982343544",
    orderDate: ISODate("2024-09-27"),
    items: [
      { productId: "PT001", name: "Dell Laptop", Category: "DELL", price: 356, quantity: 1, rating: 4.8 },
      { productId: "PT004", name: "Wireless Keyboard", Category: "Lenovo", price: 56, quantity: 1, rating: 4.5 }
    ]
  },
  { 
    orderId: 3,
    customerId: "CT0003",
    contact: "+233 546783459",
    orderDate: ISODate("2025-09-27"),
    items: [
      { productId: "PT006", name: "Internet Router", Category: "DataLink", price: 100, quantity: 2, rating: 4.9 }
    ]
  }
]);
```

---

## 3. Aggregation Queries

### a) **Total Revenue by Product Category (Past Year)**

```js
db.orders.aggregate([
  { $unwind: "$items" },
  { $match: { orderDate: { $gte: ISODate("2024-09-27") } } },
  { $group: { 
      _id: "$items.Category", 
      totalRevenue: { $sum: { $multiply: ["$items.price", "$items.quantity"] } }
  }},
  { $sort: { totalRevenue: -1 } }
]);
```

---

### b) **Top 10 Most Popular Products (By Units Sold in Past Quarter) with Avg Rating**

```js
db.orders.aggregate([
  { $unwind: "$items" },
  { $match: { orderDate: { $gte: ISODate("2025-07-01"), $lte: ISODate("2025-09-30") } } },
  { $group: { 
      _id: "$items.name", 
      totalUnitsSold: { $sum: "$items.quantity" }, 
      avgRating: { $avg: "$items.rating" }
  }},
  { $sort: { totalUnitsSold: -1 } },
  { $limit: 10 }
]);
```

---

### c) **Total Number of Orders**

```js
db.orders.aggregate([
  { $count: "totalOrders" }
]);
```

---

### d) **Average Order Quantity**

```js
db.orders.aggregate([
  { $unwind: "$items" },
  { $group: { _id: "$orderId", totalQty: { $sum: "$items.quantity" } } },
  { $group: { _id: null, averageOrderQty: { $avg: "$totalQty" } } }
]);
```

---

### e) **Most Frequently Purchased Product (All-Time)**

```js
db.orders.aggregate([
  { $unwind: "$items" },
  { $group: { _id: "$items.name", total_Units: { $sum: "$items.quantity" } } },
  { $sort: { total_Units: -1 } },
  { $limit: 1 }
]);
```

---

## 4. Expected Insights

- **Revenue by Category:** Highlights which categories contribute most financially.
- **Top Products:** Identifies bestsellers and allows performance tracking via customer ratings.
- **Order Metrics:** Provides operational insights (total orders, average quantity).
- **Frequent Products:** Helps detect evergreen items to promote further.

---
