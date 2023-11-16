
# Assignment CS-105 :  Persistent Data Management 2023-24

In this assignment we are supposed to design database for InterCity Express Trains (IET) to support there day to day operations by storing information about schedule of coaches 

# Part 1 : Group Work

##  Group Members
- Shruti Sunil Navale (Roll no. 23112022)
- Aumkar Ashok Chitrasagar

## Identification of Entities

+ Agent: Contains information about travel agents, including their ID, name, contact details, and commission rates.

+ Booking: Stores details of bookings, such as booking ID, schedule ID, booking date, passenger ID, agent ID, seater ID, booking status, etc.

+ Coach: Contains information about coaches, including coach number, mileage, and maintenance schedule.

+ Driver: Stores details about drivers, including driver ID, name, contact number, city of residence, and roster information.

+ Maintenance: Keeps track of routine maintenance schedules for coaches based on mileage and time intervals.

+ Passenger: Holds information about passengers, including passenger ID, name, age, contact number, and address.

+ Roster: Contains details of drivers and routes and time at which drivers are suppose to address routes, including staff ID, driver name, contact number, city of residence, and schedule information.

+ Route: Stores information about routes, including route ID, source, destination, distance, duration, and operating days.

+ Schedule: Contains details of coach schedules, including schedule ID, coach ID, route ID, departure, and arrival timings.

+ ScheduleTracking: Keeps track of schedule by giving actual timings for departure and arrival of coaches.

+ StandbyCoach: Stores information about standby coaches that are activated if a coach arrives late .

+ IntermediateStation: Contains information about intermediatestations, including station ID and name.

+ Intermediatestation_Schedule: Links stations to routes, indicating order of stops of coaches in routes at intermediatestations and its departure and arrival time at intermediatestations.

+ Ticket: Stores details about tickets, including ticket ID, booking ID, passenger ID, seat number, prices, and discounts.

