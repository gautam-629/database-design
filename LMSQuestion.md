# Library Management System - Q&A

This document contains 20 real-world questions and answers based on the provided database schema for the Library Management System.

---

## 1. How to add a new book to the `Book` table?

### Query:

```sql
INSERT INTO Book (Title, Author, ISBN)
VALUES ('The Great Gatsby', 'F. Scott Fitzgerald', '9780743273565');
```

---

## 2. How to list all books written by a specific author?

### Query:

```sql
SELECT * FROM Book
WHERE Author = 'F. Scott Fitzgerald';
```

---

## 3. How to add a new copy of a book and mark it as available?

### Query:

```sql
INSERT INTO Copy (BookId, Status)
VALUES (1, 'Available');
```

---

## 4. How to find all available copies of a specific book by Title?

### Query:

```sql
SELECT c.*
FROM Copy c
JOIN Book b ON c.BookId = b.BookId
WHERE b.Title = 'The Great Gatsby' AND c.Status = 'Available';
```

---

## 5. How to add a new member to the `Member` table?

### Query:

```sql
INSERT INTO Member (Name, Email, Phone)
VALUES ('John Doe', 'john.doe@example.com', '1234567890');
```

---

## 6. How to list all members who have borrowed books?

### Query:

```sql
SELECT DISTINCT m.*
FROM Member m
JOIN Loan l ON m.MemberId = l.MemberId;
```

---

## 7. How to add a loan record for a book copy?

### Query:

```sql
INSERT INTO Loan (CopyId, MemberId, LibrarianId, LoanDate, DueDate)
VALUES (1, 1, 1, NOW(), DATE_ADD(NOW(), INTERVAL 14 DAY));
```

---

## 8. How to find overdue books?

### Query:

```sql
SELECT l.*
FROM Loan l
WHERE l.DueDate < NOW() AND l.ReturnDate IS NULL;
```

---

## 9. How to mark a book copy as returned?

### Query:

```sql
UPDATE Loan
SET ReturnDate = NOW()
WHERE LoanId = 1;

UPDATE Copy
SET Status = 'Available'
WHERE CopyId = 1;
```

---

## 10. How to list all books currently on loan?

### Query:

```sql
SELECT b.*
FROM Book b
JOIN Copy c ON b.BookId = c.BookId
JOIN Loan l ON c.CopyId = l.CopyId
WHERE l.ReturnDate IS NULL;
```

---

## 11. How to add a new librarian to the `Librarian` table?

### Query:

```sql
INSERT INTO Librarian (Name, Email)
VALUES ('Alice Smith', 'alice.smith@example.com');
```

---

## 12. How to list all loans handled by a specific librarian?

### Query:

```sql
SELECT l.*
FROM Loan l
WHERE l.LibrarianId = 1;
```

---

## 13. How to mark a book copy as damaged?

### Query:

```sql
UPDATE Copy
SET Status = 'Damaged'
WHERE CopyId = 1;
```

---

## 14. How to delete a member who has no loans?

### Query:

```sql
DELETE FROM Member
WHERE MemberId = 1 AND MemberId NOT IN (
    SELECT MemberId FROM Loan
);
```

---

## 15. How to list all copies of a specific book by ISBN?

### Query:

```sql
SELECT c.*
FROM Copy c
JOIN Book b ON c.BookId = b.BookId
WHERE b.ISBN = '9780743273565';
```

---

## 16. How to find the total number of books loaned by a specific member?

### Query:

```sql
SELECT COUNT(*) AS TotalLoans
FROM Loan
WHERE MemberId = 1;
```

---

## 17. How to find the member who has borrowed the most books?

### Query:

```sql
SELECT m.Name, COUNT(l.LoanId) AS TotalLoans
FROM Member m
JOIN Loan l ON m.MemberId = l.MemberId
GROUP BY m.MemberId
ORDER BY TotalLoans DESC
LIMIT 1;
```

---

## 18. How to find the most borrowed book?

### Query:

```sql
SELECT b.Title, COUNT(l.LoanId) AS TotalLoans
FROM Book b
JOIN Copy c ON b.BookId = c.BookId
JOIN Loan l ON c.CopyId = l.CopyId
GROUP BY b.BookId
ORDER BY TotalLoans DESC
LIMIT 1;
```

---

## 19. How to find all books that have not been borrowed?

### Query:

```sql
SELECT b.*
FROM Book b
WHERE b.BookId NOT IN (
    SELECT DISTINCT c.BookId
    FROM Copy c
    JOIN Loan l ON c.CopyId = l.CopyId
);
```

---

## 20. How to delete all damaged copies of books?

### Query:

```sql
DELETE FROM Copy
WHERE Status = 'Damaged';
```
