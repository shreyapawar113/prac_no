slip 6
#include <iostream>
using namespace std;

class Complex
{
private:
    float real, imag;

public:
    Complex()
    {
        real = 0;
        imag = 0;
    }

    Complex(float r, float i)
    {
        real = r;
        imag = i;
    }

    Complex operator+(Complex c)
    {
        return Complex(real + c.real, imag + c.imag);
    }

    Complex operator-(Complex c)
    {
        return Complex(real - c.real, imag - c.imag);
    }

    Complex operator*(Complex c)
    {
        return Complex(
            real * c.real - imag * c.imag,
            real * c.imag + imag * c.real
        );
    }

    void display()
    {
        cout << real << " + i" << imag << endl;
    }
};

int main()
{
    Complex c1(2, 3), c2(4, 5), c3;

    cout << "Addition: ";
    c3 = c1 + c2;
    c3.display();

    cout << "Subtraction: ";
    c3 = c1 - c2;
    c3.display();

    cout << "Multiplication: ";
    c3 = c1 * c2;
    c3.display();

    return 0;
}
DBMS
CREATE DATABASE bankdb3;
USE bankdb3;

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
(102, 'Ravi', 'Baner', 'Pune');

INSERT INTO LOAN_APPLICATION VALUES
(1001, 50000, 60000, '2024-01-10'),
(1002, 70000, 65000, '2024-02-15');

INSERT INTO TERNARY VALUES
(1, 101, 1001),
(2, 102, 1002);

a)

CREATE FUNCTION maxLoan()
RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
    DECLARE maxval DECIMAL(10,2);

    SELECT MAX(LAMTAPPROVED)
    INTO maxval
    FROM LOAN_APPLICATION;

    RETURN maxval;
END $$

b)

CREATE TRIGGER check_amount
BEFORE INSERT ON LOAN_APPLICATION
FOR EACH ROW
BEGIN
    IF NEW.LAMTAPPROVED > NEW.LAMTREQUIRED THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Approved amount greater than required';
    END IF;
END $$

C)

CREATE PROCEDURE aundhCustomers()
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE cno INT;
    DECLARE cname CHAR(20);
    DECLARE caddr CHAR(35);
    DECLARE city CHAR(20);

    DECLARE cur CURSOR FOR
        SELECT C.CNO, C.CNAME, C.CADDR, C.CITY
        FROM CUSTOMER C
        JOIN TERNARY T ON C.CNO = T.CNO
        JOIN BRANCH B ON T.BID = B.BID
        WHERE B.BRNAME = 'Aundh';

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





