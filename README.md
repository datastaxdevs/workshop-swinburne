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

#### `✅.setup-01`- Open Gitpod

Gitpod is an IDE based on VSCode deployed in the cloud.


> ↗️ _Right Click and select open as a new Tab..._

<a href="https://gitpod.io/#https://github.com/datastaxdevs/workshop-swinburne"><img src="https://dabuttonfactory.com/button.png?t=Open+Gitpod&f=Open+Sans-Bold&ts=16&tc=fff&hp=20&vp=10&c=11&bgt=unicolored&bgc=0b5394" /></a>

#### `✅.setup-02`- Create your Astra Account: 

The Astra registration page should have opened with Gitpod, if not use [this link](https://astra.datastax.com)


#### `✅.setup-03`- Create Astra Credentials (token): Create an application token by following <a href="https://awesome-astra.github.io/docs/pages/astra/create-token/" target="_blank">these instructions</a>. 

Skip this step is you already have a token. You can reuse the same token in our other workshops, too.

> Your token should look like: `AstraCS:....`

#### `✅.setup-04`- Setup Astra CLI

Go back to your gitpod terminal waiting for your token. Make sure you select the `1_producer` shell in the bottom-right panel and provide the value where it is asked.

![pic](images/pic-astratoken.png)

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

#### `✅.setup-08`- Get the informations for your database including the keyspace list

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
> | Keyspaces              | [0] trollsquad                          |
> |                        |                                         |
> |                        |                                         |
> | Regions                | [0] us-east-1                           |
> |                        |                                         |
> +------------------------+-----------------------------------------+
> ```

*Congratulations your environment is all set, let's start the labs !*