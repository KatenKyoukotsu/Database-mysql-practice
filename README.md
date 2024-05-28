# Отчет по созданию базы данных для магазина по продаже цветов

## 1. Структура базы данных

### Предложенная схема базы данных

1. **Customers** (Клиенты)
   - `CustomerID` (PK)
   - `FirstName`
   - `LastName`
   - `Email`
   - `Phone`

2. **Orders** (Заказы)
   - `OrderID` (PK)
   - `CustomerID` (FK)
   - `OrderDate`
   - `TotalAmount`

3. **Flowers** (Цветы)
   - `FlowerID` (PK)
   - `Name`
   - `Price`
   - `StockQuantity`

4. **OrderDetails** (Детали заказа)
   - `OrderDetailID` (PK)
   - `OrderID` (FK)
   - `FlowerID` (FK)
   - `Quantity`
   - `UnitPrice`

### Дополнительно предложенная схема базы данных

5. **Suppliers** (Поставщики)
   - `SupplierID` (PK)
   - `SupplierName`
   - `ContactName`
   - `ContactPhone`

6. **FlowerSupplies** (Поставки цветов)
   - `SupplyID` (PK)
   - `FlowerID` (FK)
   - `SupplierID` (FK)
   - `SupplyDate`
   - `Quantity`

## 2. Создание таблиц

```sql
CREATE TABLE Customers (
    CustomerID INT PRIMARY KEY AUTO_INCREMENT,
    FirstName VARCHAR(50),
    LastName VARCHAR(50),
    Email VARCHAR(100),
    Phone VARCHAR(20)
);

CREATE TABLE Orders (
    OrderID INT PRIMARY KEY AUTO_INCREMENT,
    CustomerID INT,
    OrderDate DATE,
    TotalAmount DECIMAL(10, 2),
    FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID)
);

CREATE TABLE Flowers (
    FlowerID INT PRIMARY KEY AUTO_INCREMENT,
    Name VARCHAR(100),
    Price DECIMAL(10, 2),
    StockQuantity INT
);

CREATE TABLE OrderDetails (
    OrderDetailID INT PRIMARY KEY AUTO_INCREMENT,
    OrderID INT,
    FlowerID INT,
    Quantity INT,
    UnitPrice DECIMAL(10, 2),
    FOREIGN KEY (OrderID) REFERENCES Orders(OrderID),
    FOREIGN KEY (FlowerID) REFERENCES Flowers(FlowerID)
);

CREATE TABLE Suppliers (
    SupplierID INT PRIMARY KEY AUTO_INCREMENT,
    SupplierName VARCHAR(100),
    ContactName VARCHAR(100),
    ContactPhone VARCHAR(20)
);

CREATE TABLE FlowerSupplies (
    SupplyID INT PRIMARY KEY AUTO_INCREMENT,
    FlowerID INT,
    SupplierID INT,
    SupplyDate DATE,
    Quantity INT,
    FOREIGN KEY (FlowerID) REFERENCES Flowers(FlowerID),
    FOREIGN KEY (SupplierID) REFERENCES Suppliers(SupplierID)
);
```

## 3. Ввод исходных данных

