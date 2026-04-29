SLip 12
#include <iostream>
using namespace std;

class cuboidSolid {
    float length, breadth, height, mass;

public:
    void setLength(float l) { length = l; }
    void setBreadth(float b) { breadth = b; }
    void setHeight(float h) { height = h; }
    void setMass(float m) { mass = m; }

    float getLength() { return length; }
    float getBreadth() { return breadth; }
    float getHeight() { return height; }
    float getMass() { return mass; }

    float getVolume() {
        return length * breadth * height;
    }

    float getSurfaceArea() {
        return 2 * (length * breadth + breadth * height + height * length);
    }

    float getDensity() {
        return mass / getVolume();
    }
};

int main() {
    cuboidSolid c;

    float l, b, h, m;
    cin >> l >> b >> h >> m;

    c.setLength(l);
    c.setBreadth(b);
    c.setHeight(h);
    c.setMass(m);

    cout << c.getVolume() << endl;
    cout << c.getSurfaceArea() << endl;
    cout << c.getDensity() << endl;

    return 0;
}

DBMS 
CREATE DATABASE traveldb3;
USE traveldb3;

CREATE TABLE Route (
    route_no INT,
    source VARCHAR(20),
    destination VARCHAR(20),
    no_of_station INT
);

CREATE TABLE Bus (
    bus_no INT,
    capacity INT,
    depot_name VARCHAR(20)
);

CREATE TABLE Route_Bus (
    route_no INT,
    bus_no INT
);
INSERT INTO Route VALUES
(1, 'Swargate', 'Sangvi', 10),
(2, 'Pune Station', 'Hinjewadi', 15);

INSERT INTO Bus VALUES
(234, 80, 'Swargate Depot'),
(235, 120, 'Pimpri Depot');

INSERT INTO Route_Bus VALUES
(1, 234),
(1, 235),
(2, 235);

a)

CREATE FUNCTION busFromSwargate()
RETURNS VARCHAR(500)
DETERMINISTIC
BEGIN
    DECLARE result VARCHAR(500);

    SELECT GROUP_CONCAT(B.bus_no)
    INTO result
    FROM Bus B
    JOIN Route_Bus RB ON B.bus_no = RB.bus_no
    JOIN Route R ON RB.route_no = R.route_no
    WHERE R.source = 'Swargate' AND R.destination = 'Sangvi';

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

CREATE PROCEDURE bus234Details()
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE bno INT;
    DECLARE cap INT;
    DECLARE depot VARCHAR(20);

    DECLARE cur CURSOR FOR
        SELECT * FROM Bus WHERE bus_no = 234;

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



