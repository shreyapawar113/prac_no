slip 2

#include <iostream>
using namespace std;

class TalkingInterface;

class Person
{
private:
    string name;

public:
    Person(string n)
    {
        name = n;
    }

    friend class TalkingInterface;
};

class TalkingInterface
{
public:
    void greet()
    {
        cout << "Hi" << endl;
    }

    void greet(string msg)
    {
        cout << msg << endl;
    }

    void greet(string msg, int n)
    {
        for (int i = 0; i < n; i++)
        {
            cout << msg << endl;
        }
    }

    void Tables()
    {
        for (int i = 1; i <= 10; i++)
        {
            for (int j = 1; j <= 10; j++)
            {
                cout << i * j << " ";
            }
            cout << endl;
        }
    }

    void Tables(int n)
    {
        for (int i = 1; i <= 10; i++)
        {
            cout << n << " x " << i << " = " << n * i << endl;
        }
    }

    void Tables(int n, int start)
    {
        for (int i = start; i <= 10; i++)
        {
            cout << n << " x " << i << " = " << n * i << endl;
        }
    }

    void Tables(int n, int start, int end)
    {
        for (int i = start; i <= end; i++)
        {
            cout << n << " x " << i << " = " << n * i << endl;
        }
    }

    void use(Person p)
    {
        cout << "Hello " << p.name << endl;
        greet();
        greet("Namaste");
        greet("hello", 10);
        Tables();
        Tables(2);
        Tables(2, 3);
        Tables(2, 3, 6);
    }
};

int main()
{
    Person p("Ujwal");
    TalkingInterface t;
    t.use(p);
    return 0;
}
Slip 2 DBMS

CREATE DATABASE travel;
USE travel;

CREATE TABLE Route (
    route_no INT,
    source VARCHAR(50),
    destination VARCHAR(50),
    no_of_station INT
);

CREATE TABLE Bus (
    bus_no INT,
    capacity INT,
    depot_name VARCHAR(50)
);

CREATE TABLE Route_Bus (
    route_no INT,
    bus_no INT
);

a)
DELIMITER $$

CREATE FUNCTION getBusDetails()
RETURNS VARCHAR(500)
DETERMINISTIC
BEGIN
    DECLARE result VARCHAR(500);

    SELECT GROUP_CONCAT(B.bus_no)
    INTO result
    FROM Bus B
    JOIN Route_Bus RB ON B.bus_no = RB.bus_no
    JOIN Route R ON RB.route_no = R.route_no
    WHERE R.source = 'swargate' AND R.destination = 'sangvi';

    RETURN result;
END $$

b)
DELIMITER $$

CREATE PROCEDURE routeDetails()
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE r_no INT;
    DECLARE src VARCHAR(50);
    DECLARE dest VARCHAR(50);
    DECLARE stations INT;

    DECLARE cur CURSOR FOR 
        SELECT * FROM Route WHERE route_no = 2;

    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

    OPEN cur;

    read_loop: LOOP
        FETCH cur INTO r_no, src, dest, stations;

        IF done THEN
            LEAVE read_loop;
        END IF;

        SELECT r_no, src, dest, stations;
    END LOOP;

    CLOSE cur;
END $$

c)
DELIMITER $$

CREATE TRIGGER check_route_no
BEFORE INSERT ON Route
FOR EACH ROW
BEGIN
    IF NEW.route_no <= 0 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Invalid route number';
    END IF;
END $$
