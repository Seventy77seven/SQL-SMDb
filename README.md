# SQL-SMDb
SQL Postgres code for Securities Master Database
"@ES"

Creating Instument Price Table
========================================

CREATE TABLE "@ES" (
id BIGSERIAL NOT NULL PRIMARY KEY,
Date DATE NOT NULL,
Time TIME NOT NULL,
Inc_Vol INTEGER,
Volume INTEGER NOT NULL,
Open NUMERIC (6, 2) NOT NULL,
High NUMERIC (6, 2) NOT NULL,
Low NUMERIC (6, 2) NOT NULL,
Close NUMERIC (6, 2) NOT NULL);


contracts

Creating contract specs table
=============================================

CREATE TABLE contracts (
id BIGSERIAL NOT NULL PRIMARY KEY,
root_symbol VARCHAR(50) NOT NULL,
exchange VARCHAR(50) NOT NULL,
description VARCHAR(50) NOT NULL,
pit_open TIME NOT NULL,
pit_close TIME NOT NULL,
roll_over INTEGER,
expiry VARCHAR(50) NOT NULL,
F BOOLEAN NOT NULL,
G BOOLEAN NOT NULL,
H BOOLEAN NOT NULL,
J BOOLEAN NOT NULL,
K BOOLEAN NOT NULL,
M BOOLEAN NOT NULL,
N BOOLEAN NOT NULL,
Q BOOLEAN NOT NULL,
U BOOLEAN NOT NULL,
V BOOLEAN NOT NULL,
X BOOLEAN NOT NULL,
Z BOOLEAN NOT NULL);

SQL Code for copying CSV Data into Contracts table
=================================================

COPY contracts(Root_Symbol, Exchange, Description, Pit_Open, Pit_Close, Roll_Over, Expiry, F, G, H, J, K, M, N, Q, U, V, X, Z) FROM '/home/alpha/Desktop/Contract_Specs_Table.csv' DELIMITER ',' CSV HEADER;

SQL Code for copying CSV data into a "@ES" table
================================================

testdb=# COPY "@ES"(Date, Time, Inc_Vol, Volume, Open, High, Low, Close) FROM '/home/alpha/Desktop/ES_test.csv' DELIMITER ',' CSV HEADER;


SQL Code for decreasing granularity of time frame price bars ie ticks to daily
==============================================================================

SELECT date_trunc('day', date) date, (array_agg(open ORDER BY time ASC))[1] o,
MAX(high) h, MIN(low) l, (array_agg(close ORDER BY time DESC))[1] c
FROM "@ES"
GROUP BY date_trunc('day', date) ORDER BY date LIMIT 100;

SQL Code for selecting pit hour data only
=======================================================================

SELECT * FROM "@ES" WHERE time BETWEEN (SELECT pit_open FROM "contracts" WHERE root_symbol = '@ES' LIMIT 1) AND (SELECT pit_close FROM "contracts" WHERE root_symbol = '@ES' LIMIT 1);

Combining minute bar processing with Pit hour data only
======================================================================

SELECT date_trunc('day', date) date, (array_agg(open ORDER BY time ASC))[1] o,
MAX(high) h, MIN(low) l, (array_agg(close ORDER BY time DESC))[1] c
FROM (SELECT * FROM "@ES" WHERE time BETWEEN (SELECT pit_open FROM "contracts" WHERE root_symbol = '@ES' LIMIT 1) AND (SELECT pit_close FROM "contracts" WHERE root_symbol = '@ES' LIMIT 1))
GROUP BY date_trunc('day', date) ORDER BY date LIMIT 100;


                                                                                                                        77,0-1        Bot

                                                                                                                        1,5           Top
