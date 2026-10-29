# office-furniture-sales-powerbi

# E-Commerce Business Performance Analysis

This project analyzes e-commerce order performance, customer demographics, sales trends, and order cancellations using a 12-month sample dataset.

---

## 1. Objective

This analysis answers three primary business questions:

1. **How is the business performing?** (Orders, revenue, completion rates, and monthly trends)
2. **Who are the customers and what do they buy?** (Demographics, purchasing behavior, and product preferences)
3. **Where is revenue lost to cancelled orders?** (Cancellation rates across payment methods, shipping types, and categories)

---

## 2. Data Overview

The analysis is based on five relational CSV tables covering the period from September 24, 2023 to September 23, 2024 (9,999 total orders across 6,128 distinct customers).

| Table | Content & Key Fields |
| :--- | :--- |
| **orders** | One row per order (date, status, quantity, feedback, foreign keys) |
| **customer** | Customer details (customer id, age, gender) |
| **product** | Product details (product id, product type, SKU, unit price) |
| **payment** | Payment information (payment method and type) |
| **shipping** | Fulfillment details (shipping type and category) |

---

## 3. Data Cleaning

- Standardized inconsistent order status labels (such as misspelled "Completed" and "Cancelled" variations) into two definitive values.
- Split the combined `quantity:cost` column into separate numeric `quantity` and `cost` columns.
- Verified foreign key integrity across all tables: every order record successfully maps to a valid customer, product, payment, and shipping record.

---

## 4. Data Model

The project uses a standard star schema layout. Relationships are one-to-many from the dimension tables (`customer`, `product`, `payment`, `shipping`) to the central `orders` fact table, with filter direction flowing strictly from 
dimension to fact table.

<img width="1137" height="432" alt="Screenshot 2026-10-29 at 15 24 01" src="https://github.com/user-attachments/assets/3e21c93f-3759-4391-814e-6d76b9ec4c49" />

---

## 5. Key DAX Calculations

### Measures

```dax
// Revenue of completed orders only
M_revenue = 
CALCULATE(
    SUMX(orders, orders[quantity] * RELATED('product'[unit price])),
    orders[order status] = "Completed"
)

// Order completion rate
Complete rate = 
DIVIDE(
    CALCULATE(COUNTROWS(orders), orders[order status] = "Completed"), 
    COUNTROWS(orders)
)

// Average orders per customer
Orders per Customer = 
DIVIDE(
    COUNTROWS(orders), 
    DISTINCTCOUNT(orders[customer id])
)

// Revenue per completed customer
Revenue per Customer = 
DIVIDE(
    [M_revenue],
    CALCULATE(DISTINCTCOUNT(orders[customer id]), orders[order status] = "Completed")
)

// Percentage of repeat customers
Repeat Customer Rate = 
VAR t = ADDCOLUMNS(VALUES(orders[customer id]), "n", CALCULATE(COUNTROWS(orders)))
RETURN 
    DIVIDE(COUNTROWS(FILTER(t, [n] > 1)), COUNTROWS(t))

// Age group categorization in customer table
M_Age Group = 
SWITCH(TRUE(),
    customer[age] <= 25, "18-25",
    customer[age] <= 35, "26-35",
    customer[age] <= 45, "36-45",
    customer[age] <= 55, "46-55",
    customer[age] <= 65, "56-65",
    "66-80"
),
```
---

## 6. Data Visualization 

<img width="1250" height="727" alt="Screenshot 2026-10-29 at 15 08 12" src="https://github.com/user-attachments/assets/72e66591-e6c7-4711-ae8d-c1b55457351e" />

<img width="1246" height="714" alt="Screenshot 2026-10-29 at 15 21 59" src="https://github.com/user-attachments/assets/98958c99-e433-4663-88ee-09d39cb420ff" />





