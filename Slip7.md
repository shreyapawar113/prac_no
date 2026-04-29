Slip7
#include <iostream>
#include <cstring>
using namespace std;

class invertdata {
public:
    int invert(int n) {
        int rev = 0;
        while (n > 0) {
            rev = rev * 10 + n % 10;
            n /= 10;
        }
        return rev;
    }

    char* invert(char *str) {
        int len = strlen(str);
        char *rev = new char[len + 1];
        for (int i = 0; i < len; i++) {
            rev[i] = str[len - i - 1];
        }
        rev[len] = '\0';
        return rev;
    }

    void invert(int *arr) {
        int n;
        cin >> n;
        for (int i = 0; i < n / 2; i++) {
            int temp = arr[i];
            arr[i] = arr[n - i - 1];
            arr[n - i - 1] = temp;
        }
        for (int i = 0; i < n; i++) {
            cout << arr[i] << " ";
        }
        cout << endl;
    }
};

int main() {
    invertdata obj;

    int num = 5438;
    cout << obj.invert(num) << endl;

    char str[] = "comp";
    char *revstr = obj.invert(str);
    cout << revstr << endl;

    int n = 4;
    int arr[4] = {5, 7, 12, 4};
    cout << n << endl;
    obj.invert(arr);

    return 0;
}
DBMS
CREATE DATABASE bankdb4;
USE bankdb4;

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
        SET MESSAGE_TEXT = 'Loan amount less than 100000 not allowed';
    END IF;
END $$

c)

CREATE PROCEDURE loanCount()
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


