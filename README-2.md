# Отчет по созданию базы данных для магазина по продаже цветов

## 1. Разработка схемы базы данных

### Исходная схема базы данных

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

## 2. Создание базы данных

```sql
CREATE DATABASE FlowerShop;
USE FlowerShop;
```

## 3. Создание таблиц

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

## 4. Ввод исходных данных

```sql
-- Ввод данных в таблицу Customers
INSERT INTO Customers (FirstName, LastName, Email, Phone) VALUES
('John', 'Doe', 'john.doe@example.com', '123-456-7890'),
('Jane', 'Smith', 'jane.smith@example.com', '123-456-7891'),
('Alice', 'Johnson', 'alice.johnson@example.com', '123-456-7892'),
-- (добавьте еще 12-17 записей)

-- Ввод данных в таблицу Flowers
INSERT INTO Flowers (Name, Price, StockQuantity) VALUES
('Rose', 2.50, 100),
('Tulip', 1.50, 200),
('Lily', 3.00, 150),
-- (добавьте еще 12-17 записей)

-- Ввод данных в таблицу Orders
INSERT INTO Orders (CustomerID, OrderDate, TotalAmount) VALUES
(1, '2024-05-01', 50.00),
(2, '2024-05-02', 30.00),
(3, '2024-05-03', 70.00),
-- (добавьте еще 12-17 записей)

-- Ввод данных в таблицу OrderDetails
INSERT INTO OrderDetails (OrderID, FlowerID, Quantity, UnitPrice) VALUES
(1, 1, 10, 2.50),
(1, 2, 5, 1.50),
(2, 3, 10, 3.00),
-- (добавьте еще 12-17 записей)

-- Ввод данных в таблицу Suppliers
INSERT INTO Suppliers (SupplierName, ContactName, ContactPhone) VALUES
('Flower Farm', 'Tom Brown', '987-654-3210'),
('Bloom Suppliers', 'Nancy White', '987-654-3211'),
-- (добавьте еще 12-17 записей)

-- Ввод данных в таблицу FlowerSupplies
INSERT INTO FlowerSupplies (FlowerID, SupplierID, SupplyDate, Quantity) VALUES
(1, 1, '2024-05-01', 50),
(2, 2, '2024-05-02', 100),
-- (добавьте еще 12-17 записей)
```

## 5. Манипуляции с таблицами

### Фильтрация данных в SQL: WHERE

```sql
-- Пример 1: Найти все заказы, сделанные после 1 мая 2024 года
SELECT * FROM Orders WHERE OrderDate > '2024-05-01';

-- Пример 2: Найти все цветы с ценой выше 2 долларов
SELECT * FROM Flowers WHERE Price > 2.00;

-- Пример 3: Найти всех клиентов с фамилией 'Smith'
SELECT * FROM Customers WHERE LastName = 'Smith';
```

### Сортировка в SQL: ORDER BY

```sql
-- Пример 1: Сортировать заказы по дате заказа
SELECT * FROM Orders ORDER BY OrderDate;

-- Пример 2: Сортировать цветы по цене по убыванию
SELECT * FROM Flowers ORDER BY Price DESC;

-- Пример 3: Сортировать клиентов по фамилии
SELECT * FROM Customers ORDER BY LastName;
```

### Вставка и изменение данных в SQL

```sql
-- Пример 1: Вставка нового клиента
INSERT INTO Customers (FirstName, LastName, Email, Phone) 
VALUES ('Emily', 'Clark', 'emily.clark@example.com', '123-456-7893');

-- Пример 2: Обновление количества на складе для конкретного цветка
UPDATE Flowers SET StockQuantity = StockQuantity + 50 WHERE Name = 'Rose';

-- Пример 3: Удаление заказа по ID
DELETE FROM Orders WHERE OrderID = 1;
```

### Группировки и фильтрация в SQL: HAVING

```sql
-- Пример 1: Найти общую сумму заказов для каждого клиента, у которых общая сумма больше 100 долларов
SELECT CustomerID, SUM(TotalAmount) as TotalSpent
FROM Orders
GROUP BY CustomerID
HAVING SUM(TotalAmount) > 100;

-- Пример 2: Найти количество различных цветов, у которых запас больше 100
SELECT COUNT(DISTINCT FlowerID) as FlowerCount
FROM Flowers
WHERE StockQuantity > 100;

-- Пример 3: Найти среднюю цену цветов у поставщиков, где средняя цена выше 2 долларов
SELECT SupplierID, AVG(Price) as AvgPrice
FROM FlowerSupplies fs
JOIN Flowers f ON fs.FlowerID = f.FlowerID
GROUP BY SupplierID
HAVING AVG(Price) > 2.00;
```

