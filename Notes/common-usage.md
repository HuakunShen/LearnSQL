# SQL Common Usage (MySQL)

[Jupyter Notebook With Output](./common-usage.ipynb)

```sql
-- show databases
SHOW DATABASES;
-- show users and show privileges
SELECT *
FROM mysql.user;

-- create and drop schema/databse
CREATE SCHEMA testschema;
DROP SCHEMA IF EXISTS testschema;

CREATE DATABASE testdb;
CREATE DATABASE IF NOT EXISTS testdb;
DROP DATABASE IF EXISTS test;

-- create privileged user
CREATE USER 'admin'@'localhost' IDENTIFIED BY 'admin';
GRANT ALL PRIVILEGES ON *.* TO 'admin'@'localhost';
FLUSH PRIVILEGES;
SHOW GRANTS FOR admin@localhost;
-- remove all privileges
REVOKE ALL PRIVILEGES, GRANT OPTION FROM user@localhost;
SHOW GRANTS FOR admin@localhost;

-- drop user
DROP USER 'admin'@'localhost';

-- grant specific privileges
CREATE USER 'user'@'localhost' IDENTIFIED BY 'password';
SHOW GRANTS FOR user@localhost;
GRANT ALL ON world.* TO 'user'@'localhost';
SHOW GRANTS FOR user@localhost;

USE world;

SHOW TABLES;

SHOW TABLE STATUS LIKE 'city';
SHOW ENGINES;
ALTER TABLE city
    ENGINE = 'myisam';

DESC city;
DESCRIBE country;
DESCRIBE countrylanguage;
SHOW CREATE TABLE city;

-- crud
-- read
SELECT *
FROM city
LIMIT 5;
SELECT *
FROM country
LIMIT 5;
SELECT *
FROM countrylanguage
LIMIT 5;

-- country who has the largest percentage of population speaking english
SELECT c.name
FROM country AS c
         INNER JOIN
     (SELECT countrycode
      FROM countrylanguage
      WHERE percentage = (SELECT max(percentage) AS percentage FROM countrylanguage WHERE language = 'english')
        AND language = 'english') AS cl
     ON c.code = cl.countrycode
;

-- percentage of chinese speaking population in the world population
SELECT concat(round((SELECT sum(population.population)
                     FROM (SELECT pbp.population * pbp.percentage / 100 AS population
                           FROM (SELECT c.population, cl.percentage
                                 FROM country AS c
                                          INNER JOIN (SELECT * FROM countrylanguage WHERE language = 'chinese') AS cl
                                                     ON c.code = cl.countrycode) AS pbp) AS population) /
                    (SELECT sum(population) FROM country) * 100, 2), '%') AS ratio;


-- create
INSERT INTO city (name, countrycode, district, population)
VALUES ('shenzhen', 'chn', 'unknown', 10000000);

SELECT *
FROM city
ORDER BY id DESC
LIMIT 1;

CREATE TABLE person
(
    id                int NOT NULL AUTO_INCREMENT,
    firstname         varchar(30),
    lastname          varchar(30),
    home_country_code char(3),
    CONSTRAINT pk_id PRIMARY KEY (id)
) ENGINE = innodb;

CREATE TABLE student
(
    id       int NOT NULL AUTO_INCREMENT,
    personid int NOT NULL,
    CONSTRAINT pk_id PRIMARY KEY (id),
    CONSTRAINT fk_person FOREIGN KEY (personid) REFERENCES person (id)
        ON UPDATE CASCADE
        ON DELETE CASCADE
) ENGINE = innodb;

DESC student;

ALTER TABLE student
    ADD COLUMN school varchar(50),
    ADD COLUMN grade  int UNSIGNED,
    ADD COLUMN class  int UNSIGNED;

DESC student;

INSERT INTO student (personid, school) -- should fail because of foreign key constraint, the personid dne in person table
VALUES (10, 'bcs');
INSERT INTO person (firstname, lastname)
VALUES ('first', 'last');
INSERT INTO person (id, firstname, lastname)
VALUES (100, 'one', 'two');

SELECT *
FROM person;
INSERT INTO student (personid, school)
VALUES ((SELECT id FROM person LIMIT 1), 'bcs');
INSERT INTO student (personid, school)
VALUES (100, 'uoft');
SELECT *
FROM student;

-- update
UPDATE person
SET id=99,
    firstname='three',
    lastname='four'
WHERE id = 100;

SELECT *
FROM person;

SELECT *
FROM student;
-- check cascading update student's personid

-- delete
DELETE
FROM person
WHERE id = 99;

SELECT *
FROM student; -- check cascading delete student with person id=100

DELETE
FROM person;

DELETE
FROM student;

DROP TABLE IF EXISTS student;

DROP TABLE IF EXISTS person;
```
