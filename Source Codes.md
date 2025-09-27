Task to be Performed: 
Start the MongoDB shell 
```bash
C:\Users\user>mongosh
```
Or
```bash
>_MONGOSH
```
```bash
>use ecom_technet_db

switched to db ecom_technet_db

```
```js
>db.orders.insertMany([
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
```js
{
  acknowledged: true,
  insertedIds: {
    '0': ObjectId('68d7aaf537f42d625e7ee397'),
    '1': ObjectId('68d7aaf537f42d625e7ee398'),
    '2': ObjectId('68d7aaf537f42d625e7ee399')
  }
}
```
```js
>db.orders.aggregate([
  { $unwind: "$items" },
  { $match: { orderDate: { $gte: ISODate("2024-09-27") } } },
  { $group: { 
      _id: "$items.Category", 
      totalRevenue: { $sum: { $multiply: ["$items.price", "$items.quantity"] } }
  }},
  { $sort: { totalRevenue: -1 } }
]);
```
```js
  Output:
{
  _id: 'DELL',
  totalRevenue: 712
}
{
  _id: 'DataLink',
  totalRevenue: 200
}
{
  _id: 'Lenovo',
  totalRevenue: 89
}
{
  _id: 'Energizer',
  totalRevenue: 80
}
```
```js
>db.orders.aggregate([
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

```js

  Output:
{
  _id: 'AAA Baterries',
  totalUnitsSold: 5,
  avgRating: 4
}
{
  _id: 'Internet Router',
  totalUnitsSold: 2,
  avgRating: 4.9
}
{
  _id: 'Wireless Mouse',
  totalUnitsSold: 1,
  avgRating: 4.2
}
{
  _id: 'Dell Laptop',
  totalUnitsSold: 1,
  avgRating: 4.8
}

```
```js
>db.orders.aggregate([
  { $count: "totalOrders" }
]);
{
  totalOrders: 3
}
```

```js
>db.orders.aggregate([
  { $unwind: "$items" },
  { $group: { _id: "$orderId", totalQty: { $sum: "$items.quantity" } } },
  { $group: { _id: null, averageOrderQty: { $avg: "$totalQty" } } }
]);
{
  _id: null,
  averageOrderQty: 3.6666666666666665
}
```
```js
>db.orders.aggregate([
  { $unwind: "$items" },
  { $group: { _id: "$items.name", total_Units: { $sum: "$items.quantity" } } },
  { $sort: { total_Units: -1 } },
  { $limit: 1 }
]);
{
  _id: 'AAA Baterries',
  total_Units: 5
}
```
```bash
>ecom_technet_db
```

