Slip8
#include <iostream>
#include <cstring>
using namespace std;

class Book {
    char *title;
    int authors;
    char *isbn;
    float price;
    int copies;

public:
    Book() {
        title = new char[1];
        title[0] = '\0';
        isbn = new char[1];
        isbn[0] = '\0';
        authors = 1;
        copies = 1;
        price = 0;
    }

    Book(const char *t, int a, const char *i, float p, int c) {
        authors = a;
        copies = c;
        price = p;

        if (validISBN(i)) {
            isbn = new char[strlen(i) + 1];
            strcpy(isbn, i);
        } else {
            isbn = new char[1];
            isbn[0] = '\0';
        }

        title = new char[strlen(t) + 1];
        strcpy(title, t);
    }

    Book(const Book &b) {
        authors = b.authors;
        copies = b.copies;
        price = b.price;

        title = new char[strlen(b.title) + 1];
        strcpy(title, b.title);

        isbn = new char[strlen(b.isbn) + 1];
        strcpy(isbn, b.isbn);
    }

    bool validISBN(const char *i) {
        int len = strlen(i);

        if (len == 10) {
            int sum = 0;
            for (int k = 0; k < 10; k++) {
                if (i[k] < '0' || i[k] > '9') return false;
                int d = i[k] - '0';
                sum += (k + 1) * d;
            }
            return (sum % 11 == 0);
        }

        if (len == 13) {
            int sum = 0;
            for (int k = 0; k < 13; k++) {
                if (i[k] < '0' || i[k] > '9') return false;
                int d = i[k] - '0';
                if (k % 2 == 0) sum += d;
                else sum += 3 * d;
            }
            return (sum % 10 == 0);
        }

        return false;
    }

    void setTitle(const char *t) {
        delete[] title;
        title = new char[strlen(t) + 1];
        strcpy(title, t);
    }

    void setAuthors(int a) { authors = a; }

    void setISBN(const char *i) {
        if (validISBN(i)) {
            delete[] isbn;
            isbn = new char[strlen(i) + 1];
            strcpy(isbn, i);
        }
    }

    void setPrice(float p) { price = p; }

    void setCopies(int c) { copies = c; }

    char* getTitle() { return title; }
    int getAuthors() { return authors; }
    char* getISBN() { return isbn; }
    float getPrice() { return price; }
    int getCopies() { return copies; }

    void show() {
        cout << title << endl;
        cout << authors << endl;
        cout << isbn << endl;
        cout << price << endl;
        cout << copies << endl;
    }

    ~Book() {
        delete[] title;
        delete[] isbn;
    }
};

int main() {
    Book b1("CPP", 2, "0306406152", 500.5, 3);
    b1.show();

    Book b2 = b1;
    b2.show();

    return 0;
}

DBMS

CREATE DATABASE traveldb2;
USE traveldb2;

CREATE TABLE Route (
    route_no INT,
    source VARCHAR(20),
    destination VARCHAR(20),
    no_of_station INT
);

CREATE TABLE Bus (
    bus_no INT,
    capacity INT,
    depot_name VARCHAR(20),
    route_no INT
);

INSERT INTO Route VALUES
(191, 'Swargate', 'Kothrud', 12),
(192, 'Pune Station', 'Hinjewadi', 15);

INSERT INTO Bus VALUES
(143, 90, 'Swargate Depot', 191),
(144, 110, 'Pimpri Depot', 192);

a)

CREATE FUNCTION routeDetails191()
RETURNS VARCHAR(500)
DETERMINISTIC
BEGIN
    DECLARE result VARCHAR(500);

    SELECT CONCAT(route_no, ' ', source, ' ', destination, ' ', no_of_station)
    INTO result
    FROM Route
    WHERE route_no = 191;

    RETURN result;
END $$

b)

CREATE TRIGGER check_route
BEFORE INSERT ON Route
FOR EACH ROW
BEGIN
    IF NEW.route_no <= 0 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Invalid route number';
    END IF;
END $$

c)

CREATE PROCEDURE bus143Details()
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE bno INT;
    DECLARE cap INT;
    DECLARE depot VARCHAR(20);
    DECLARE rno INT;

    DECLARE cur CURSOR FOR
        SELECT * FROM Bus WHERE bus_no = 143;

    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

    OPEN cur;

    read_loop: LOOP
        FETCH cur INTO bno, cap, depot, rno;

        IF done THEN
            LEAVE read_loop;
        END IF;

        SELECT bno, cap, depot, rno;
    END LOOP;

    CLOSE cur;
END $$