### Запрос данных из нескольких таблиц: JOIN

```sql
-- Пример 1: Получить список всех заказов с информацией о клиентах
SELECT o.OrderID, o.OrderDate, c.FirstName, c.LastName
FROM Orders o
JOIN Customers c ON o.CustomerID = c.CustomerID;

-- Пример 2: Получить список всех деталей заказов с информацией о цветах
SELECT od.OrderDetailID, f.Name, od.Quantity, od.UnitPrice
FROM OrderDetails od
JOIN Flowers f ON od.FlowerID = f.FlowerID;

-- Пример 3: Получить список всех поставок с информацией о поставщиках и цветах
SELECT fs.SupplyID, s.SupplierName, f.Name, fs.SupplyDate, fs.Quantity
FROM FlowerSupplies fs
JOIN Suppliers s ON fs.SupplierID = s.SupplierID
JOIN Flowers f ON fs.FlowerID = f.FlowerID;
```

### Типы соединений в SQL

```sql
-- Внутреннее соединение (INNER JOIN)
SELECT o.OrderID, c.FirstName, c.LastName
FROM Orders o
INNER JOIN Customers c ON o.CustomerID = c.CustomerID;

-- Левое соединение (LEFT JOIN)
SELECT o.OrderID, c.FirstName, c.LastName
FROM Orders o
LEFT JOIN Customers c ON o.CustomerID = c.CustomerID;

-- Правое соединение (RIGHT JOIN)
SELECT o.OrderID, c.FirstName, c.LastName
FROM Orders o
RIGHT JOIN Customers c ON o.CustomerID = c.CustomerID;
```

### Подзапросы

```sql
-- Пример 1: Найти все заказы, сделанные клиентами с фамилией 'Smith'
SELECT * FROM Orders
WHERE CustomerID IN (SELECT CustomerID FROM Customers WHERE LastName = 'Smith');

-- Пример 

2: Найти цветы, которых нет в заказах
SELECT * FROM Flowers
WHERE FlowerID NOT IN (SELECT FlowerID FROM OrderDetails);

-- Пример 3: Найти клиентов, которые сделали заказы на сумму больше 100 долларов
SELECT * FROM Customers
WHERE CustomerID IN (SELECT CustomerID FROM Orders GROUP BY CustomerID HAVING SUM(TotalAmount) > 100);
```

### Транзакции

```sql
START TRANSACTION;

-- Обновить количество на складе
UPDATE Flowers SET StockQuantity = StockQuantity - 10 WHERE FlowerID = 1;

-- Вставить новый заказ
INSERT INTO Orders (CustomerID, OrderDate, TotalAmount) VALUES (1, '2024-05-10', 25.00);

-- Вставить детали заказа
INSERT INTO OrderDetails (OrderID, FlowerID, Quantity, UnitPrice) VALUES (LAST_INSERT_ID(), 1, 10, 2.50);

COMMIT;
```

### Использование оператора CASE

```sql
-- Пример 1: Назначить статус заказа в зависимости от общей суммы
SELECT OrderID, TotalAmount,
       CASE
           WHEN TotalAmount > 100 THEN 'High'
           WHEN TotalAmount BETWEEN 50 AND 100 THEN 'Medium'
           ELSE 'Low'
       END as OrderStatus
FROM Orders;

-- Пример 2: Назначить категории цветов в зависимости от цены
SELECT FlowerID, Name, Price,
       CASE
           WHEN Price > 3 THEN 'Premium'
           WHEN Price BETWEEN 2 AND 3 THEN 'Standard'
           ELSE 'Budget'
       END as PriceCategory
FROM Flowers;

-- Пример 3: Назначить статус клиента в зависимости от суммы всех заказов
SELECT CustomerID, FirstName, LastName,
       CASE
           WHEN (SELECT SUM(TotalAmount) FROM Orders WHERE CustomerID = c.CustomerID) > 100 THEN 'VIP'
           ELSE 'Regular'
       END as CustomerStatus
FROM Customers c;
```

## Заключение

Все запросы для создания и работы с базой данных магазина по продаже цветов были выполнены. Каждая тема была представлена отдельным блоком с соответствующими SQL-запросами.
