slip 14
#include <iostream>
#include <cstring>
using namespace std;

class Time24;

class Time12 {
    int h, m, s;
    char ap[3];

public:
    Time12(int hh=12, int mm=0, int ss=0, const char *a="AM") {
        h = hh;
        m = mm;
        s = ss;
        strcpy(ap, a);
    }

    friend Time24 convert(Time12);
    friend Time12 convert(Time24);
    friend void compare(Time12, Time24);

    void show() {
        cout << h << ":" << m << ":" << s << " " << ap << endl;
    }
};

class Time24 {
    int h, m, s;

public:
    Time24(int hh=0, int mm=0, int ss=0) {
        h = hh;
        m = mm;
        s = ss;
    }

    friend Time24 convert(Time12);
    friend Time12 convert(Time24);
    friend void compare(Time12, Time24);

    void show() {
        cout << h << ":" << m << ":" << s << endl;
    }
};

Time24 convert(Time12 t) {
    int hh = t.h;
    if (strcmp(t.ap, "PM") == 0 && hh != 12) hh += 12;
    if (strcmp(t.ap, "AM") == 0 && hh == 12) hh = 0;
    return Time24(hh, t.m, t.s);
}

Time12 convert(Time24 t) {
    int hh = t.h;
    char ap[3];

    if (hh >= 12) {
        strcpy(ap, "PM");
        if (hh > 12) hh -= 12;
    } else {
        strcpy(ap, "AM");
        if (hh == 0) hh = 12;
    }

    return Time12(hh, t.m, t.s, ap);
}

void compare(Time12 t1, Time24 t2) {
    Time24 t1_24 = convert(t1);

    if (t1_24.h == t2.h && t1_24.m == t2.m && t1_24.s == t2.s)
        cout << "Equal" << endl;
    else
        cout << "Not Equal" << endl;
}

int main() {
    Time12 t12(10, 30, 0, "PM");
    Time24 t24(22, 30, 0);

    Time24 c1 = convert(t12);
    Time12 c2 = convert(t24);

    c1.show();
    c2.show();

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




