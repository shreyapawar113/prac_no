slip 14
#include <iostream>
#include <cstring>
using namespace std;

class Time24;

class Time12
{
private:
    int hh, mm, ss;
    char ap[3];

public:
    Time12(int h = 12, int m = 0, int s = 0, const char *p = "AM")
    {
        hh = h;
        mm = m;
        ss = s;
        strcpy(ap, p);
    }

    void display()
    {
        cout << hh << ":" << mm << ":" << ss << " " << ap << endl;
    }

    friend void convertTo24(Time12, Time24 &);
    friend void convertTo12(Time24, Time12 &);
    friend void compare(Time12, Time24);
};

class Time24
{
private:
    int hh, mm, ss;

public:
    Time24(int h = 0, int m = 0, int s = 0)
    {
        hh = h;
        mm = m;
        ss = s;
    }

    void display()
    {
        cout << hh << ":" << mm << ":" << ss << endl;
    }

    friend void convertTo24(Time12, Time24 &);
    friend void convertTo12(Time24, Time12 &);
    friend void compare(Time12, Time24);
};

void convertTo24(Time12 t12, Time24 &t24)
{
    int h = t12.hh;

    if (strcmp(t12.ap, "PM") == 0 && h != 12)
        h += 12;
    if (strcmp(t12.ap, "AM") == 0 && h == 12)
        h = 0;

    t24.hh = h;
    t24.mm = t12.mm;
    t24.ss = t12.ss;
}

void convertTo12(Time24 t24, Time12 &t12)
{
    int h = t24.hh;
    char p[3];

    if (h == 0)
    {
        h = 12;
        strcpy(p, "AM");
    }
    else if (h < 12)
    {
        strcpy(p, "AM");
    }
    else if (h == 12)
    {
        strcpy(p, "PM");
    }
    else
    {
        h -= 12;
        strcpy(p, "PM");
    }

    t12.hh = h;
    t12.mm = t24.mm;
    t12.ss = t24.ss;
    strcpy(t12.ap, p);
}

void compare(Time12 t12, Time24 t24)
{
    Time24 temp;
    convertTo24(t12, temp);

    if (temp.hh == t24.hh && temp.mm == t24.mm && temp.ss == t24.ss)
        cout << "Times are equal" << endl;
    else
        cout << "Times are not equal" << endl;
}

int main()
{
    Time12 t12(10, 30, 0, "PM");
    Time24 t24;

    convertTo24(t12, t24);
    cout << "24-hour format: ";
    t24.display();

    Time12 t12b;
    convertTo12(t24, t12b);
    cout << "12-hour format: ";
    t12b.display();

    compare(t12, t24);

    return 0;
}

DBMS
CREATE DATABASE traveldb4;
USE traveldb4;

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
(223, 90, 'Swargate Depot'),
(224, 120, 'Pimpri Depot');

INSERT INTO Route VALUES
(1, 'Swargate', 'Sangvi', 10),
(2, 'Pune Station', 'Hinjewadi', 15);

INSERT INTO Bus_Route VALUES
(223, 1),
(223, 2),
(224, 2);

a)

CREATE FUNCTION routesOfBus223()
RETURNS VARCHAR(500)
DETERMINISTIC
BEGIN
    DECLARE result VARCHAR(500);

    SELECT GROUP_CONCAT(R.route_no, ' ', R.source, ' ', R.destination)
    INTO result
    FROM Route R
    JOIN Bus_Route BR ON R.route_no = BR.route_no
    WHERE BR.bus_no = 223;

    RETURN result;
END $$


b)

CREATE TRIGGER check_bus
BEFORE INSERT ON Bus
FOR EACH ROW
BEGIN
    IF NEW.bus_no <= 0 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Invalid bus number';
    END IF;
END $$


c)

CREATE PROCEDURE route1Details()
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE rno INT;
    DECLARE src VARCHAR(20);
    DECLARE dest VARCHAR(20);
    DECLARE st INT;

    DECLARE cur CURSOR FOR
        SELECT * FROM Route WHERE route_no = 1;

    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

    OPEN cur;

    read_loop: LOOP
        FETCH cur INTO rno, src, dest, st;

        IF done THEN
            LEAVE read_loop;
        END IF;

        SELECT rno, src, dest, st;
    END LOOP;

    CLOSE cur;
END $$




