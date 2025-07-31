# Database Summary

## Overview
Quick reference for the most important database tables and relationships. For complete schema, see [DATABASE.md](../detailed/DATABASE.md).

## Core Tables

### users
Primary user account information.

```sql
- id: UUID (PK)
- email: VARCHAR(255) UNIQUE NOT NULL
- name: VARCHAR(100) NOT NULL
- role: ENUM('admin', 'user', 'guest') NOT NULL
- created_at: TIMESTAMP
- updated_at: TIMESTAMP
```

**Key Indexes**: email, role, created_at
**Relationships**: Has many orders, sessions, activities

### products
Product catalog information.

```sql
- id: UUID (PK)
- name: VARCHAR(200) NOT NULL
- description: TEXT
- price: DECIMAL(10,2) NOT NULL
- status: ENUM('active', 'inactive', 'archived')
- created_at: TIMESTAMP
- updated_at: TIMESTAMP
```

**Key Indexes**: status, name, created_at
**Relationships**: Has many order_items, reviews

### orders
Customer order records.

```sql
- id: UUID (PK)
- user_id: UUID (FK -> users.id)
- status: ENUM('pending', 'processing', 'completed', 'cancelled')
- total_amount: DECIMAL(10,2) NOT NULL
- created_at: TIMESTAMP
- updated_at: TIMESTAMP
```

**Key Indexes**: user_id, status, created_at
**Relationships**: Belongs to user, has many order_items

### order_items
Individual items within orders.

```sql
- id: UUID (PK)
- order_id: UUID (FK -> orders.id)
- product_id: UUID (FK -> products.id)
- quantity: INTEGER NOT NULL
- unit_price: DECIMAL(10,2) NOT NULL
- subtotal: DECIMAL(10,2) NOT NULL
```

**Key Indexes**: order_id, product_id
**Relationships**: Belongs to order and product

## Key Relationships

```
users (1) ──── (N) orders
orders (1) ──── (N) order_items
products (1) ──── (N) order_items
```

## Common Queries

### Get User Orders
```sql
SELECT o.*, COUNT(oi.id) as item_count, 
       SUM(oi.quantity) as total_items
FROM orders o
LEFT JOIN order_items oi ON o.id = oi.order_id
WHERE o.user_id = $1
GROUP BY o.id
ORDER BY o.created_at DESC;
```

### Product Sales Summary
```sql
SELECT p.*, 
       COUNT(DISTINCT oi.order_id) as order_count,
       SUM(oi.quantity) as total_sold,
       SUM(oi.subtotal) as revenue
FROM products p
LEFT JOIN order_items oi ON p.id = oi.product_id
LEFT JOIN orders o ON oi.order_id = o.id 
  AND o.status = 'completed'
GROUP BY p.id;
```

### Active Users (Last 30 Days)
```sql
SELECT COUNT(DISTINCT user_id) as active_users
FROM orders
WHERE created_at >= NOW() - INTERVAL '30 days';
```

## Database Conventions

1. **Primary Keys**: Always UUID, named `id`
2. **Foreign Keys**: Named as `{table}_id`
3. **Timestamps**: Every table has `created_at` and `updated_at`
4. **Soft Deletes**: Use `deleted_at` timestamp, not hard deletes
5. **Indexes**: Add for all foreign keys and commonly queried fields
6. **Naming**: Snake_case for all database objects

## Performance Considerations

- Indexes exist on all foreign keys
- Composite indexes for common query patterns
- Partitioning for large tables (orders by month)
- Regular VACUUM and ANALYZE scheduled

## Backup & Recovery

- Daily full backups at 2 AM UTC
- Point-in-time recovery enabled
- 30-day retention policy
- Monthly backup tests

---

*For complete schema with all tables, constraints, and functions, see [DATABASE.md](../detailed/DATABASE.md)*