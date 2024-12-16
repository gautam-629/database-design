# Database Design Case Studies: Comprehensive Guide

## Data Insertion Examples

### 1. Library Management System - Sample Insertions

```sql
-- Insert Books
INSERT INTO Book (Title, Author, ISBN) VALUES
('To Kill a Mockingbird', 'Harper Lee', '9780446310789'),
('1984', 'George Orwell', '9780451524935');

-- Insert Book Copies
INSERT INTO Copy (BookId, Status) VALUES
(1, 'Available'),
(1, 'Available'),
(2, 'Available');

-- Insert Members
INSERT INTO Member (Name, Email, Phone) VALUES
('John Doe', 'john.doe@email.com', '555-1234'),
('Jane Smith', 'jane.smith@email.com', '555-5678');

-- Insert Librarians
INSERT INTO Librarian (Name, Email) VALUES
('Library Admin', 'admin@library.org');

-- Insert Loan
INSERT INTO Loan (CopyId, MemberId, LibrarianId, LoanDate, DueDate) VALUES
(1, 1, 1, NOW(), DATE_ADD(NOW(), INTERVAL 14 DAY));
```

### 2. Hospital Management System - Sample Insertions

```sql
-- Insert Doctors
INSERT INTO Doctor (Name, Specialization, Phone) VALUES
('Dr. Emily Chen', 'Pediatrics', '555-KIDS'),
('Dr. Michael Rodriguez', 'Cardiology', '555-HEART');

-- Insert Patients
INSERT INTO Patient (Name, DOB, Address, Phone) VALUES
('Alice Johnson', '1985-03-15', '123 Maple St, Cityville', '555-9876'),
('Bob Williams', '1970-11-22', '456 Oak Rd, Townsburg', '555-5432');

-- Insert Appointments
INSERT INTO Appointment (DoctorId, PatientId, Date, Time, Reason) VALUES
(1, 1, '2024-02-15', '10:00:00', 'Annual checkup'),
(2, 2, '2024-02-16', '14:30:00', 'Cardiac screening');

-- Insert Medical Records
INSERT INTO MedicalRecord (PatientId, Details, Date) VALUES
(1, 'Routine pediatric checkup, all vitals normal', '2024-02-15'),
(2, 'Initial cardiac risk assessment', '2024-02-16');

-- Insert Bills
INSERT INTO Bill (PatientId, TotalAmount, Date) VALUES
(1, 250.00, '2024-02-15'),
(2, 500.00, '2024-02-16');
```

### 3. Online Learning Platform - Sample Insertions

```sql
-- Insert Users
INSERT INTO User (Name, Email, Role) VALUES
('Emma Thompson', 'emma@learn.com', 'Instructor'),
('Alex Rivera', 'alex@learn.com', 'Student'),
('Sarah Kim', 'sarah@learn.com', 'Admin');

-- Insert Courses
INSERT INTO Course (Title, Description, InstructorId) VALUES
('Python Programming', 'Complete Python course for beginners', 1),
('Data Science Fundamentals', 'Introduction to data science concepts', 1);

-- Insert Enrollments
INSERT INTO Enrollment (UserId, CourseId, Progress, EnrollDate) VALUES
(2, 1, 25.50, '2024-01-15'),
(2, 2, 10.75, '2024-02-01');

-- Insert Feedback
INSERT INTO Feedback (CourseId, UserId, Rating, Comment, Date) VALUES
(1, 2, 4, 'Great course, very informative!', NOW()),
(2, 2, 5, 'Excellent introduction to data science', NOW());
```

### 4. E-Commerce Marketplace - Sample Insertions

```sql
-- Insert Users
INSERT INTO User (Name, Email, Role) VALUES
('Emily Wong', 'emily@shop.com', 'Seller'),
('David Kumar', 'david@shop.com', 'Buyer'),
('Admin User', 'admin@marketplace.com', 'Admin');

-- Insert Products
INSERT INTO Product (Name, Description, Price, SellerId) VALUES
('Wireless Headphones', 'Noise-cancelling Bluetooth headphones', 129.99, 1),
('Smartwatch', 'Advanced fitness tracking smartwatch', 199.99, 1);

-- Insert Orders
INSERT INTO `Order` (BuyerId, OrderDate, TotalAmount) VALUES
(2, NOW(), 329.98);

-- Insert Order Items
INSERT INTO OrderItem (OrderId, ProductId, Quantity, Subtotal) VALUES
(1, 1, 1, 129.99),
(1, 2, 1, 199.99);

-- Insert Reviews
INSERT INTO Review (ProductId, UserId, Rating, Comment) VALUES
(1, 2, 5, 'Amazing sound quality!'),
(2, 2, 4, 'Great watch, battery could be better');
```

## Extended Q&A and Advanced Scenarios

### 1. Library Management System

**Q1: How to implement a reservation system?**
**Approach:**

- Add a `Reservation` table
- Track pending reservations before they become active loans
- Implement logic to convert reservations to loans when copies become available

```sql
CREATE TABLE Reservation (
    ReservationId INT PRIMARY KEY AUTO_INCREMENT,
    MemberId INT NOT NULL,
    BookId INT NOT NULL,
    ReservationDate DATETIME NOT NULL,
    Status ENUM('Pending', 'Active', 'Cancelled', 'Completed'),
    FOREIGN KEY (MemberId) REFERENCES Member(MemberId),
    FOREIGN KEY (BookId) REFERENCES Book(BookId)
);
```

