slip 9
#include <iostream>
using namespace std;

class Clock
{
private:
    int h, m, s;

    void normalize()
    {
        if (s >= 60) { m += s / 60; s %= 60; }
        if (m >= 60) { h += m / 60; m %= 60; }
        if (h >= 24) { h %= 24; }

        if (s < 0)
        {
            int borrow = (abs(s) + 59) / 60;
            m -= borrow;
            s += borrow * 60;
        }
        if (m < 0)
        {
            int borrow = (abs(m) + 59) / 60;
            h -= borrow;
            m += borrow * 60;
        }
        if (h < 0)
        {
            h = (h % 24 + 24) % 24;
        }
    }

public:
    Clock()
    {
        h = 0; m = 0; s = 0;
    }

    Clock(int hh, int mm, int ss)
    {
        h = hh; m = mm; s = ss;
        normalize();
    }

    Clock operator++()
    {
        s++;
        normalize();
        return *this;
    }

    Clock operator++(int)
    {
        Clock temp = *this;
        s++;
        normalize();
        return temp;
    }

    Clock operator--()
    {
        s--;
        normalize();
        return *this;
    }

    Clock operator--(int)
    {
        Clock temp = *this;
        s--;
        normalize();
        return temp;
    }

    friend istream& operator>>(istream &in, Clock &c)
    {
        in >> c.h >> c.m >> c.s;
        c.normalize();
        return in;
    }

    friend ostream& operator<<(ostream &out, Clock &c)
    {
        out << c.h << ":" << c.m << ":" << c.s;
        return out;
    }
};

int main()
{
    Clock c1(23, 59, 58);

    cout << "Initial: " << c1 << endl;

    ++c1;
    cout << "After ++: " << c1 << endl;

    c1++;
    cout << "After postfix ++: " << c1 << endl;

    --c1;
    cout << "After --: " << c1 << endl;

    cout << "Enter time (h m s): ";
    cin >> c1;
    cout << "You entered: " << c1 << endl;

    return 0;
}

DBMS
CREATE DATABASE bankdb5;
USE bankdb5;

CREATE TABLE BRANCH (
    BID INT,
    BRNAME CHAR(30),
    BRCITY CHAR(10)
);

CREATE TABLE CUSTOMER (
    CNO INT,
    CNAME CHAR(20),
    CADDR CHAR(35),
    CITY CHAR(20)
);

CREATE TABLE LOAN_APPLICATION (
    LNO INT,
    LAMTREQUIRED DECIMAL(10,2),
    LAMTAPPROVED DECIMAL(10,2),
    L_DATE DATE
);

CREATE TABLE TERNARY (
    BID INT,
    CNO INT,
    LNO INT
);

INSERT INTO BRANCH VALUES
(1, 'Aundh', 'Pune'),
(2, 'Shivaji Nagar', 'Pune');

INSERT INTO CUSTOMER VALUES
(101, 'Amit', 'Aundh', 'Pune'),
(102, 'Ravi', 'Baner', 'Pune'),
(103, 'Suresh', 'Aundh', 'Pune');

INSERT INTO LOAN_APPLICATION VALUES
(1001, 150000, 140000, '2024-01-10'),
(1002, 90000, 80000, '2024-02-15');

INSERT INTO TERNARY VALUES
(1, 101, 1001),
(1, 103, 1002),
(2, 102, 1002);

a)

CREATE FUNCTION countCustomers(amount DECIMAL(10,2))
RETURNS INT
DETERMINISTIC
BEGIN
    DECLARE total INT;

    SELECT COUNT(DISTINCT CNO)
    INTO total
    FROM TERNARY T
    JOIN LOAN_APPLICATION L ON T.LNO = L.LNO
    WHERE L.LAMTREQUIRED > amount;

    RETURN total;
END $$

b)

CREATE TRIGGER check_loan
BEFORE INSERT ON LOAN_APPLICATION
FOR EACH ROW
BEGIN
    IF NEW.LAMTREQUIRED < 100000 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Loan less than 100000 not allowed';
    END IF;
END $$


c) 
CREATE PROCEDURE branchDetails(branch_name CHAR(30))
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE bid INT;
    DECLARE brname CHAR(30);
    DECLARE brcity CHAR(10);

    DECLARE cur CURSOR FOR
        SELECT * FROM BRANCH WHERE BRNAME = branch_name;

    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

    OPEN cur;

    read_loop: LOOP
        FETCH cur INTO bid, brname, brcity;

        IF done THEN
            LEAVE read_loop;
        END IF;

        SELECT bid, brname, brcity;
    END LOOP;

    CLOSE cur;
END $$