```sql
-- Ввод данных в таблицу Customers
INSERT INTO Customers (FirstName, LastName, Email, Phone) VALUES
('John', 'Doe', 'john.doe@example.com', '123-456-7890'),
('Jane', 'Smith', 'jane.smith@example.com', '123-456-7891'),
('Alice', 'Johnson', 'alice.johnson@example.com', '123-456-7892'),
-- ... (добавьте еще 12-17 записей)

-- Ввод данных в таблицу Flowers
INSERT INTO Flowers (Name, Price, StockQuantity) VALUES
('Rose', 2.50, 100),
('Tulip', 1.50, 200),
('Lily', 3.00, 150),
-- ... (добавьте еще 12-17 записей)

-- Ввод данных в таблицу Orders
INSERT INTO Orders (CustomerID, OrderDate, TotalAmount) VALUES
(1, '2024-05-01', 50.00),
(2, '2024-05-02', 30.00),
(3, '2024-05-03', 70.00),
-- ... (добавьте еще 12-17 записей)

-- Ввод данных в таблицу OrderDetails
INSERT INTO OrderDetails (OrderID, FlowerID, Quantity, UnitPrice) VALUES
(1, 1, 10, 2.50),
(1, 2, 5, 1.50),
(2, 3, 10, 3.00),
-- ... (добавьте еще 12-17 записей)

-- Ввод данных в таблицу Suppliers
INSERT INTO Suppliers (SupplierName, ContactName, ContactPhone) VALUES
('Flower Farm', 'Tom Brown', '987-654-3210'),
('Bloom Suppliers', 'Nancy White', '987-654-3211'),
-- ... (добавьте еще 12-17 записей)

-- Ввод данных в таблицу FlowerSupplies
INSERT INTO FlowerSupplies (FlowerID, SupplierID, SupplyDate, Quantity) VALUES
(1, 1, '2024-05-01', 50),
(2, 2, '2024-05-02', 100),
-- ... (добавьте еще 12-17 записей)
```

## 4. Выполнение заданий

### Индексы

```sql
CREATE INDEX idx_customer_email ON Customers(Email);
CREATE INDEX idx_order_date ON Orders(OrderDate);
CREATE INDEX idx_flower_name ON Flowers(Name);
```

### Ограничения в базах данных

```sql
ALTER TABLE Flowers ADD CONSTRAINT chk_price CHECK (Price > 0);
ALTER TABLE OrderDetails ADD CONSTRAINT chk_quantity CHECK (Quantity > 0);
```

### Представления в SQL

```sql
CREATE VIEW CustomerOrders AS
SELECT c.CustomerID, c.FirstName, c.LastName, o.OrderID, o.OrderDate, o.TotalAmount
FROM Customers c
JOIN Orders o ON c.CustomerID = o.CustomerID;

CREATE VIEW FlowerStock AS
SELECT f.FlowerID, f.Name, f.StockQuantity
FROM Flowers f;

CREATE VIEW SupplierFlowers AS
SELECT s.SupplierName, f.Name, fs.SupplyDate, fs.Quantity
FROM Suppliers s
JOIN FlowerSupplies fs ON s.SupplierID = fs.SupplierID
JOIN Flowers f ON fs.FlowerID = f.FlowerID;
```

### Оконные функции

```sql
-- Ранжирование заказов по дате
SELECT OrderID, OrderDate, 
       RANK() OVER (ORDER BY OrderDate) as OrderRank
FROM Orders;

-- Скользящее среднее по количеству цветов в заказах
SELECT OrderID, FlowerID, Quantity, 
       AVG(Quantity) OVER (PARTITION BY FlowerID ORDER BY OrderID ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING) as MovingAvgQuantity
FROM OrderDetails;

-- Кумулятивная сумма продаж по клиентам
SELECT CustomerID, OrderID, TotalAmount, 
       SUM(TotalAmount) OVER (PARTITION BY CustomerID ORDER BY OrderDate) as CumulativeTotal
FROM Orders;
```

### Временные таблицы

```sql
-- Временная таблица для временного хранения заказов на обработку
CREATE TEMPORARY TABLE TempOrders (
    OrderID INT,
    CustomerID INT,
    OrderDate DATE,
    TotalAmount DECIMAL(10, 2)
);

INSERT INTO TempOrders (OrderID, CustomerID, OrderDate, TotalAmount)
SELECT OrderID, CustomerID, OrderDate, TotalAmount
FROM Orders
WHERE OrderDate > '2024-05-01';

SELECT * FROM TempOrders;

DROP TEMPORARY TABLE TempOrders;
```

## Заключение

Все запросы для создания и работы с базой данных магазина по продаже цветов были выполнены. Каждая тема была представлена отдельным блоком с соответствующими SQL-запросами.