**Q2: How to generate late fee calculations?**
**Approach:**

- Create a function to calculate late fees
- Add a `LateFee` table to track penalties

```sql
DELIMITER //
CREATE FUNCTION CalculateLateFee(loan_id INT)
RETURNS DECIMAL(10,2) DETERMINISTIC
BEGIN
    DECLARE days_overdue INT;
    DECLARE fee DECIMAL(10,2);

    SELECT DATEDIFF(CURRENT_DATE, DueDate) INTO days_overdue
    FROM Loan
    WHERE LoanId = loan_id;

    IF days_overdue > 0 THEN
        SET fee = days_overdue * 0.50;  -- $0.50 per day late
    ELSE
        SET fee = 0;
    END IF;

    RETURN fee;
END //
DELIMITER ;
```

### 2. Hospital Management System

**Q1: Implement a Patient Referral Tracking System**

```sql
CREATE TABLE Referral (
    ReferralId INT PRIMARY KEY AUTO_INCREMENT,
    SourceDoctorId INT NOT NULL,
    TargetDoctorId INT NOT NULL,
    PatientId INT NOT NULL,
    ReferralDate DATETIME NOT NULL,
    Status ENUM('Pending', 'Accepted', 'Rejected'),
    Notes TEXT,
    FOREIGN KEY (SourceDoctorId) REFERENCES Doctor(DoctorId),
    FOREIGN KEY (TargetDoctorId) REFERENCES Doctor(DoctorId),
    FOREIGN KEY (PatientId) REFERENCES Patient(PatientId)
);
```

**Q2: Create a Complex Billing Analysis Query**

```sql
SELECT
    YEAR(b.Date) as BillingYear,
    MONTH(b.Date) as BillingMonth,
    SUM(b.TotalAmount) as MonthlyRevenue,
    COUNT(DISTINCT b.PatientId) as UniquePatients,
    AVG(b.TotalAmount) as AverageBill
FROM Bill b
GROUP BY BillingYear, BillingMonth
ORDER BY BillingYear, BillingMonth;
```

### 3. Online Learning Platform

**Q1: Implement Certification Tracking**

```sql
CREATE TABLE Certification (
    CertificationId INT PRIMARY KEY AUTO_INCREMENT,
    UserId INT NOT NULL,
    CourseId INT NOT NULL,
    IssueDate DATE NOT NULL,
    ExpirationDate DATE,
    CertificationCode VARCHAR(50) UNIQUE,
    FOREIGN KEY (UserId) REFERENCES User(UserId),
    FOREIGN KEY (CourseId) REFERENCES Course(CourseId)
);
```

**Q2: Advanced Course Recommendation System**

```sql
SELECT
    c.CourseId,
    c.Title,
    AVG(f.Rating) as AverageRating,
    COUNT(DISTINCT e.UserId) as EnrollmentCount,
    (
        SELECT COUNT(*)
        FROM Enrollment e2
        WHERE e2.CourseId = c.CourseId AND e2.Progress >= 90
    ) as CompletionCount
FROM Course c
LEFT JOIN Feedback f ON c.CourseId = f.CourseId
LEFT JOIN Enrollment e ON c.CourseId = e.CourseId
GROUP BY c.CourseId
ORDER BY AverageRating DESC, EnrollmentCount DESC;
```

### 4. E-Commerce Marketplace

**Q1: Implement Seller Performance Metrics**

```sql
CREATE TABLE SellerPerformance (
    PerformanceId INT PRIMARY KEY AUTO_INCREMENT,
    SellerId INT NOT NULL,
    Period DATE NOT NULL,
    TotalSales DECIMAL(10,2),
    NumberOfSales INT,
    AverageRating DECIMAL(3,2),
    FOREIGN KEY (SellerId) REFERENCES User(UserId)
);
```

**Q2: Advanced Sales and Revenue Analysis**

```sql
SELECT
    p.SellerId,
    u.Name as SellerName,
    YEAR(o.OrderDate) as SalesYear,
    MONTH(o.OrderDate) as SalesMonth,
    SUM(oi.Subtotal) as MonthlyRevenue,
    COUNT(DISTINCT o.OrderId) as NumberOfOrders,
    AVG(r.Rating) as AverageProductRating
FROM Product p
JOIN `Order` o ON p.ProductId = oi.ProductId
JOIN OrderItem oi ON o.OrderId = oi.OrderId
JOIN User u ON p.SellerId = u.UserId
LEFT JOIN Review r ON p.ProductId = r.ProductId
GROUP BY p.SellerId, SalesYear, SalesMonth
ORDER BY MonthlyRevenue DESC;
```

## Best Practices and Advanced Considerations

1. **Data Integrity**: Always use foreign key constraints
2. **Performance**: Create appropriate indexes
3. **Security**: Implement role-based access control
4. **Scalability**: Design flexible schemas
5. **Maintenance**: Regular database optimization and backups

## Conclusion

These extended case studies demonstrate the complexity and depth of real-world database design, showing how databases can solve intricate business problems across various domains.
