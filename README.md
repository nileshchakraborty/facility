# Distributed Systems
### Spring Framework Web Application

The Spring Framework is an application framework and inversion of control container for the Java platform. The framework's core features can be used by any Java application, but there are extensions for building web applications on top of the Java EE platform.
More information here: [https://spring.io/](https://spring.io/)

## Running the JAR application 
**System requirements**
* Java 8 SDK or later

From the source directory go to the location of the application target folder:
Then run the application with the java compiler:

`java -jar  target/facility.jar`

```sh
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.5.2.RELEASE)
 
```

The application can be access locally at:

`http://localhost:8080/facility/`

Application routes:


|Routes|Response Type|Method|
|------|-------------|------|
|/chaos/rate/{rate} |application/json |ChaosController.setRate()|
|/facility/delete    |application/json |FacilityController.cleanDB()|
|/facility/{id}      |application/json | FacilityController.getFacility()|
|/facility/add       |application/json |FacilityController.addFacility()|
|/facility/all                      |application/json| FacilityController.getALL()|
|/facility/delete/{id}              |application/json| FacilityController.deleteByID()|
|/facility/maintenance/{id}         |application/json| MaintenanceController.getFacility()|
|/facility/maintenance/add          |application/json| MaintenanceController.addMaintenance()|
|/facility/maintenance/all          |application/json| MaintenanceController.getALL()|
|/facility/maintenance/delete/{id}  |application/json| MaintenanceController.deleteByID()|
|/facility/use/{id}                 |application/json| UseController.getMaintenance()|
|/facility/use/add                  |application/json| UseController.addFacilityUse()|
|/facility/use/all                  |application/json| UseController.getALL()|
|/facility/use/delete/{id}          |application/json| UseController.deleteByID()|

## Testing the spring framework context

To test the system we can generate traffic using the Facility client jar:
 
```sh
java -jar  target/client.jar

 Select an option from the menu:

 	 1) ADD Facility Client Traffic

 	 2) GET Facility Client Traffic

 	 3) DELETE Client Traffic

 	 4) GET/ADD/DELETE Client Traffic

 	 5) Quit
```


## AWS RDS - PostgreSQL Database

[AmazonRDS - DBInstance](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.DBInstanceClass.html)

|Instance | Description|
----------|------------|
TypeType|Small Instance - Current Generation|
Number of virtual cores|	1 vCPU|
Memory|2 GiB|
Network Performance | Low
Free Tier Eligible|	Yes|

Default settings for maximum number of concurrent connections 'max_connections' settings :

* LEAST({DBInstanceClassMemory/9531392},5000)

 setting | min_val | max_val | boot_val | reset_val | 
---------|---------|---------|----------|-----------|
 87      |  1      | 262143  | 100      | 87        |
 
 
To increase performance change to:
* GREATEST({DBInstanceClassMemory/9531392},5000)

More information about AWS RDS database metrics can be found here:
* [monitoring](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/rds-metricscollected.html)

### Database Traffic Summary
**Network Transmit Throughput**

* The outgoing (Transmit) network traffic on the DB instance, including both customer database traffic and Amazon RDS traffic used for monitoring and replication.
* Units: Bytes/second

![](doc/facilityDB_traffic.png)


### Database Performance Summary
**WriteIOPS**

* The average number of disk I/O operations per second. 
* Units: Count/Second

**WriteLatency**
	
* The average amount of time taken per disk I/O operation.
* Units: Seconds

![](doc/facilityDB_performance.png)


### DB Table Structure

```sh
psql (9.6.2, server 9.6.1)

facilitydb=> \d
                     List of relations
 Schema |             Name              |   Type   | Owner
--------+-------------------------------+----------+--------
 public | facility                      | table    | devops
 public | facility_details              | table    | devops
 public | facility_use                  | table    | devops
 public | facility_use_order_number_seq | sequence | devops
 public | inspection                    | table    | devops
 public | maintenance                   | table    | devops
(6 rows)

facilitydb=> \d facility

            Table "facility"
    Column     |  Type   | Modifiers
---------------+---------+-----------
 facility_id   | integer | not null
 facility_name | text    |
 room_number   | integer |
 media         | boolean |
 max_capacity  | integer |
Indexes:
    "facility_pkey" PRIMARY KEY, btree (facility_id)
Referenced by:
    TABLE "facility_details"
    TABLE "facility_use"
    TABLE "inspection"
    TABLE "maintenance"

facilitydb=> \d facility_details

        Table "facility_details"
     Column      |  Type   | Modifiers
-----------------+---------+-----------
 facility_id     | integer | not null
 phone_number    | text    |
 department      | text    |
 occupied        | boolean |
 inspection_date | date    |
Foreign-key constraints:
    "facility_details_facility_id_fkey"

facilitydb=> \d facility_use

           Table "facility_use"
      Column       |  Type   |      Modifiers
-------------------+---------+-------------------
 order_number      | integer | not null 
 facility_id       | integer | not null
 customer_id       | integer | not null
 reservation_start | date    |
 reservation_end   | date    |
 inspection_date   | date    |
Foreign-key constraints:
    "facility_use_facility_id_fkey"


facilitydb=> \d inspection

            Table "inspection"
      Column       |  Type   | Modifiers
-------------------+---------+-----------
 facility_id       | integer | not null
 inspection_code   | text    |
 inspection_date   | date    |
 passed_inspection | boolean |
 description       | text    |
Foreign-key constraints:
    "inspection_facility_id_fkey"

facilitydb=> \d maintenance

            Table "maintenance"
       Column       |  Type   | Modifiers
--------------------+---------+-----------
 facility_id        | integer | not null
 start_work         | date    |
 end_work           | date    |
 facility_downtime  | numeric |
 maintenance_cost   | integer |
 maintenance_log    | text    |
 maintenance_status | text    |
Foreign-key constraints:
    "maintenance_facility_id_fkey"

```

# Project UML Diagram

### Composite Structure
![](doc/systemDiagram.png)
