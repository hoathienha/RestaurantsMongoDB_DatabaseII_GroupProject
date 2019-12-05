# RestaurantsMongoDB_DatabaseII_GroupProject
A project about OLAP noSQL database created in MongoDB

Based on snowflake schema designed in mySQL workbench for restaurant database, populate data to the Fact collection 
with sample data generated from mockaroo.com (500 rows and JSON as output type).

Fact collection: Revenue
Fact:  Revenue 

{ 
"orderID": 
"product":[

{
"productID": 
"productName": 
"productCategory": 
"qty": 
"itemPrice": 
"supplierID": 
"supplierName": 
}, 
{
  "productID": 
  "productName": 
  "productCategory": 
  "qty": 
  "itemPrice": 
  "supplierID": 
  "supplierName": 
}, 
… 
] 
"employee": 
{ 
"employeeID": 
"employeeName": 
},
"date": 
{ 
  "year": 
  "month": 
  "day": 
  "hour": 
  "season": 
}, 

"customer": [

{ 
  "customerID": 
  "customerPhone": 
}, 

{
  "customerID": 
  "customerPhone": 
}, 
... 
]} 
 
# Aggregate questions useful for restaurant converted to mongoDB queries

1. Slowest hour with specific product category over the years based on the average total sales?
Insight: to keep track of the quiet hours during period of time so that can consider promotion happy hour to attract more customers.

db.restaurants.aggregate([

      {$unwind: "$product"},  

      {$group: {_id: {hour: "$date.hour", category: "$product.productCategory"},
      average_sales: {$avg: {$multiply:  ["$product.productPrice","$product.qty"]}}}}, 

      {$sort:{average_sales:1}} ,
      {$limit: 1}

])

2. Which employees served the highest number of orders(top 2) in October?
Insight: the restaurant also wants to keep track of the most effective employees in a specific month.

db.restaurants.aggregate([ 

{$match: {"date.month": 10}},

{$group: {_id: {employee: "$employee.employeeName"}, numberOfOrders: {$sum: 1}}}, 

{$sort:{numberOfOrders: -1}},

{$limit: 2}

])

3. Customer who spent the most at the restaurant in 2014(with phone number)?
Insight: to keep track regular customers and offer them special deals or discounts

db.restaurants.aggregate([

      {$unwind: "$product"},
      
      {$match: {"date.year": 2014}},

      {$group: {_id: {customerPhone: "$customer.customerPhone"},
      total_sales: {$sum: {$multiply:  ["$product.productPrice","$product.qty"]}}}}, 

      {$sort:{total_sales: -1}},
      
      {$limit: 1}

])

4. Quantity of each product category provided by all suppliers?
Insight: to track the status of food stock and the suppliers

db.restaurants.aggregate([ 

{$unwind: "$product"},  

{$group: {_id: {supplierID: "$product.supplierName", item: "$product.productName"},
quantity: {$sum: "$product.qty"}}},

{$sort:{supplierID: 1}} 

]) 