## ER Diagram
![image](https://github.com/Shrrutii29/intercityExpressTrains/assets/121250741/5657949f-444f-4333-ac2e-8d2e65443784)

## Creation of database-Intercityy
SQL queries to perform following steps to create database for IntercityExpressTrains I have mentioned in textfile named intercity.txt 

- Create database
- Create tables 
- Insert sample data in table

# Part 2 : Individual Work
doneby -Shruti Sunil Navale
## Set B

#### 1.Show schedule of all trips including main driver information for 10th October this year.

    SQL Query : 

     mysql> SELECT DISTINCT
    ->     s.sid,
    ->     s.cid,
    ->     s.rid,
    ->     s.departure,
    ->     s.arrival,
    ->     d.did AS driver_id,
    ->     d.name AS driver_name,
    ->     d.contactno AS driver_contact
    -> FROM
    ->     schedule s
    -> JOIN
    ->     roster r ON s.rid = r.rid
    -> JOIN
    ->     driver d ON r.did = d.did
    -> WHERE
    ->     r.remark = 'Main Driver' AND DATE(s.departure) = '2023-10-10';


    Output :
    +-------+------+-------+---------------------+---------------------+-----------+---------------+----------------+
    | sid   | cid  | rid   | departure           | arrival             | driver_id | driver_name   | driver_contact |
    +-------+------+-------+---------------------+---------------------+-----------+---------------+----------------+
    | SC148 | C104 | RB301 | 2023-10-10 12:30:00 | 2023-10-10 15:30:00 | D007      | Suraj Pillai  | 9876001122     |
    | SC149 | C105 | RB501 | 2023-10-10 01:00:00 | 2023-10-10 10:45:00 | D009      | Rahul Reddy   | 9898776655     |
    | SC150 | C107 | RF204 | 2023-10-10 02:30:00 | 2023-10-10 08:30:00 | D011      | Tanay Bhat    | 9876654321     |
    | SC149 | C105 | RB501 | 2023-10-10 01:00:00 | 2023-10-10 10:45:00 | D012      | Karthik Joshi | 9765666778     |
    | SC150 | C107 | RF204 | 2023-10-10 02:30:00 | 2023-10-10 08:30:00 | D012      | Karthik Joshi | 9765666778     |
    +-------+------+-------+---------------------+---------------------+-----------+---------------+----------------+


#### 2.List all coaches with mileage between 4000 and 4999 km covered for September this year; include information on the coach, its last service date and total number of scheduled trips.

    SQL Query :

    mysql> SELECT

    ->     c.cid,
    ->     c.cname,
    ->     c.capacity,
    ->     c.mileage,
    ->     c.facilities,
    ->     MAX(m.lastmaintenance) AS last_service_date,
    ->     COUNT(s.sid) AS total_trips
    -> FROM
    ->     coach c
    -> JOIN
    ->     maintenance m ON c.cid = m.cid
    -> JOIN
    ->     schedule s ON c.cid = s.cid
    -> WHERE
    ->     c.mileage BETWEEN 4000 AND 4999
    ->     AND MONTH(s.departure) = 9
    -> GROUP BY
    ->     c.cid, c.cname, c.capacity, c.mileage,c.facilities;

     Output : 
    +------+------------------+----------+---------+-----------------------------------------------------------+-------------------+-------------+
    | cid  | cname            | capacity | mileage | facilities                                                | last_service_date | total_trips |
    +------+------------------+----------+---------+-----------------------------------------------------------+-------------------+-------------+
    | C102 | Janshatabdi      |       30 |    4800 | Meal Services, Wi-Fi, Reading Materials, Air Conditioning | 2023-08-15        |           2 |
    | C104 | LTT VSKP Express |       30 |    4300 | Meal Services, Wi-Fi, Reading Materials, Air Conditioning | 2023-10-10        |           2 |
    | C107 | VSG YPR EXP      |       30 |    4900 | Meal Services, Wi-Fi, Reading Materials, Air Conditioning | 2024-01-25        |           2 |
    +------+------------------+----------+---------+-----------------------------------------------------------+-------------------+-------------+


#### 3.List all agents, in descending order of percentage of confirmed booking each trip in the month of October this year. Include agent and route information in your result.

    SQL Query : 
    mysql> SELECT

    ->     a.aid AS agent_id,
    ->     a.aname AS agent_name,
    ->     r.rid AS route_id,
    ->     r.source AS source,
    ->     r.dest AS destination,
    ->     s.sid AS schedule_id,
    ->     COUNT(*) AS total_bookings,
    ->     SUM(CASE WHEN b.bstatus = 'Confirmed' THEN 1 ELSE 0 END) AS confirmed_bookings,
    ->     (SUM(CASE WHEN b.bstatus = 'Confirmed' THEN 1 ELSE 0 END) / COUNT(*)) * 100 AS percentage_confirmed
    -> FROM
    ->     agent a
    -> JOIN
    ->     booking b ON a.aid = b.aid
    -> JOIN
    ->     schedule s ON b.sid = s.sid
    -> JOIN
    ->     route r ON s.rid = r.rid
    -> WHERE
    ->     MONTH(s.departure) = 10
    ->     AND YEAR(s.departure) = YEAR(CURDATE())
    -> GROUP BY
    ->     agent_id, agent_name, route_id, source, destination, schedule_id
    -> ORDER BY
    ->     percentage_confirmed DESC;

    
    Output : 
    
    +----------+------------------+----------+-------------+----------------+-------------+----------------+--------------------+----------------------+
    | agent_id | agent_name       | route_id | source      | destination    | schedule_id | total_bookings | confirmed_bookings | percentage_confirmed |
    +----------+------------------+----------+-------------+----------------+-------------+----------------+--------------------+----------------------+
    | A002     |  Priya Patel     | RB101    | Gandhinagar | Mumbai Central | SC101       |              1 |                  1 |             100.0000 |
    | A003     |  Rajiv Singh     | RB101    | Gandhinagar | Mumbai Central | SC101       |              1 |                  1 |             100.0000 |
    | A001     |  Arjun Sharma    | RB101    | Gandhinagar | Mumbai Central | SC102       |              1 |                  1 |             100.0000 |
    | A003     |  Rajiv Singh     | RB101    | Gandhinagar | Mumbai Central | SC102       |              1 |                  1 |             100.0000 |
    | A005     |  Vikram Verma    | RB101    | Gandhinagar | Mumbai Central | SC102       |              1 |                  1 |             100.0000 |
    | A009     |  Aryan Khanna    | RB101    | Gandhinagar | Mumbai Central | SC102       |              1 |                  1 |             100.0000 |
    | A011     |  Siddharth Mehta | RB204    | Goa         | Mumbai         | SC110       |              1 |                  1 |             100.0000 |
    | A001     |  Arjun Sharma    | RB204    | Goa         | Mumbai         | SC110       |              1 |                  1 |             100.0000 |
    | A001     |  Arjun Sharma    | RB201    | Himachal    | New Delhi      | SC103       |              1 |                  1 |             100.0000 |
    | A001     |  Arjun Sharma    | RB203    | Solapur     | Mumbai         | SC107       |              1 |                  1 |             100.0000 |
    | A001     |  Arjun Sharma    | RB101    | Gandhinagar | Mumbai Central | SC101       |              2 |                  1 |              50.0000 |
    | A004     |  Ananya Kapoor   | RB101    | Gandhinagar | Mumbai Central | SC102       |              2 |                  1 |              50.0000 |
    | A012     |  Sneha Joshi     | RB203    | Solapur     | Mumbai         | SC107       |              2 |                  1 |              50.0000 |
    | A006     |  Nandini Gupta   | RB101    | Gandhinagar | Mumbai Central | SC102       |              1 |                  0 |               0.0000 |
    | A010     |  Aisha Malik     | RB101    | Gandhinagar | Mumbai Central | SC102       |              1 |                  0 |               0.0000 |
    | A004     |  Ananya Kapoor   | RB204    | Goa         | Mumbai         | SC110       |              1 |                  0 |               0.0000 |
    | A012     |  Sneha Joshi     | RB201    | Himachal    | New Delhi      | SC103       |              1 |                  0 |               0.0000 |
    +----------+------------------+----------+-------------+----------------+-------------+----------------+--------------------+----------------------+

#### 4.Display the details of the routes where majority of bookings are not made by agents.

    SQL Query :
    mysql> SELECT
    ->     r.rid AS route_id,
    ->     r.source,
    ->     r.dest,
    ->     COUNT(*) AS total_bookings,
    ->     SUM(CASE WHEN b.aid IS NULL THEN 1 ELSE 0 END) AS bookings_by_passengers,
    ->     SUM(CASE WHEN b.aid IS NOT NULL THEN 1 ELSE 0 END) AS bookings_by_agents
    -> FROM
    ->     route r
    -> LEFT JOIN
    ->     schedule s ON r.rid = s.rid
    -> LEFT JOIN
    ->     booking b ON s.sid = b.sid
    -> GROUP BY
    ->     route_id, r.source, r.dest
    -> HAVING
    ->     bookings_by_passengers > bookings_by_agents OR bookings_by_agents IS NULL;


    Output :
    +----------+-----------------+---------------+----------------+------------------------+--------------------+
    | route_id | source          | dest          | total_bookings | bookings_by_passengers | bookings_by_agents |
    +----------+-----------------+---------------+----------------+------------------------+--------------------+
    | RB201    | Himachal        | New Delhi     |              9 |                      5 |                  4 |
    | RB202    | Sainagar Shirdi | Mumbai        |              5 |                      5 |                  0 |
    | RB301    | Indore          | Bhopal        |              1 |                      1 |                  0 |
    | RB302    | Delhi           | Bhopal        |              1 |                      1 |                  0 |
    | RB401    | Visakhapatnam   | Secunderabad  |              1 |                      1 |                  0 |
    | RB501    | Ajmer           | Lonavala      |              1 |                      1 |                  0 |
    | RB601    | Bengaluru       | Dharwad       |              1 |                      1 |                  0 |
    | RF201    | New Delhi       | Himachal      |              5 |                      5 |                  0 |
    | RF203    | Mumbai          | Solapur       |              6 |                      6 |                  0 |
    | RF204    | Mumbai          | Goa           |              7 |                      7 |                  0 |
    | RF301    | Bhopal          | Indore        |              1 |                      1 |                  0 |
    | RF302    | Bhopal          | Delhi         |              1 |                      1 |                  0 |
    | RF401    | Secunderabad    | Visakhapatnam |              1 |                      1 |                  0 |
    | RF501    | Lonavala        | Ajmer         |              1 |                      1 |                  0 |
    | RF601    | Dharwad         | Bengaluru     |              1 |                      1 |                  0 |
    +----------+-----------------+---------------+----------------+------------------------+--------------------+

#### 5.Display the details of the agents who have made maximum commission in the Month of September.

    SQL Query
    mysql> SELECT
    ->     a.aid AS agent_id,
    ->     a.aname AS agent_name,
    ->     SUM(IF(b.bstatus = 'Confirmed', t.total_price * a.commission, 0)) AS total_commission
    -> FROM
    ->     booking b
    -> JOIN
    ->     agent a ON b.aid = a.aid
    -> JOIN
    ->     ticket t ON b.bid = t.bid
    -> WHERE
    ->     MONTH(b.bdate) = 9
    -> GROUP BY
    ->     agent_id, agent_name
    -> ORDER BY
    ->     total_commission DESC
    -> LIMIT 1;
    

    output : 
    +----------+---------------+--------------------+
    | agent_id | agent_name    | total_commission   |
    +----------+---------------+--------------------+
    | A001     |  Arjun Sharma | 28.750000428408384 |
    +----------+---------------+--------------------+









