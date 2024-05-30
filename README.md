# Отчет по Базам Данных: Железная Дорога

## 1. Разбор и предложение схемы базы данных

### Схема базы данных

Предположим, что исходная схема базы данных выглядит следующим образом:

- Таблица `Passengers` (Пассажиры):
  - `PassengerID` (PK)
  - `Name`
  - `Surname`
  - `BirthDate`
  - `Gender`

- Таблица `Tickets` (Билеты):
  - `TicketID` (PK)
  - `PassengerID` (FK)
  - `TrainID` (FK)
  - `SeatNumber`
  - `DateOfJourney`
  - `Price`

- Таблица `Trains` (Поезда):
  - `TrainID` (PK)
  - `TrainNumber`
  - `Route`

### Предложенная схема базы данных

Предложенная схема будет расширена для включения информации о станциях и маршрутах:

- Таблица `Passengers` (Пассажиры):
  - `PassengerID` (PK)
  - `Name`
  - `Surname`
  - `BirthDate`
  - `Gender`
  - `PhoneNumber`
  - `Email`

- Таблица `Tickets` (Билеты):
  - `TicketID` (PK)
  - `PassengerID` (FK)
  - `TrainID` (FK)
  - `SeatNumber`
  - `DateOfJourney`
  - `Price`
  - `DepartureStationID` (FK)
  - `ArrivalStationID` (FK)

- Таблица `Trains` (Поезда):
  - `TrainID` (PK)
  - `TrainNumber`
  - `Route`

- Таблица `Stations` (Станции):
  - `StationID` (PK)
  - `StationName`
  - `City`
  - `State`

- Таблица `Routes` (Маршруты):
  - `RouteID` (PK)
  - `TrainID` (FK)
  - `StationID` (FK)
  - `ArrivalTime`
  - `DepartureTime`

## 2. Создание таблиц по предложенной схеме БД

```sql
CREATE TABLE Passengers (
    PassengerID INT PRIMARY KEY,
    Name VARCHAR(50),
    Surname VARCHAR(50),
    BirthDate DATE,
    Gender CHAR(1),
    PhoneNumber VARCHAR(15),
    Email VARCHAR(100)
);

CREATE TABLE Tickets (
    TicketID INT PRIMARY KEY,
    PassengerID INT,
    TrainID INT,
    SeatNumber VARCHAR(10),
    DateOfJourney DATE,
    Price DECIMAL(10, 2),
    DepartureStationID INT,
    ArrivalStationID INT,
    FOREIGN KEY (PassengerID) REFERENCES Passengers(PassengerID),
    FOREIGN KEY (TrainID) REFERENCES Trains(TrainID),
    FOREIGN KEY (DepartureStationID) REFERENCES Stations(StationID),
    FOREIGN KEY (ArrivalStationID) REFERENCES Stations(StationID)
);

CREATE TABLE Trains (
    TrainID INT PRIMARY KEY,
    TrainNumber VARCHAR(10),
    Route VARCHAR(255)
);

CREATE TABLE Stations (
    StationID INT PRIMARY KEY,
    StationName VARCHAR(100),
    City VARCHAR(100),
    State VARCHAR(100)
);

CREATE TABLE Routes (
    RouteID INT PRIMARY KEY,
    TrainID INT,
    StationID INT,
    ArrivalTime TIME,
    DepartureTime TIME,
    FOREIGN KEY (TrainID) REFERENCES Trains(TrainID),
    FOREIGN KEY (StationID) REFERENCES Stations(StationID)
);
```

## 3. Введение исходных данных

```sql
-- Таблица Passengers
INSERT INTO Passengers VALUES (1, 'John', 'Doe', '1985-02-15', 'M', '123-456-7890', 'john.doe@example.com');
INSERT INTO Passengers VALUES (2, 'Jane', 'Smith', '1990-05-20', 'F', '234-567-8901', 'jane.smith@example.com');
-- Добавьте еще 15-18 записей

-- Таблица Trains
INSERT INTO Trains VALUES (1, 'A123', 'City A - City B');
INSERT INTO Trains VALUES (2, 'B456', 'City B - City C');
-- Добавьте еще 15-18 записей

-- Таблица Stations
INSERT INTO Stations VALUES (1, 'Central Station', 'City A', 'State A');
INSERT INTO Stations VALUES (2, 'West Station', 'City B', 'State B');
-- Добавьте еще 15-18 записей

-- Таблица Tickets
INSERT INTO Tickets VALUES (1, 1, 1, 'A1', '2023-06-01', 100.00, 1, 2);
INSERT INTO Tickets VALUES (2, 2, 2, 'B1', '2023-06-02', 120.00, 2, 1);
-- Добавьте еще 15-18 записей

-- Таблица Routes
INSERT INTO Routes VALUES (1, 1, 1, '08:00', '08:15');
INSERT INTO Routes VALUES (2, 1, 2, '09:00', '09:15');
-- Добавьте еще 15-18 записей
```

