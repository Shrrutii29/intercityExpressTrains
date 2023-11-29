
# Assignment CS-105 :  Persistent Data Management 2023-24

In this assignment we are supposed to design database for InterCity Express Trains (IET) to support there day to day operations by storing information about schedule of coaches 

# Part 1 : Group Work

##  Group Members
- Shruti Sunil Navale (Roll no. 23112022)
- Aumkar Ashok Chitragar (Roll no. 23112006)

## Contribution
- ER Diagram : Shruti Navale & Aumkar Chitragar
- Schema : Shruti Navale
- Sample data : Shruti Navale & Aumkar Chitragar
- Readme File : Shruti Navale

## Assumption
- we have considered coach is train
- there are always some coaches which are idle so that we can use them as standbycoach
- sample data is not large considered only different situations where part 2 queries can run
- data entries for september,october,november,december is shown
- route between A to B and B to A is considered different but intermediate stations are considered same for both by changing stop_order only
- passenger can do many bookings but every booking for a schedule is related to a passenger
- seats numbering system is same in every coach
- ticket is per seat
 
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
## Set B : done by - Shruti Navale (23112022)

#### 1.Show schedule of all trips including main driver information for 10th October this year.

    SQL Query : 
    select distinct s.sid, s.cid,s.rid,s.departure,s.arrival,d.did as Maindriver_id,d.name as Maindriver_name,d.contactno AS Maindriver_contact from schedule s,driver d,roster r where s.rid = r.rid and r.did = d.did and r.remark = 'Main Driver' AND DATE(s.departure) = '2023-10-10';

    Output :
    +-------+------+-------+---------------------+---------------------+---------------+-----------------+--------------------+
    | sid   | cid  | rid   | departure           | arrival             | Maindriver_id | Maindriver_name | Maindriver_contact |
    +-------+------+-------+---------------------+---------------------+---------------+-----------------+--------------------+
    | SC148 | C104 | RB301 | 2023-10-10 12:30:00 | 2023-10-10 15:30:00 | D007          | Suraj Pillai    | 9876001122         |
    | SC149 | C105 | RB501 | 2023-10-10 01:00:00 | 2023-10-10 10:45:00 | D009          | Rahul Reddy     | 9898776655         |
    | SC150 | C107 | RF204 | 2023-10-10 02:30:00 | 2023-10-10 08:30:00 | D011          | Tanay Bhat      | 9876654321         |
    +-------+------+-------+---------------------+---------------------+---------------+-----------------+--------------------+


#### 2.List all coaches with mileage between 4000 and 4999 km covered for September this year; include information on the coach, its last service date and total number of scheduled trips.

    SQL Query :
    select c.cid, c.cname, c.capacity, c.mileage, c.facilities, max(m.lastmaintenance) as last_service_date, count( distinct s.sid) as total_trips from coach c join maintenance m on c.cid = m.cid join schedule s on c.cid = s.cid where c.mileage between 4000 and 4999 and month(s.departure) = 9 group by c.cid, c.cname, c.capacity, c.mileage, c.facilities;

     Output : 
    +------+------------------+----------+---------+-----------------------------------------------------------+-------------------+-------------+
    | cid  | cname            | capacity | mileage | facilities                                                | last_service_date | total_trips |
    +------+------------------+----------+---------+-----------------------------------------------------------+-------------------+-------------+
    | C102 | Janshatabdi      |       30 |    4800 | Meal Services, Wi-Fi, Reading Materials, Air Conditioning | 2023-08-15        |           1 |
    | C104 | LTT VSKP Express |       30 |    4300 | Meal Services, Wi-Fi, Reading Materials, Air Conditioning | 2023-10-10        |           2 |
    | C107 | VSG YPR EXP      |       30 |    4900 | Meal Services, Wi-Fi, Reading Materials, Air Conditioning | 2024-01-25        |           1 |
    +------+------------------+----------+---------+-----------------------------------------------------------+-------------------+-------------+

    
