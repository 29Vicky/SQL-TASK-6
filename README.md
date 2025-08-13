# SQL-TASK-6
-- Create Authors table
CREATE TABLE Authors (
    AuthorID INT PRIMARY KEY,
    Name VARCHAR(100),
    Country VARCHAR(50)
);

-- Create Books table
CREATE TABLE Books (
    BookID INT PRIMARY KEY,
    Title VARCHAR(100),
    Genre VARCHAR(50),
    AuthorID INT,
    PublishedYear INT,
    FOREIGN KEY (AuthorID) REFERENCES Authors(AuthorID)
);

-- Create Members table
CREATE TABLE Members (
    MemberID INT PRIMARY KEY,
    Name VARCHAR(100),
    Email VARCHAR(100)
);

-- Create Borrow table
CREATE TABLE Borrow (
    BorrowID INT PRIMARY KEY,
    BookID INT,
    MemberID INT,
    BorrowDate DATE,
    ReturnDate DATE,
    FOREIGN KEY (BookID) REFERENCES Books(BookID),
    FOREIGN KEY (MemberID) REFERENCES Members(MemberID)
);

-- Insert sample data
INSERT INTO Authors VALUES
(1, 'J.K. Rowling', 'UK'),
(2, 'Chetan Bhagat', 'India'),
(3, 'R.K. Narayan', 'India'),
(4, 'George R.R. Martin', 'USA');

INSERT INTO Books VALUES
(1, 'Harry Potter', 'Fantasy', 1, 1997),
(2, 'Five Point Someone', 'Fiction', 2, 2004),
(3, 'Malgudi Days', 'Short Stories', 3, 1943),
(4, 'A Game of Thrones', 'Fantasy', 4, 1996),
(5, 'Half Girlfriend', 'Romance', 2, 2014);

INSERT INTO Members VALUES
(1, 'Alice', 'alice@example.com'),
(2, 'Bob', 'bob@example.com'),
(3, 'Charlie', 'charlie@example.com');

INSERT INTO Borrow VALUES
(1, 1, 1, '2024-01-10', '2024-01-20'),
(2, 2, 1, '2024-02-15', NULL),
(3, 3, 2, '2024-02-20', '2024-02-28'),
(4, 1, 2, '2024-03-01', NULL),
(5, 5, 1, '2024-03-10', NULL);

-- =====================
-- Subquery Examples
-- =====================

-- 1. Books written by authors from India
SELECT * FROM Books
WHERE AuthorID IN (
    SELECT AuthorID FROM Authors
    WHERE Country = 'India'
);

-- 2. Members who have borrowed at least one book
SELECT * FROM Members
WHERE MemberID IN (
    SELECT DISTINCT MemberID FROM Borrow
);

-- 3. Books that have never been borrowed
SELECT * FROM Books
WHERE BookID NOT IN (
    SELECT DISTINCT BookID FROM Borrow
);

-- 4. Most recently borrowed book
SELECT * FROM Books
WHERE BookID = (
    SELECT BookID FROM Borrow
    ORDER BY BorrowDate DESC
    LIMIT 1
);

-- 5. Members who borrowed more than 1 book
SELECT M.Name, M.Email
FROM Members M
WHERE (
    SELECT COUNT(*) FROM Borrow BR
    WHERE BR.MemberID = M.MemberID
) > 1;


-- 6. Authors who have written in more than one genre
SELECT Name FROM Authors
WHERE AuthorID IN (
    SELECT AuthorID
    FROM Books
    GROUP BY AuthorID
    HAVING COUNT(DISTINCT Genre) > 1
);

-- 7. Members and total books borrowed
SELECT M.Name,
       (SELECT COUNT(*) FROM Borrow BR WHERE BR.MemberID = M.MemberID) AS TotalBorrowed
FROM Members M;

-- 8. Oldest published book
SELECT * FROM Books
WHERE PublishedYear = (
    SELECT MIN(PublishedYear) FROM Books
);

-- 9. Members who borrowed books by 'J.K. Rowling'
SELECT DISTINCT M.Name
FROM Members M
WHERE M.MemberID IN (
    SELECT BR.MemberID
    FROM Borrow BR
    JOIN Books B ON BR.BookID = B.BookID
    WHERE B.AuthorID = (
        SELECT AuthorID FROM Authors WHERE Name = 'J.K. Rowling'
    )
);

-- 10. Books published after the average published year
SELECT * FROM Books
WHERE PublishedYear > (
    SELECT AVG(PublishedYear) FROM Books
);
