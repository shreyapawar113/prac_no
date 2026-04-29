slip5
Q1a)
#include <iostream>
using namespace std;

class Rectangle
{
private:
    float length, width;

public:
    void setlength(float l)
    {
        length = l;
    }

    void setwidth(float w)
    {
        width = w;
    }

    float perimeter()
    {
        return 2 * (length + width);
    }

    float area()
    {
        return length * width;
    }

    void show()
    {
        cout << "Length: " << length << " Width: " << width << endl;
    }
};

int main()
{
    Rectangle r1, r2;

    r1.setlength(10.5);
    r1.setwidth(5.2);

    r2.setlength(7.0);
    r2.setwidth(3.5);

    r1.show();
    cout << "Area: " << r1.area() << endl;
    cout << "Perimeter: " << r1.perimeter() << endl;

    r2.show();
    cout << "Area: " << r2.area() << endl;
    cout << "Perimeter: " << r2.perimeter() << endl;

    return 0;
}
b)
#include <iostream>
using namespace std;

class time
{
private:
    int h, m, s;

public:
    void settime(int hh, int mm, int ss)
    {
        h = hh;
        m = mm;
        s = ss;
    }

    void showtime()
    {
        cout << h << ":" << m << ":" << s << endl;
    }

    time add(time t)
    {
        time temp;

        temp.s = s + t.s;
        temp.m = m + t.m + temp.s / 60;
        temp.s = temp.s % 60;

        temp.h = h + t.h + temp.m / 60;
        temp.m = temp.m % 60;

        return temp;
    }
};

int main()
{
    time t1, t2, t3;

    t1.settime(2, 45, 50);
    t2.settime(3, 20, 30);

    t3 = t1.add(t2);

    t1.showtime();
    t2.showtime();
    cout << "After Addition: ";
    t3.showtime();

    return 0;
}

DBMS

CREATE DATABASE traveldb;
USE traveldb;

CREATE TABLE Bus (
    bus_no INT,
    capacity INT,
    depot_name VARCHAR(20)
);

CREATE TABLE Route (
    route_no INT,
    source VARCHAR(20),
    destination VARCHAR(20),
    no_of_stations INT
);

CREATE TABLE Bus_Route (
    bus_no INT,
    route_no INT
);

INSERT INTO Bus VALUES
(101, 120, 'Swargate'),
(102, 80, 'Pimpri'),
(103, 150, 'Nigdi');

INSERT INTO Route VALUES
(1, 'Swargate', 'Kothrud', 10),
(2, 'Pune Station', 'Hinjewadi', 15),
(3, 'Swargate', 'Baner', 8);

INSERT INTO Bus_Route VALUES
(101, 1),
(102, 2),
(103, 1),
(103, 3);

a)

CREATE FUNCTION routeToKothrud()
RETURNS VARCHAR(500)
DETERMINISTIC
BEGIN
    DECLARE result VARCHAR(500);

    SELECT GROUP_CONCAT(route_no)
    INTO result
    FROM Route
    WHERE destination = 'Kothrud';

    RETURN result;
END $$

b)
CREATE PROCEDURE busCapacity()
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE bno INT;
    DECLARE cap INT;
    DECLARE depot VARCHAR(20);

    DECLARE cur CURSOR FOR
        SELECT * FROM Bus WHERE capacity > 100;

    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

    OPEN cur;

    read_loop: LOOP
        FETCH cur INTO bno, cap, depot;

        IF done THEN
            LEAVE read_loop;
        END IF;

        SELECT bno, cap, depot;
    END LOOP;

    CLOSE cur;
END $$

c)

CREATE TRIGGER check_capacity
BEFORE INSERT ON Bus
FOR EACH ROW
BEGIN
    IF NEW.capacity > 100 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Capacity greater than 100 not allowed';
    END IF;
END $$

