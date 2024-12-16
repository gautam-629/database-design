# Database Design Case Studies

This document outlines various real-world database design case studies, detailing their use cases, requirements, entity-relationship diagrams (ERD), logical models, and advanced features. Each case study demonstrates how specific systems are modeled and optimized for their respective applications.

---

## **1. Library Management System**

### **Use Case:**

- Manage books, members, loans, and librarians.
- Track book availability, issue/return dates, and overdue items.
- Generate reports on overdue books and inventory.

### **Entities:**

- **Book:** Stores details about books.
- **Copy:** Tracks individual book copies and their status.
- **Member:** Maintains user data.
- **Loan:** Tracks book borrowing records.
- **Librarian:** Handles library administration tasks.

### **Logical Model:**

```sql
CREATE TABLE Book (
    BookId INT PRIMARY KEY AUTO_INCREMENT,
    Title VARCHAR(255) NOT NULL,
    Author VARCHAR(255),
    ISBN VARCHAR(13) UNIQUE
);

CREATE TABLE Copy (
    CopyId INT PRIMARY KEY AUTO_INCREMENT,
    BookId INT NOT NULL,
    Status ENUM('Available', 'Loaned', 'Damaged') DEFAULT 'Available',
    FOREIGN KEY (BookId) REFERENCES Book(BookId)
);

CREATE TABLE Member (
    MemberId INT PRIMARY KEY AUTO_INCREMENT,
    Name VARCHAR(255) NOT NULL,
    Email VARCHAR(255) UNIQUE,
    Phone VARCHAR(15)
);

CREATE TABLE Librarian (
    LibrarianId INT PRIMARY KEY AUTO_INCREMENT,
    Name VARCHAR(255) NOT NULL,
    Email VARCHAR(255) UNIQUE
);

CREATE TABLE Loan (
    LoanId INT PRIMARY KEY AUTO_INCREMENT,
    CopyId INT NOT NULL,
    MemberId INT NOT NULL,
    LibrarianId INT,
    LoanDate DATETIME NOT NULL,
    DueDate DATETIME NOT NULL,
    ReturnDate DATETIME,
    FOREIGN KEY (CopyId) REFERENCES Copy(CopyId),
    FOREIGN KEY (MemberId) REFERENCES Member(MemberId),
    FOREIGN KEY (LibrarianId) REFERENCES Librarian(LibrarianId)
);
```

### **Advanced Features:**

- Triggers to update book availability status upon issue or return.
- Indexes on `LoanDate` and `MemberId` for performance.

---

## **2. Hospital Management System**

### **Use Case:**

- Manage doctors, patients, appointments, medical records, and billing.
- Track patient history and assigned doctors.
- Support reporting for appointments and billing.

### **Entities:**

- **Doctor:** Tracks doctor information.
- **Patient:** Maintains patient details.
- **Appointment:** Schedules consultations.
- **MedicalRecord:** Stores patient treatment details.
- **Bill:** Handles patient billing.

### **Logical Model:**

```sql
CREATE TABLE Doctor (
    DoctorId INT PRIMARY KEY AUTO_INCREMENT,
    Name VARCHAR(255) NOT NULL,
    Specialization VARCHAR(100),
    Phone VARCHAR(15)
);

CREATE TABLE Patient (
    PatientId INT PRIMARY KEY AUTO_INCREMENT,
    Name VARCHAR(255) NOT NULL,
    DOB DATE NOT NULL,
    Address TEXT,
    Phone VARCHAR(15)
);

CREATE TABLE Appointment (
    AppointmentId INT PRIMARY KEY AUTO_INCREMENT,
    DoctorId INT NOT NULL,
    PatientId INT NOT NULL,
    Date DATE NOT NULL,
    Time TIME NOT NULL,
    Reason TEXT,
    FOREIGN KEY (DoctorId) REFERENCES Doctor(DoctorId),
    FOREIGN KEY (PatientId) REFERENCES Patient(PatientId)
);

CREATE TABLE MedicalRecord (
    RecordId INT PRIMARY KEY AUTO_INCREMENT,
    PatientId INT NOT NULL,
    Details TEXT,
    Date DATE NOT NULL,
    FOREIGN KEY (PatientId) REFERENCES Patient(PatientId)
);

CREATE TABLE Bill (
    BillId INT PRIMARY KEY AUTO_INCREMENT,
    PatientId INT NOT NULL,
    TotalAmount DECIMAL(10, 2) NOT NULL,
    Date DATE NOT NULL,
    FOREIGN KEY (PatientId) REFERENCES Patient(PatientId)
);
```

### **Advanced Features:**

