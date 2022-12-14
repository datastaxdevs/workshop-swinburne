## π Workshop NoSQL: Apache Cassandraβ’ and AstraDB

[![Discord](https://img.shields.io/discord/685554030159593522)](https://discord.com/widget?id=685554030159593522&theme=dark)


β²οΈ **Duration :** 3 hours

π **Level** Beginner to Intermediate

![](images/splash.png)

> [π Accessing HANDS-ON](#-start-hands-on)

## π Table of contents

- [LAB1 - Initialize your environment](#lab1---initialize-your-environment)
- [LAB2 - First CQL Queries](#lab2-first-cql-queries)
- [LAB3 - Data Modeling](#lab3-datamodel)
- [LAB4 - Coding](#lab4-coding-java-and-python)
<p>

# π Start Hands-on

## LAB1 - Initialize your environment

#### `β.lab1.01`- Create your Astra Account

> βοΈ _Right Click and select open as a new Tab..._
    
<a href="[https://gitpod.io/#https://github.com/datastaxdevs/workshop-swinburne](https://astra.datastax.com)"><img src="https://dabuttonfactory.com/button.png?t=Astra+Sigin+or+SignUp&f=Open+Sans-Bold&ts=16&tc=fff&hp=20&vp=10&c=11&bgt=unicolored&bgc=0b5394" /></a>

#### `β.lab1.02`- Create Astra Credentials (token)

> **Note**: Skip this step is you already have a token. You can reuse the same token in our other workshops, too.

> [Full documentation](https://awesome-astra.github.io/docs/pages/astra/create-token).
    
![](https://awesome-astra.github.io/docs/img/astra/astra-create-token.gif)
    
> Your token should look like: `AstraCS:....`

#### `β.lab1.03`- Open Gitpod IDE (VS Code in the cloud)

> βοΈ _Right Click and select open as a new Tab..._

<a href="https://gitpod.io/#https://github.com/datastaxdevs/workshop-swinburne"><img src="https://dabuttonfactory.com/button.png?t=Open+Gitpod&f=Open+Sans-Bold&ts=16&tc=fff&hp=20&vp=10&c=11&bgt=unicolored&bgc=0b5394" /></a>

#### `β.lab1.04`- Setup Astra CLI

- In Gitpod in a terminal enter the command:

```
astra setup
```

- You are asked to provide a TOKEN:

```diff
    _____            __                  
   /  _  \   _______/  |_____________    
  /  /_\  \ /  ___/\   __\_  __ \__  \  
 /    |    \\___ \  |  |  |  | \// __ \_ 
 \____|__  /____  > |__|  |__|  (____  /
         \/     \/                   \/ 
           Version: 0.1.1

 -----------------------
 ---      SETUP      ---
 -----------------------

$ Enter an Astra token:
+<ENTER_TOKEN_HERE>
```

> π₯οΈ `output`
>
> ```
> [OK]    Configuration has been saved.
> [OK]     Enter 'astra help' to list available commands.
> ```

#### `β.lab1.05`- List your existing Users.

```bash
astra user list
```

> π₯οΈ `output`
>
> ```
> +--------------------------------------+-----------------------------+---------------------+
> | User Id                              | User Email                  | Status              |
> +--------------------------------------+-----------------------------+---------------------+
> | b665658a-ae6a-4f30-a740-2342a7fb469c | cedrick.lunven@datastax.com | active              |
> +--------------------------------------+-----------------------------+---------------------+
> ```

#### `β.lab1.06`- Create database `workshops` and keyspace `sensor_data` if they do not exist:

```bash
astra db create workshops -k sensor_data --if-not-exist --wait
```

> **Note**: If the database already exist but has not been used for while the status will be `HIBERNATED`. The previous command will resume the db an create the new keyspace but it can take about a minute to execute.
    
- **Command Informations**
    
| Chunk         | Description     |
|--------------|-----------|
| `db create` | Operation executed `create` in group `db`  |
| `workshops` | Name of the database, our argument |
|`-k sensor_data` | Name of the keyspace, a db can contains multiple keyspaces |
| `--if-not-exist` | Flag for itempotency creating only what if needed |
| `--wait` | Make the command blocking until all expected operations are executed (timeout is 180s) |

> π₯οΈ `output`
>
> ```
> [INFO]  Database 'workshops' does not exist. Creating database 'workshops' with keyspace 'sensor_data'
> [INFO]  Database 'workshops' and keyspace 'sensor_data' are being created.
> [INFO]  Database 'workshops' has status 'PENDING' waiting to be 'ACTIVE' ...
> [INFO]  Database 'workshops' has status 'ACTIVE' (took 87975 millis)
> [OK]    Database 'workshops' is ready.
> ```

#### `β.lab1.07`- Get the informations for your database including the keyspace list

```bash
astra db get workshops
```

> π₯οΈ `output`
>
> ```
> +------------------------+-----------------------------------------+
> | Attribute              | Value                                   |
> +------------------------+-----------------------------------------+
> | Name                   | workshops                               |
> | id                     | bb61cfd6-2702-4b19-97b6-3b89a04c9be7    |
> | Status                 | ACTIVE                                  |
> | Default Cloud Provider | AWS                                     |
> | Default Region         | us-east-1                               |
> | Default Keyspace       | sensor_data                             |
> | Creation Time          | 2022-08-29T06:13:06Z                    |
> |                        |                                         |
> | Keyspaces              | [0] sensor_data                         |
> |                        |                                         |
> |                        |                                         |
> | Regions                | [0] us-east-1                           |
> |                        |                                         |
> +------------------------+-----------------------------------------+
> ```

## LAB2. Discovering Cassandra Query Language

#### `β.lab2.01`- Launch the initialization script
    
```
astra db cqlsh workshops -f initialize.cql
```
    
> **Note**: The command above will create a few tables for you to learn about Create, read update, delete. We will go into more details of a tables structure in the `lab3`.

#### `β.lab2.02`- Open CQL shell in interactive mode:

```
astra db cqlsh workshops -k sensor_data
```

> π₯οΈ `Output`
>
> ```
> [INFO]  Downloading Cqlshell, please wait...
> [INFO]  Installing  archive, please wait...
> [INFO]  Secure connect bundles have been downloaded.
> [INFO]  
> Cqlsh is starting, please wait for connection establishment...
> Connected to cndb at 127.0.0.1:9042.
> [cqlsh 6.8.0 | Cassandra 4.0.0.6816 | CQL spec 3.4.5 | Native protocol v4]
> Use HELP for help.
> token@cqlsh:sensor_data> 
> ```

#### `β.lab2.03`- Create (Insert) some basic data

```sql
INSERT INTO networks 
(bucket,name,description,region,num_sensors)
VALUES ('all','forest-net',
        'forest fire detection network',
        'south',3);
INSERT INTO networks 
(bucket,name,description,region,num_sensors)
VALUES ('all','volcano-net',
        'volcano monitoring network',
        'north',2);    
```

#### `β.lab2.04`- Insert collections data

> **Note**: in the following, we are using `MAP<>` which lets you define you our key/value mapping, thereby adding a bit of flexibility -- Cassandra Data models are strongly typed.

```sql
INSERT INTO sensors_by_network 
(network,sensor,latitude,longitude,characteristics)
VALUES ('forest-net','s1001',30.526503,-95.582815,
       {'accuracy':'medium','sensitivity':'high'});
INSERT INTO sensors_by_network 
(network,sensor,latitude,longitude,characteristics)
VALUES ('forest-net','s1002',30.518650,-95.583585,
       {'accuracy':'medium','sensitivity':'high'});     
INSERT INTO sensors_by_network 
(network,sensor,latitude,longitude,characteristics)
VALUES ('forest-net','s1003',30.515056,-95.556225,
       {'accuracy':'medium','sensitivity':'high'});     
INSERT INTO sensors_by_network 
(network,sensor,latitude,longitude,characteristics)
VALUES ('volcano-net','s2001',44.460321,-110.828151,
       {'accuracy':'high','sensitivity':'medium'});    
INSERT INTO sensors_by_network 
(network,sensor,latitude,longitude,characteristics)
VALUES ('volcano-net','s2002',44.463195,-110.830124,
       {'accuracy':'high','sensitivity':'medium'});     
```

#### `β.lab2.05`- Insert data in `sensors_by_network`
    
```sql
INSERT INTO sensors_by_network 
(network,sensor,latitude,longitude,characteristics)
VALUES ('forest-net','s1001',30.526503,-95.582815,
       {'accuracy':'medium','sensitivity':'high'});
INSERT INTO sensors_by_network 
(network,sensor,latitude,longitude,characteristics)
VALUES ('forest-net','s1002',30.518650,-95.583585,
       {'accuracy':'medium','sensitivity':'high'});     
INSERT INTO sensors_by_network 
(network,sensor,latitude,longitude,characteristics)
VALUES ('forest-net','s1003',30.515056,-95.556225,
       {'accuracy':'medium','sensitivity':'high'});     
INSERT INTO sensors_by_network 
(network,sensor,latitude,longitude,characteristics)
VALUES ('volcano-net','s2001',44.460321,-110.828151,
       {'accuracy':'high','sensitivity':'medium'});    
INSERT INTO sensors_by_network 
(network,sensor,latitude,longitude,characteristics)
VALUES ('volcano-net','s2002',44.463195,-110.830124,
       {'accuracy':'high','sensitivity':'medium'});    
```

#### `β.lab2.05`- Read data from network

Now that we've inserted a set of rows (two sets, to be precise), let's take a look at how to read the data back out. This is done with a **SELECT** statement. In its simplest form we could just execute a statement like the following **_**cough_** **_**cough_**:

> β οΈ _this is a first sample, select * with no WHERE is almost forbidden as will perform a full scan of your cluster_
    
```sql
SELECT * FROM networks;
```

```
 name        | description                   | region
-------------+-------------------------------+--------
  forest-net | forest fire detection network |  south
 volcano-net |    volcano monitoring network |  north
```

or

```sql
SELECT * FROM sensors_by_network;
```

π **Expected output**

```
token@cqlsh:sensor_data> SELECT * FROM sensors_by_network;

 network     | sensor | characteristics                               | latitude  | longitude
-------------+--------+-----------------------------------------------+-----------+-------------
  forest-net |  s1001 | {'accuracy': 'medium', 'sensitivity': 'high'} | 30.526503 |  -95.582815
  forest-net |  s1002 | {'accuracy': 'medium', 'sensitivity': 'high'} | 30.518650 |  -95.583585
  forest-net |  s1003 | {'accuracy': 'medium', 'sensitivity': 'high'} | 30.515056 |  -95.556225
 volcano-net |  s2001 | {'accuracy': 'high', 'sensitivity': 'medium'} | 44.460321 | -110.828151
 volcano-net |  s2002 | {'accuracy': 'high', 'sensitivity': 'medium'} | 44.463195 | -110.830124
```

You may have noticed my coughing fit a moment ago. Even though you can execute a **SELECT** statement with no partition key defined, this is NOT something you should do when using Apache Cassandra. We are doing it here for illustration purposes only and because our whole dataset is just a handful of values.

Given the data we inserted earlier, a more proper statement would be something like (while we are at it, we also explicitly specify which columns we want back):

```sql
SELECT sensor, characteristics, latitude, longitude 
FROM sensors_by_network
WHERE network = 'forest-net';
```

π **Expected output**

```
token@cqlsh:sensor_data> SELECT sensor, characteristics, latitude, longitude
               ... FROM sensors_by_network
               ... WHERE network = 'forest-net';

 sensor | characteristics                               | latitude  | longitude
--------+-----------------------------------------------+-----------+------------
  s1001 | {'accuracy': 'medium', 'sensitivity': 'high'} | 30.526503 | -95.582815
  s1002 | {'accuracy': 'medium', 'sensitivity': 'high'} | 30.518650 | -95.583585
  s1003 | {'accuracy': 'medium', 'sensitivity': 'high'} | 30.515056 | -95.556225
 ```

The key is to ensure we are **always selecting by some partition key** at a minimum, so to avoid the dreaded _full-cluster scans_ which yield performances that are generally unacceptable in production.

Ok, with that out of the way we can **READ** the data from the other table as well - remember we **INSERT**ed on both tables?

π **Commands to execute**

```sql
SELECT * FROM temperatures_by_sensor;

SELECT timestamp, value 
FROM temperatures_by_sensor
WHERE sensor='s1002' 
AND date='2020-07-05';
```

(again, in the second **SELECT** we specify some columns - it is something we may want to do in most cases).


π **Expected output**

```
token@cqlsh:sensor_data> select timestamp, value from temperatures_by_sensor where sensor='s1002' and DATE='2020-07-05';

 timestamp                       | value
---------------------------------+-------
 2020-07-05 12:59:59.000000+0000 |    99
 2020-07-05 12:00:01.000000+0000 |   100
 2020-07-05 00:59:59.000000+0000 |    82
 2020-07-05 00:00:01.000000+0000 |    82
```

Once you execute the above **SELECT** statements you should see something like the expected output above. We have now **READ** the data we **INSERTED** earlier. Awesome job!

π **Commands to execute**

```sql
SELECT * FROM temperatures_by_sensor
WHERE sensor='s1002';
```

π **Expected output**

Suprise !

## LAB3. DataModel

```sql
CREATE TABLE IF NOT EXISTS networks (
  bucket          TEXT,
  name            TEXT,
  description     TEXT,
  region          TEXT,
  num_sensors     INT,
  PRIMARY KEY ((bucket), name)
);

CREATE TABLE IF NOT EXISTS temperatures_by_network (
  network TEXT,
  week DATE,
  date_hour TIMESTAMP,
  sensor TEXT,
  avg_temperature FLOAT,
  latitude DECIMAL,
  longitude DECIMAL,
  PRIMARY KEY ((network,week),date_hour,sensor)
) WITH CLUSTERING ORDER BY (date_hour DESC, sensor ASC);

CREATE TABLE IF NOT EXISTS sensors_by_network (
  network TEXT,
  sensor TEXT,
  latitude DECIMAL,
  longitude DECIMAL,
  characteristics MAP<TEXT,TEXT>,
  PRIMARY KEY ((network),sensor)
);

CREATE TABLE IF NOT EXISTS temperatures_by_sensor (
  sensor TEXT,
  date DATE,
  timestamp TIMESTAMP,
  value FLOAT,
  PRIMARY KEY ((sensor,date),timestamp)
) WITH CLUSTERING ORDER BY (timestamp DESC);
```

### Execute a few queries

```sql
-- Q1 (note 'all' is the only partition key in this table)
SELECT  name, description, region, num_sensors
FROM    networks
WHERE   bucket = 'all';

-- Q2
SELECT  date_hour, avg_temperature, latitude, longitude, sensor 
FROM    temperatures_by_network
WHERE   network    = 'forest-net'
  AND   week       = '2020-07-05'
  AND   date_hour >= '2020-07-05'
  AND   date_hour  < '2020-07-07';

-- Q3
SELECT  *
FROM    sensors_by_network
WHERE   network = 'forest-net';

-- Q4
SELECT  timestamp, value 
FROM    temperatures_by_sensor
WHERE   sensor = 's1003'
  AND   date   = '2020-07-06';
```

To close `cqlsh` and get back to the shell prompt.

```
exit
```

## LAB4. Coding Java and Python

#### β Download the Secure Connect Bundle

Besides the "Client ID" and the "Client Secret" from the Token, the drivers also need the "Secure Connect Bundle" zipfile to work (it contains proxy and routing information as well as the necessary certificates). To download it:

```
astra db download-scb -f secure-connect-workshops.zip workshops
```

You can check it has been saved with `ls *.zip`.

#### β Configure the dot-env file

Copy the template dot-env and edit it with:

```
cp .env.sample .env ; gp open .env
```

Replace Client Secret strings from the database Token.

Finally, `source` the .env file:

```bash
source .env
```

Choose your path:

[![](https://awesome-astra.github.io/docs/img/tile-python.png)](python/Python_README.md)

[![](https://awesome-astra.github.io/docs/img/tile-java.png)](java/Java_README.md)

