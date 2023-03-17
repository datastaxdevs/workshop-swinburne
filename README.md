## 🎓 Workshop NoSQL: Apache Cassandra™ and AstraDB

[![Discord](https://img.shields.io/discord/685554030159593522)](https://discord.com/widget?id=685554030159593522&theme=dark)

⏲️ **Duration :** 3h30 hours

🎓 **Level** Beginner to Intermediate

![](images/splash.png)

## 📋 Table of contents

- [LAB1 - Initialize your environment](#lab1-initializing-environment)
- [LAB2 - First CQL Queries](#lab2-discovering-cassandra-query-language)
- [LAB3 - Data Modeling](#lab3-datamodel)
- [LAB4 - Coding](#lab4-coding-java-and-python)
- [LAB5 - Stargate APIS](#lab5-stargate-apis)
<p>

## LAB1. Initializing environment

### 1.1 - Create an Astra Account

> **Note**: **Datastax Astra** is a cloud-native, fully managed database-as-a-service (DBaaS) based on Apache Cassandra. It provides a scalable, performant and highly available database solution without the operational overhead of managing Cassandra clusters. Astra supports both SQL and NoSQL APIs, and includes features like backup and restore, monitoring and alerting, and access control. It enables developers to focus on application development while the database infrastructure is managed by Datastax.

- `✅ 1.1.a` - Access [https://astra.datastax.com](https://astra.datastax.com) and register with `Google` or `Github` account

![](images/astra-login.png?raw=true")

### 1.2 - Create an Astra Token

- `✅ 1.2.a` Locate `Settings` (#1) in the menu on the left, then `Token Management` (#2)

- `✅ 1.2.b`Select the role `Organization Administrator` before clicking `[Generate Token]`

![](https://github.com/DataStax-Academy/cassandra-for-data-engineers/blob/main/images/setup-astra-2.png?raw=true)

- `✅ 1.2.c` - Copy your token in the clipboard. With this token we will now create what is needed for the training.

![](https://github.com/DataStax-Academy/cassandra-for-data-engineers/blob/main/images/setup-astra-3.png?raw=true)

### 1.3 - Open your Environment

> **Note**: **Gitpod** is a cloud-based integrated development environment (IDE) that allows developers to build, test and run applications directly in their web browser. It provides preconfigured dev environments for GitHub projects, so developers can start coding immediately without setting up local environment. Gitpod saves time and streamlines the development process.

> `✅ 1.3.a` ↗️ _Right Click and select open as a new Tab..._
>
> [![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/datastaxdevs/workshop-swinburne)

### 1.4 - Setup Astra CLI

> **Note**:  The **Astra CLI** is a command-line interface (CLI) tool for managing Apache Cassandra databases hosted on Datastax Astra. It allows developers to perform tasks like creating and managing databases, executing Cassandra queries, and managing security and access control. The Astra CLI simplifies database management and provides an alternative to the Astra web interface, enabling automation and integration with other tools and workflows.

- `✅ 1.4.a` **Locate the terminal called `setup` and check that the CLI is available**

```
astra --version
```

> 🖥️ `output`
>
> ```
> $ astra --version
> 0.2.1
> ```

- `✅ 1.4.b` **Trigger the setup command**

```
astra setup
```

> 🖥️ `output`
>
> ```
>     _____            __                  
>    /  _  \   _______/  |_____________    
>   /  /_\  \ /  ___/\   __\_  __ \__  \  
>  /    |    \\___ \  |  |  |  | \// __ \_ 
>  \____|__  /____  > |__|  |__|  (____  /
>          \/     \/                   \/ 
>            Version: 0.2.1
> 
>  -----------------------
>  ---      SETUP      ---
>  -----------------------
> 
> $ Enter an Astra token:
> ```

- `✅ 1.4.c` **Provide your token as asked (starting witg AstraCS:..)**

> 🖥️ `output`
>
> ```
> [OK]    Configuration has been saved.
> [OK]    Enter 'astra help' to list available commands.
> ```

- `✅ 1.4.d` **List your existing Users.**

```bash
astra user list
```

> 🖥️ `output`
>
> ```
> +--------------------------------------+-----------------------------+---------------------+
> | User Id                              | User Email                  | Status              |
> +--------------------------------------+-----------------------------+---------------------+
> | b665658a-ae6a-4f30-a740-2342a7fb469c | cedrick.lunven@datastax.com | active              |
> +--------------------------------------+-----------------------------+---------------------+
> ```

### 1.5 - Create database

- `✅ 1.5.a` **Create database `workshops` with keyspace `sensor_data`**

```bash
astra db create workshops -k sensor_data --if-not-exist
```

> 🖥️ `Output`
>
> ```
> [ INFO ] - Database 'workshops' already exist. Connecting to database.
> [ INFO ] - Database 'workshops' has status 'MAINTENANCE' waiting to be 'ACTIVE' ...
>[ INFO ] - Database 'workshops' has status 'ACTIVE' (took 7983 millis)
> ```

| Chunk         | Description     |
|--------------|-----------|
| `db create` | Operation executed `create` in group `db`  |
| `workshops` | Name of the database, our argument |
|`-k sensor_data` | Name of the keyspace, a db can contains multiple keyspaces |
| `--if-not-exist` | Flag for itempotency creating only what if needed |

> **Note**: If the database already exist but has not been used for while the status will be `HIBERNATED`. The previous command will resume the db an create the new keyspace but it can take about a minute to execute.
>
> ```
> [OK]    Resuming Database 'com.dtsx.astra.sdk.db.domain.Database@406005b3' ...
> [INFO]  Database 'workshops' has status 'RESUMING' waiting to be 'ACTIVE' ...
> [INFO]  Database 'workshops' has status 'ACTIVE' (took 41466 millis)
> [INFO]  Keyspace  'workshops' already exists. Connecting to keyspace.
> [OK]    Database 'workshops' is ready.
> ```

- `✅ 1.5.b` **Check the status of database `workshops`**

```bash
astra db status workshops
```

> 🖥️ `output`
>
> ```
> [ INFO ] - Database 'workshops' has status 'ACTIVE'
> ```

- `✅ 1.5.c` **Get the information for your database including the keyspace list**

```bash
astra db get workshops
```

> 🖥️ `output`
>
> ```
> +------------------------+-----------------------------------------+
> | Attribute              | Value                                   |
> +------------------------+-----------------------------------------+
> | Name                   | workshops                               |
> | id                     | 906ef2f2-07d0-472c-add6-fe719cf61d02    |
> | Status                 | ACTIVE                                  |
> | Default Cloud Provider | GCP                                     |
> | Default Region         | us-east1                                |
> | Default Keyspace       | sensor_data                             |
> | Creation Time          | 2023-02-20T12:06:21Z                    |
> |                        |                                         |
> | Keyspaces              | [0] sensor_data                         |
> |                        |                                         |
> | Regions                | [0] us-east1                            |
> +------------------------+-----------------------------------------+
> ```

[🏠 Back to Table of Contents](#-table-of-content)

## LAB2. Discovering Cassandra Query Language

#### `✅.lab2.01`- Launch the initialization script
    
```
astra db cqlsh workshops -f /workspace/workshop-swinburne/initialize.cql
```
    
> **Note**: The command above will create a few tables for you to learn about Create, read update, delete. We will go into more details of a tables structure in the `lab3`.

#### `✅.lab2.02`- Open CQL shell in interactive mode:

```
astra db cqlsh workshops -k sensor_data
```

> 🖥️ `Output`
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

#### `✅.lab2.03`- Create (Insert) some basic data

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

#### `✅.lab2.04`- Insert collections data

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

#### `✅.lab2.05`- Insert data in `sensors_by_network`
    
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

#### `✅.lab2.05`- Read data from network

Now that we've inserted a set of rows (two sets, to be precise), let's take a look at how to read the data back out. This is done with a **SELECT** statement. In its simplest form we could just execute a statement like the following **_**cough_** **_**cough_**:

> ⚠️ _this is a first sample, select * with no WHERE is almost forbidden as will perform a full scan of your cluster_
    
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

📗 **Expected output**

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

📗 **Expected output**

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

📘 **Commands to execute**

```sql
SELECT * FROM temperatures_by_sensor;

SELECT timestamp, value 
FROM temperatures_by_sensor
WHERE sensor='s1002' 
AND date='2020-07-05';
```

(again, in the second **SELECT** we specify some columns - it is something we may want to do in most cases).


📗 **Expected output**

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

📘 **Commands to execute**

```sql
SELECT * FROM temperatures_by_sensor
WHERE sensor='s1002';
```

📗 **Expected output**

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

#### ✅ Download the Secure Connect Bundle

Besides the "Client ID" and the "Client Secret" from the Token, the drivers also need the "Secure Connect Bundle" zipfile to work (it contains proxy and routing information as well as the necessary certificates). To download it:

```
astra db download-scb -f secure-connect-workshops.zip workshops
```

You can check it has been saved with `ls *.zip`.

#### ✅ Configure the dot-env file

Copy the template dot-env and edit it with:

```
cp .env.sample .env ; gp open .env
```

Replace Client Secret strings from the database Token.

Finally, `source` the .env file:

```bash
source .env
```

Choose your Language:

[![](https://awesome-astra.github.io/docs/img/tile-python.png)](python/Python_README.md)     [![](https://awesome-astra.github.io/docs/img/tile-java.png)](java/Java_README.md)

## LAB5. Stargate Apis

### 5.1 - Open Swagger

- `✅ 5.1.a` - Open Connect TAB

![image](images/open-connect-tab.png?raw=true)

- `✅ 5.1.a` - Open Swagger UI

![image](images/open-swagger-ui.png?raw=true)


### 5.2 - Rest Operations

-  ✅ `5.2.a` List keyspaces

![image](images/swagger-list-keyspace.png?raw=true)

- Click `Try it out`
- Click on `Execute`

-  ✅ `5.2.b` List Tables

![image](images/swagger-list-tables.png?raw=true)

- Click `Try it out`
- keyspace: `sensor_data`
- Click on `Execute`

-  ✅ `5.2.c` Read multiple rows

![image](images/swagger-listrows.png?raw=true)

- keyspaceName: `sensor_data`
- tableName: `users`
- Click Execute

- Notice how now you can only limited return fields

- fields: `firstname, lastname`


### 5.3 - Open Astra GraphQL

The application will use the GraphQL Api to interact with the database, let us have a look at this API.

- `✅ 5.3.a` - Locate Playground, in the `Connect` Tab by scrolling down and clicking graphQL

![](images/astra-graphql-1.png?raw=true")

- `✅ 5.3.b` - There click on the `GraphQL Playground` link.

![](images/astra-graphql-2.png?raw=true")

- `✅ 5.3.c` - List Keyspaces in db `workshop`

In the left part of the screen enter the following request and validate with the Big triangle button in the middle (play)

```yaml
query getAllKeyspaces{ 
  keyspaces {
    name 
  } 
}
```

> `Output`
> ![](images/gql-list-keyspaces.png?raw=true")
> 
### 5.4 - GraphQL Operations

- `✅ 5.4.a` - Display documentation by click on the `DOCS` panel totally on the right of the playground.

![](images/gql-documentation.png?raw=true")

- `✅ 5.4.b` - Display the table list of the keyspace `sensor_data`

```yaml
query getSensporDataKeyspaceTables {
  keyspace(name:"sensor_data") {
    name
    tables {
      name
      columns {
        name
        kind
        type {
          basic
          info {
            name
          }
        }
      }
    } 
  }
}
```

- `✅ 5.4.d` - Open second tab

In the url change `system` by `sensor_data`

- `✅ 5.4.d` - List content of table `network`,

```yaml
query getNetworks {
  networks(value:{}) {
   values {
    name
   }
  }
}
```

> `Output`
> ```yaml
> {
>  "data": {
>   "networks": {
>    "values": [
>     { "name": "forest-net" },
>     { "name": "volcano-net" }
>    ]
>   }
>  }
> }
> ```
