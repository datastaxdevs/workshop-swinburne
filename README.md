## 🎓 Workshop NoSQL: Apache Cassandra™ and AstraDB

[![Discord](https://img.shields.io/discord/685554030159593522)](https://discord.com/widget?id=685554030159593522&theme=dark)


⏲️ **Duration :** 3 hours

🎓 **Level** Beginner to Intermediate


![](images/splash.png)

> [🔖 Accessing HANDS-ON](#-start-hands-on)

## 📋 Table of contents

- **Objectives**
  - [Objectives](#objectives)
  - [Frequently asked questions](#frequently-asked-questions)
  - [Materials for the Session](#materials-for-the-session)
- **Architecture Design**
  - [Architecture overview](#architecture-overview)
  - [Injector Component](#injector-component)
  - [Analyzer Component](#analyzer-component)
- [Setup - Initialize your environment](#setup---initialize-your-environment)
- [LAB1 - Producer and Consumer](#lab1---producer-and-consumer)
- [LAB2 - Pulsar functions](#lab2---pulsar-functions)
- [LAB3 - Working with Database](#lab3---working-with-databases)
- [LAB4 - Pulsar I/O](#lab4---pulsar-io)
- [Homework](#Homework)
<p>

# 🏁 Start Hands-on

## Setup - Initialize your environment

#### `✅.setup-01`- Create your Astra Account: 

The Astra registration page should have opened with Gitpod, if not use [this link](https://astra.datastax.com)


#### `✅.setup-02`- Create Astra Credentials (token): Create an application token by following <a href="https://awesome-astra.github.io/docs/pages/astra/create-token/" target="_blank">these instructions</a>. 

Skip this step is you already have a token. You can reuse the same token in our other workshops, too.

> Your token should look like: `AstraCS:....`

#### `✅.setup-03`- Open Gitpod

Gitpod is an IDE based on VSCode deployed in the cloud.

> ↗️ _Right Click and select open as a new Tab..._

<a href="https://gitpod.io/#https://github.com/datastaxdevs/workshop-swinburne"><img src="https://dabuttonfactory.com/button.png?t=Open+Gitpod&f=Open+Sans-Bold&ts=16&tc=fff&hp=20&vp=10&c=11&bgt=unicolored&bgc=0b5394" /></a>

#### `✅.setup-04`- Setup Astra CLI

When the gitpod open you are asked to provide a TOKEN:

> 🖥️ `setup-04 output`
>
> ```
> [cedrick.lunven@gmail.com]
> ASTRA_DB_APPLICATION_TOKEN=AstraCS:AAAAAAAA
> 
> [What's NEXT ?]
> You are all set.(configuration is stored in ~/.astrarc) You can now:
>    • Use any command, 'astra help' will get you the list
>    • Try with 'astra db list'
>    • Enter interactive mode using 'astra'
> 
> Happy Coding !
> ```

#### `✅.setup-05`- List your existing Users.

```bash
astra user list
```

> 🖥️ `setup-05 output`
>
> ```
> +--------------------------------------+-----------------------------+---------------------+
> | User Id                              | User Email                  | Status              |
> +--------------------------------------+-----------------------------+---------------------+
> | b665658a-ae6a-4f30-a740-2342a7fb469c | cedrick.lunven@datastax.com | active              |
> +--------------------------------------+-----------------------------+---------------------+
> ```

#### `✅.setup-06`- Create database `workshops` and keyspace `trollsquad` if they do not exist:

```bash
astra db create workshops -k sensor_data --if-not-exist --wait
```

Let's analyze the command:
| Chunk         | Description     |
|--------------|-----------|
| `db create` | Operation executed `create` in group `db`  |
| `workshops` | Name of the database, our argument |
|`-k sensor_data` | Name of the keyspace, a db can contains multiple keyspaces |
| `--if-not-exist` | Flag for itempotency creating only what if needed |
| `--wait` | Make the command blocking until all expected operations are executed (timeout is 180s) |

> **Note**: If the database already exist but has not been used for while the status will be `HIBERNATED`. The previous command will resume the db an create the new keyspace but it can take about a minute to execute.

> 🖥️ `setup-06 output`
>
> ```
> [ INFO ] - Database 'workshops' already exist. Connecting to database.
> [ INFO ] - Database 'workshops' has status 'MAINTENANCE' waiting to be 'ACTIVE' ...
>[ INFO ] - Database 'workshops' has status 'ACTIVE' (took 7983 millis)
> ```

#### `✅.setup-07`- Get the informations for your database including the keyspace list

```bash
astra db get workshops
```

> 🖥️ `setup-08 output`
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

*Congratulations your environment is all set, let's start the labs !*

## LAB1. Setup your Astra DB instance

#### ✅ 5a) Start the CQL shell and connect to database `workshops` and keyspace `sensor_data`:

```
astra db cqlsh workshops -k sensor_data
```

> 🖥️ Output
>
> ```
> [ INFO ] - Cqlsh has been installed
> 
> Cqlsh is starting please wait for connection establishment...
> Connected to cndb at 127.0.0.1:9042.
> [cqlsh 6.8.0 | Cassandra 4.0.0.6816 | CQL spec 3.4.5 | Native protocol v4]
> Use HELP for help.
> token@cqlsh:sensor_data> 
> ```

#### ✅ 5b) Initialize the Schema with `cqlsh`

We want to initialize the following schema


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

#### Launch the initialization script

```
astra db cqlsh workshops -f initialize.cql
```

Now you can copy-paste any of the queries below and execute them with the `Enter` key:

```
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

To close `cqlsh` and get back to the shell prompt, execute the `EXIT` command.

</details>

#### Download the Secure Connect Bundle

Besides the "Client ID" and the "Client Secret" from the Token, the drivers
also need the "Secure Connect Bundle" zipfile to work (it contains proxy
and routing information as well as the necessary certificates).

To download it:

```
astra db download-scb -f secure-connect-workshops.zip workshops
```

You can check it has been saved with `ls *.zip`.