- Constraints to prevent appointment scheduling conflicts for doctors.
- Procedures to generate patient-specific billing reports.

---

## **3. Online Learning Platform**

### **Use Case:**

- Manage users, courses, enrollments, and feedback.
- Track course progress for students.
- Provide ratings and feedback for courses.

### **Entities:**

- **User:** Represents students, instructors, and admins.
- **Course:** Stores course content.
- **Enrollment:** Tracks student participation.
- **Feedback:** Captures course reviews.

### **Logical Model:**

```sql
CREATE TABLE User (
    UserId INT PRIMARY KEY AUTO_INCREMENT,
    Name VARCHAR(255) NOT NULL,
    Email VARCHAR(255) UNIQUE NOT NULL,
    Role ENUM('Student', 'Instructor', 'Admin') DEFAULT 'Student'
);

CREATE TABLE Course (
    CourseId INT PRIMARY KEY AUTO_INCREMENT,
    Title VARCHAR(255) NOT NULL,
    Description TEXT,
    InstructorId INT,
    FOREIGN KEY (InstructorId) REFERENCES User(UserId)
);

CREATE TABLE Enrollment (
    EnrollmentId INT PRIMARY KEY AUTO_INCREMENT,
    UserId INT NOT NULL,
    CourseId INT NOT NULL,
    Progress DECIMAL(5, 2) DEFAULT 0.00,
    EnrollDate DATE NOT NULL,
    FOREIGN KEY (UserId) REFERENCES User(UserId),
    FOREIGN KEY (CourseId) REFERENCES Course(CourseId)
);

CREATE TABLE Feedback (
    FeedbackId INT PRIMARY KEY AUTO_INCREMENT,
    CourseId INT NOT NULL,
    UserId INT NOT NULL,
    Rating INT CHECK (Rating BETWEEN 1 AND 5),
    Comment TEXT,
    Date DATETIME NOT NULL,
    FOREIGN KEY (CourseId) REFERENCES Course(CourseId),
    FOREIGN KEY (UserId) REFERENCES User(UserId)
);
```

### **Advanced Features:**

- Triggers to notify instructors of new enrollments.
- Reports for course completion rates and feedback.

---

## **4. E-Commerce Marketplace**

### **Use Case:**

- Manage sellers, buyers, products, orders, and reviews.
- Track payments and shipping statuses.
- Provide analytics for sales and feedback.

### **Entities:**

- **User:** Stores seller, buyer, and admin details.
- **Product:** Tracks product listings.
- **Order:** Manages customer orders.
- **OrderItem:** Links products to orders.
- **Review:** Captures product reviews.

### **Logical Model:**

```sql
CREATE TABLE User (
    UserId INT PRIMARY KEY AUTO_INCREMENT,
    Name VARCHAR(255) NOT NULL,
    Email VARCHAR(255) UNIQUE NOT NULL,
    Role ENUM('Buyer', 'Seller', 'Admin') DEFAULT 'Buyer'
);

CREATE TABLE Product (
    ProductId INT PRIMARY KEY AUTO_INCREMENT,
    Name VARCHAR(255) NOT NULL,
    Description TEXT,
    Price DECIMAL(10, 2) NOT NULL,
    SellerId INT NOT NULL,
    FOREIGN KEY (SellerId) REFERENCES User(UserId)
);

CREATE TABLE Order (
    OrderId INT PRIMARY KEY AUTO_INCREMENT,
    BuyerId INT NOT NULL,
    OrderDate DATETIME NOT NULL,
    TotalAmount DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (BuyerId) REFERENCES User(UserId)
);

CREATE TABLE OrderItem (
    OrderItemId INT PRIMARY KEY AUTO_INCREMENT,
    OrderId INT NOT NULL,
    ProductId INT NOT NULL,
    Quantity INT NOT NULL,
    Subtotal DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (OrderId) REFERENCES Order(OrderId),
    FOREIGN KEY (ProductId) REFERENCES Product(ProductId)
);

CREATE TABLE Review (
    ReviewId INT PRIMARY KEY AUTO_INCREMENT,
    ProductId INT NOT NULL,
    UserId INT NOT NULL,
    Rating INT CHECK (Rating BETWEEN 1 AND 5),
    Comment TEXT,
    FOREIGN KEY (ProductId) REFERENCES Product(ProductId),
    FOREIGN KEY (UserId) REFERENCES User(UserId)
);
```

### **Advanced Features:**

- Stored procedures for monthly sales reports.
- Optimized indexing for fast product searches.

---

This document serves as a reference for designing databases for various applications. Use the models and practices outlined here to build efficient, scalable systems.