#### 3.List all agents, in descending order of percentage of confirmed booking each trip in the month of October this year. Include agent and route information in your result.

    SQL Query : 
    select a.aid as agent_id, a.aname as agent_name, r.rid as route_id, r.source, r.dest as destination, s.sid as schedule_id, count(*) as total_bookings, sum(b.bstatus = 'Confirmed') as confirmed_bookings, (sum(b.bstatus = 'Confirmed') / count(*)) * 100 as percentage_confirmed from agent a,booking b,schedule s,route r where a.aid = b.aid and b.sid = s.sid and s.rid = r.rid and month(s.departure) = 10 and year(s.departure) = year(curdate()) group by agent_id, route_id, schedule_id order by percentage_confirmed desc;

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
    | A001     |  Arjun Sharma    | RB203    | Dharwad     | Bengaluru      | SC107       |              1 |                  1 |             100.0000 |
    | A012     |  Sneha Joshi     | RB101    | Gandhinagar | Mumbai Central | SC101       |              1 |                  1 |             100.0000 |
    | A001     |  Arjun Sharma    | RB101    | Gandhinagar | Mumbai Central | SC101       |              2 |                  1 |              50.0000 |
    | A004     |  Ananya Kapoor   | RB101    | Gandhinagar | Mumbai Central | SC102       |              2 |                  1 |              50.0000 |
    | A012     |  Sneha Joshi     | RB203    | Dharwad     | Bengaluru      | SC107       |              2 |                  1 |              50.0000 |
    | A006     |  Nandini Gupta   | RB101    | Gandhinagar | Mumbai Central | SC102       |              1 |                  0 |               0.0000 |
    | A010     |  Aisha Malik     | RB101    | Gandhinagar | Mumbai Central | SC102       |              1 |                  0 |               0.0000 |
    | A004     |  Ananya Kapoor   | RB204    | Goa         | Mumbai         | SC110       |              1 |                  0 |               0.0000 |
    | A012     |  Sneha Joshi     | RB201    | Himachal    | New Delhi      | SC103       |              1 |                  0 |               0.0000 |
    +----------+------------------+----------+-------------+----------------+-------------+----------------+--------------------+----------------------+

#### 4.Display the details of the routes where majority of bookings are not made by agents.

    SQL Query :
     select r.rid as route_id, r.source, r.dest, count(distinct b.bid) as total_bookings,count(distinct b.booker_pid) as bookings_by_passengers,count(distinct b.aid) as bookings_by_agents from route r, booking b, schedule s where r.rid = s.rid and s.sid = b.sid and b.bstatus='Confirmed' group by route_id, r.source, r.dest having bookings_by_passengers > bookings_by_agents;


    Output :
    +----------+----------+-----------+----------------+------------------------+--------------------+
    | route_id | source   | dest      | total_bookings | bookings_by_passengers | bookings_by_agents |
    +----------+----------+-----------+----------------+------------------------+--------------------+
    | RB201    | Himachal | New Delhi |              3 |                      3 |                  2 |
    | RB204    | Goa      | Mumbai    |              7 |                      5 |                  3 |
    +----------+----------+-----------+----------------+------------------------+--------------------+

#### 5.Display the details of the agents who have made maximum commission in the Month of September.

    SQL Query
     select a.aid as agent_id, a.aname as agent_name, max(t.total_price * a.commission) as total_commission from booking b,agent a,ticket t where b.aid = a.aid and b.bid = t.bid and month(b.bdate) = 9 and b.bstatus="Confirmed" group by agent_id, agent_name order by total_commission desc limit 1;
     
    output : 
    +----------+---------------+--------------------+
    | agent_id | agent_name    | total_commission   |
    +----------+---------------+--------------------+
    | A009     |  Aryan Khanna | 22.500000335276127 |
    +----------+---------------+--------------------+
    
## Set C : done by - Aumkar Chitragar (23112006)
    I I have submitted desired queries in text file named Aumkar Part 2 Assignment.txt 







