slip13
#include <iostream>
using namespace std;

class message {
public:
    void display() {
        cout << "Hi" << endl;
    }

    void display(const char *str) {
        cout << str << endl;
    }

    void display(const char *str, int n) {
        for (int i = 0; i < n; i++)
            cout << str << endl;
    }
};

class tableinterface {
public:
    void Tables() {
        for (int i = 1; i <= 10; i++)
            cout << i << endl;
    }

    void Tables(int n) {
        for (int i = 1; i <= 10; i++)
            cout << n << " * " << i << " = " << n * i << endl;
    }

    void Tables(int n, int start) {
        for (int i = start; i <= 10; i++)
            cout << n << " * " << i << " = " << n * i << endl;
    }

    void Tables(int n, int start, int end) {
        for (int i = start; i <= end; i++)
            cout << n << " * " << i << " = " << n * i << endl;
    }
};

int main() {
    message m;
    m.display();
    m.display("Namaste");
    m.display("hello", 10);

    tableinterface t;
    t.Tables();
    t.Tables(2);
    t.Tables(2, 3);
    t.Tables(2, 3, 6);

    return 0;
}

DBMS

CREATE DATABASE bankdb8;
USE bankdb8;

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
(1, 'Karvenagar', 'Pune'),
(2, 'Aundh', 'Pune');

INSERT INTO CUSTOMER VALUES
(7, 'Amit', 'Karvenagar', 'Pune'),
(8, 'Ravi', 'Baner', 'Pune');

INSERT INTO LOAN_APPLICATION VALUES
(1001, 150000, 140000, '2024-01-10'),
(1002, 200000, 190000, '2024-02-15');

INSERT INTO TERNARY VALUES
(1, 7, 1001),
(2, 8, 1002);

a)

CREATE FUNCTION totalCustomers(branch_name CHAR(30))
RETURNS INT
DETERMINISTIC
BEGIN
    DECLARE total INT;

    SELECT COUNT(DISTINCT C.CNO)
    INTO total
    FROM CUSTOMER C
    JOIN TERNARY T ON C.CNO = T.CNO
    JOIN BRANCH B ON T.BID = B.BID
    WHERE B.BRNAME = branch_name;

    RETURN total;
END $$

b)

CREATE TRIGGER check_amount
BEFORE INSERT ON LOAN_APPLICATION
FOR EACH ROW
BEGIN
    IF NEW.LAMTAPPROVED < NEW.LAMTREQUIRED THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Approved amount less than required';
    END IF;
END $$

c)

CREATE PROCEDURE customer7()
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE cno INT;
    DECLARE cname CHAR(20);
    DECLARE caddr CHAR(35);
    DECLARE city CHAR(20);

    DECLARE cur CURSOR FOR
        SELECT * FROM CUSTOMER WHERE CNO = 7;

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

D
