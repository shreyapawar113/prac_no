slip10
#include <iostream>
#include <cstring>
using namespace std;

class account {
    int accno;
    char type[20];
    long amount;
    char name[50];
    static int counter;

public:
    account() {
        accno = 820000 + counter++;
        amount = 0;
        strcpy(type, "saving");
        strcpy(name, "");
    }

    void setType(const char *t) {
        strcpy(type, t);
    }

    void setAmount(long a) {
        amount = a;
    }

    void setName(const char *n) {
        strcpy(name, n);
    }

    int getAccNo() { return accno; }
    char* getType() { return type; }
    long getAmount() { return amount; }
    char* getName() { return name; }

    void show() {
        cout << accno << endl;
        cout << name << endl;
        cout << type << endl;
        cout << amount << endl;
    }
};

int account::counter = 1;

int main() {
    account a[5];
    char name[50], type[20];
    long amt;

    for (int i = 0; i < 5; i++) {
        cin >> name;
        cin >> type;
        cin >> amt;

        a[i].setName(name);
        a[i].setType(type);
        a[i].setAmount(amt);
    }

    for (int i = 0; i < 5; i++) {
        a[i].show();
    }

    return 0;
}
DBMS

CREATE DATABASE bankdb6;
USE bankdb6;

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
(123, 'Amit', 'Aundh', 'Pune'),
(102, 'Ravi', 'Baner', 'Pune');

INSERT INTO LOAN_APPLICATION VALUES
(1001, 50000, 40000, '2024-01-10'),
(1002, 70000, 60000, '2024-02-15');

INSERT INTO TERNARY VALUES
(1, 123, 1001),
(2, 102, 1002);

a)

CREATE FUNCTION minLoan()
RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
    DECLARE minval DECIMAL(10,2);

    SELECT MIN(LAMTAPPROVED)
    INTO minval
    FROM LOAN_APPLICATION;

    RETURN minval;
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

CREATE PROCEDURE customer123()
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE cno INT;
    DECLARE cname CHAR(20);
    DECLARE caddr CHAR(35);
    DECLARE city CHAR(20);

    DECLARE cur CURSOR FOR
        SELECT * FROM CUSTOMER WHERE CNO = 123;

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

