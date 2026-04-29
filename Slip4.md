slip 4
#include <iostream>
#include <cstring>
#include <cctype>
using namespace std;

class Key;

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

    void display()
    {
        cout << msg << endl;
    }

    friend void encrypt(Message &, Key);
};

class Key
{
private:
    char key[30];
    int len;

public:
    Key(const char *k)
    {
        strcpy(key, k);
        len = strlen(k);
    }

    int isValid()
    {
        if (len < 8 || len >= 30)
            return 0;

        int hasDigit = 0, hasUpper = 0;

        for (int i = 0; i < len; i++)
        {
            if (isdigit(key[i]))
                hasDigit = 1;
            if (isupper(key[i]))
                hasUpper = 1;
        }

        return hasDigit && hasUpper;
    }

    friend void encrypt(Message &, Key);
};

void encrypt(Message &m, Key k)
{
    if (!k.isValid())
    {
        cout << "Invalid Key" << endl;
        return;
    }

    for (int i = 0; i < m.len; i++)
    {
        m.msg[i] = m.msg[i] + (k.key[i % k.len] % 5);
    }
}

int main()
{
    Message m("HELLO WORLD");
    Key k("Abcdef12");

    encrypt(m, k);
    m.display();
    
    return 0;
}

Slip 4 DBM
CREATE DATABASE bankdb2;
USE bankdb2;

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
(1002, 250000, 220000, '2024-02-15');

INSERT INTO TERNARY VALUES
(1, 101, 1001),
(1, 103, 1002),
(2, 102, 1002);


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
    IF NEW.LAMTREQUIRED < 100000 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Amount less than 100000 not allowed';
    END IF;
END $$

c)

CREATE PROCEDURE loanCustomers()
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE countCust INT DEFAULT 0;

    DECLARE cur CURSOR FOR
        SELECT DISTINCT CNO
        FROM TERNARY T
        JOIN LOAN_APPLICATION L ON T.LNO = L.LNO
        WHERE L.LAMTREQUIRED > 200000;

    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

    OPEN cur;

    read_loop: LOOP
        FETCH cur INTO @c;

        IF done THEN
            LEAVE read_loop;
        END IF;

        SET countCust = countCust + 1;
    END LOOP;

    CLOSE cur;

    SELECT countCust;
END $$

