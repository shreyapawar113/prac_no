slip 11
#include <iostream>
#include <cstring>
using namespace std;

class Message
{
private:
    char *msg;
    int len;

public:
    Message(const char *m = "")
    {
        len = strlen(m);
        msg = new char[len + 1];
        strcpy(msg, m);
    }

    Message(const Message &m)
    {
        len = m.len;
        msg = new char[len + 1];
        strcpy(msg, m.msg);
    }

    Message operator+(Message m)
    {
        Message temp;
        temp.len = len + m.len;

        temp.msg = new char[temp.len + 1];
        strcpy(temp.msg, msg);
        strcat(temp.msg, m.msg);

        return temp;
    }

    void display()
    {
        cout << msg << endl;
    }

    ~Message()
    {
        delete[] msg;
    }
};

int main()
{
    Message m1("Hello ");
    Message m2("World");
    Message m3;

    m3 = m1 + m2;

    m3.display();

    return 0;
}

DBMS

CREATE DATABASE bankdb7;
USE bankdb7;

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
(1002, 250000, 230000, '2024-02-15');

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

CREATE TRIGGER check_amount
BEFORE INSERT ON LOAN_APPLICATION
FOR EACH ROW
BEGIN
    IF NEW.LAMTREQUIRED < 100000 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Loan amount less than 100000 not allowed';
    END IF;
END $$

c)

CREATE PROCEDURE customerDetails(cust_no INT)
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE cno INT;
    DECLARE cname CHAR(20);
    DECLARE caddr CHAR(35);
    DECLARE city CHAR(20);

    DECLARE cur CURSOR FOR
        SELECT * FROM CUSTOMER WHERE CNO = cust_no;

    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

    OPEN cur;

    read_loop: LOOP
        FETCH cur INTO cno, cname, caddr, city;

        IF done THEN
            LEAVE read_loop;
        END IF;

        SELECT cno, cname, caddr, city;
    END LOOP;

    CLOSE cur;
END $$


