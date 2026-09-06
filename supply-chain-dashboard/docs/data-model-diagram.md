# Data Model — Star Schema

GitHub renders Mermaid diagrams natively, so this will display as a diagram directly on the repo page.

```mermaid
erDiagram
    FactOrders }o--|| DimCustomer : "Customer_Id"
    FactOrders }o--|| DimProduct : "Product_Id"
    FactOrders }o--|| DimDepartment : "Department_Id"
    FactOrders }o--|| DimDate : "Order Date (active)"
    FactOrders }o--|| DimDate : "Shipping Date (inactive, via USERELATIONSHIP)"

    FactOrders {
        int Order_Id
        int Customer_Id
        int Product_Id
        int Department_Id
        date OrderDate
        date ShippingDate
        int Late_delivery_risk
        float Sales
        float Profit
    }

    DimCustomer {
        int Customer_Id
        string Segment
        string Country
        string Region
    }

    DimProduct {
        int Product_Id
        string Product_Name
        string Category
    }

    DimDepartment {
        int Department_Id
        string Department_Name
    }

    DimDate {
        date Date
        int Year
        int Month
        string MonthName
        int Quarter
    }
```

## Why dual date relationships?

Orders need to be analyzed on two different timelines — when they were **placed** (Order Date) and when they were **shipped** (Shipping Date). Power BI only allows one *active* relationship between two tables at a time, so:

- The relationship on **Order Date** is active by default (used in most measures).
- The relationship on **Shipping Date** is kept inactive and activated inside specific DAX measures using `USERELATIONSHIP()`, e.g. `Orders Shipped (by Ship Date)`.

This avoids duplicating the fact table or the date table just to support two timelines.