## 4. Выполнение заданий

### Индексы

Создание индексов для ускорения запросов.

```sql
CREATE INDEX idx_passenger_name ON Passengers(Name);
CREATE INDEX idx_ticket_date ON Tickets(DateOfJourney);
```

### Ограничения в базах данных

Добавление ограничений на таблицы.

```sql
ALTER TABLE Passengers
ADD CONSTRAINT chk_gender CHECK (Gender IN ('M', 'F'));

ALTER TABLE Tickets
ADD CONSTRAINT chk_price CHECK (Price > 0);
```

### Представления в SQL

Создание представления для получения информации о билетах с пассажирами.

```sql
CREATE VIEW TicketInfo AS
SELECT 
    t.TicketID, 
    p.Name, 
    p.Surname, 
    t.DateOfJourney, 
    t.SeatNumber, 
    t.Price
FROM 
    Tickets t
JOIN 
    Passengers p ON t.PassengerID = p.PassengerID;
```

### Оконные функции

Примеры трех оконных функций.

```sql
-- Оконная функция ROW_NUMBER
SELECT 
    PassengerID, 
    Name, 
    ROW_NUMBER() OVER (ORDER BY Name) as RowNum
FROM 
    Passengers;

-- Оконная функция RANK
SELECT 
    PassengerID, 
    Name, 
    RANK() OVER (ORDER BY Name) as Rank
FROM 
    Passengers;

-- Оконная функция SUM
SELECT 
    TrainID, 
    DateOfJourney, 
    SUM(Price) OVER (PARTITION BY TrainID ORDER BY DateOfJourney) as RunningTotal
FROM 
    Tickets;
```

### Временные таблицы (виртуальные таблицы)

Пример создания временной таблицы.

```sql
CREATE TEMPORARY TABLE TempPassengers AS
SELECT 
    * 
FROM 
    Passengers
WHERE 
    Gender = 'M';
```

## Выполнение манипуляций с таблицами

### Фильтрация данных в SQL: WHERE

```sql
SELECT * FROM Passengers WHERE Gender = 'F';
```

### Сортировка в SQL: ORDER BY

```sql
SELECT * FROM Passengers ORDER BY BirthDate DESC;
```

### Вставка и изменение данных в SQL

```sql
-- Вставка данных
INSERT INTO Passengers (PassengerID, Name, Surname, BirthDate, Gender, PhoneNumber, Email) 
VALUES (3, 'Alice', 'Johnson', '1988-03-15', 'F', '345-678-9012', 'alice.johnson@example.com');

-- Обновление данных
UPDATE Passengers 
SET Email = 'new.email@example.com' 
WHERE PassengerID = 1;
```

### Группировки и фильтрация в SQL: HAVING

```sql
SELECT Gender, COUNT(*) as NumberOfPassengers
FROM Passengers
GROUP BY Gender
HAVING COUNT(*) > 1;
```

### Запрос данных из нескольких таблиц: JOIN

```sql
SELECT 
    p.Name, 
    p.Surname, 
    t.SeatNumber, 
    t.DateOfJourney, 
    s.StationName as DepartureStation
FROM 
    Tickets t
JOIN 
    Passengers p ON t.PassengerID = p.PassengerID
JOIN 
    Stations s ON t.DepartureStationID = s.StationID;
```

### Типы соединений в SQL

```sql
-- INNER JOIN
SELECT * FROM Tickets t INNER JOIN Passengers p ON t.PassengerID = p.PassengerID;

-- LEFT JOIN
SELECT * FROM Passengers p LEFT JOIN Tickets t ON p.PassengerID = t.PassengerID;

-- RIGHT JOIN
SELECT * FROM Tickets t RIGHT JOIN Passengers p ON t.PassengerID = p.PassengerID;
```

### Подзапросы

```sql
SELECT * FROM Passengers 
WHERE PassengerID IN (SELECT PassengerID FROM Tickets WHERE Price > 100);
```

### Транзакции

```sql
BEGIN TRANSACTION;

UPDATE Passengers SET PhoneNumber = '987-654-3210' WHERE PassengerID = 2;

IF @@ERROR = 0
BEGIN
    COMMIT TRANSACTION;
END
ELSE
BEGIN
    ROLLBACK TRANSACTION;
END;
```

### Использование оператора CASE

```sql
SELECT 
    PassengerID, 


    Name, 
    Gender, 
    CASE 
        WHEN Gender = 'M' THEN 'Male'
        WHEN Gender = 'F' THEN 'Female'
        ELSE 'Unknown'
    END as GenderDescription
FROM 
    Passengers;
```

## Заключение

Все запросы были выполнены на основе созданной базы данных. В отчете представлены все необходимые данные и запросы, выполненные в соответствии с заданием.
