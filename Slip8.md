Slip8
#include <iostream>
#include <cstring>
#include <cctype>
using namespace std;

class Book
{
private:
    char *title;
    int authors;
    char *isbn;
    float price;
    int copies;

    int isValidISBN(const char *num)
    {
        int len = strlen(num);

        if (len == 10)
        {
            int sum = 0;
            for (int i = 0; i < 10; i++)
            {
                if (!isdigit(num[i])) return 0;
                sum += (num[i] - '0') * (10 - i);
            }
            return (sum % 11 == 0);
        }
        else if (len == 13)
        {
            int sum = 0;
            for (int i = 0; i < 13; i++)
            {
                if (!isdigit(num[i])) return 0;

                if (i % 2 == 0)
                    sum += (num[i] - '0') * 1;
                else
                    sum += (num[i] - '0') * 3;
            }
            return (sum % 10 == 0);
        }
        return 0;
    }

public:
    Book()
    {
        title = new char[1];
        title[0] = '\0';

        isbn = new char[1];
        isbn[0] = '\0';

        authors = 1;
        price = 0;
        copies = 1;
    }

    Book(const char *t, int a, const char *i, float p, int c)
    {
        if (!isValidISBN(i))
        {
            cout << "Invalid ISBN" << endl;
            exit(0);
        }

        title = new char[strlen(t) + 1];
        strcpy(title, t);

        isbn = new char[strlen(i) + 1];
        strcpy(isbn, i);

        authors = a;
        price = p;
        copies = c;
    }

    Book(const Book &b)
    {
        title = new char[strlen(b.title) + 1];
        strcpy(title, b.title);

        isbn = new char[strlen(b.isbn) + 1];
        strcpy(isbn, b.isbn);

        authors = b.authors;
        price = b.price;
        copies = b.copies;
    }

    void setTitle(const char *t)
    {
        delete[] title;
        title = new char[strlen(t) + 1];
        strcpy(title, t);
    }

    void setAuthors(int a) { authors = a; }
    void setPrice(float p) { price = p; }
    void setCopies(int c) { copies = c; }

    void setISBN(const char *i)
    {
        if (isValidISBN(i))
        {
            delete[] isbn;
            isbn = new char[strlen(i) + 1];
            strcpy(isbn, i);
        }
    }

    const char* getTitle() { return title; }
    int getAuthors() { return authors; }
    const char* getISBN() { return isbn; }
    float getPrice() { return price; }
    int getCopies() { return copies; }

    void display()
    {
        cout << "Title: " << title << endl;
        cout << "Authors: " << authors << endl;
        cout << "ISBN: " << isbn << endl;
        cout << "Price: " << price << endl;
        cout << "Copies: " << copies << endl;
    }
};

int main()
{
    Book b1("CPP", 2, "9780306406157", 500.0, 5);
    Book b2 = b1;

    b1.display();
    cout << endl;
    b2.display();

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

