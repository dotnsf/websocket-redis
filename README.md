# Doodle Share Ex

## Overview

Trial application, which tries to use WebSocket with Redis so that application can be scaled-in/out, based on [Doodle Share](https://github.com/dotnsf/doodleshare).


## How to use

### As an Administrator who want to host free doodling(without passcode).

- Access to hosting URL:

  - `https://doodleshareex.yellowmix.net/view?room=XXXXXXXX`

  - `XXXXXXXX` (room parameter) have to be **unique** string, like your name and timestamp.
  
    - Ex. `https://doodleshareex.yellowmix.net/view?room=kkimura-20220608`

  - You can specify `columns` value in query string, which force to set columns number(Default: auto).
  
    - Ex. `https://doodleshareex.yellowmix.net/view?room=kkimura-20220608&columns=3`

  - Show screen's **QR code** to your guests, and ask them to enter your room with this QR code.

  - You can view semi-realtime doodling with your screen. Guest can save their doodle.

  - If you want to see doodles list in your room, you can click QR code, and they would navigate you to image list page.

### As an Administrator who want to host doodling with passcode.

- Access to hosting URL, and sign-in/up with your ID&passcode:

  - `https://doodleshareex.yellowmix.net/auth`

- You can see/create/update/delete your reserved room.

  - You will be asked your password to update room information.

- You can click one room in this page, and you will be navigated to room page.


## How to run with docker

- Run Redis image as container:

  - `$ docker run -d --name redisserver -p 6379:6379 redis`

- Run PostgreSQL image as container:

  - `$ docker run -d --name postgres -p 5432:5432 -e POSTGRES_USER=admin -e POSTGRES_PASSWORD=password -e POSTGRES_DB=mydb postgres`

- Create two tables in PostgreSQL:

  - `$ docker container exec -it postgres psql -h localhost -U admin -d mydb`

  - `postgres=# create table if not exists images ( id varchar(50) not null primary key, body bytea, contenttype varchar(50) default '', timestamp varchar(50) default '', name varchar(50) default '', comment varchar(256) default '', room varchar(256) default '', uuid varchar(100) default '', migrate_to varchar(100) default '', created bigint default 0, updated bigint default 0 );`

  - `postgres=# create table if not exists rooms ( id varchar(256) not null primary key, uuid varchar(100) default '', basic_id varchar(50) default '', basic_password varchar(50) default '', created bigint default 0, updated bigint default 0 );`

  - `postgres=# create table if not exists users ( id varchar(50) not null primary key, type int default 0, created bigint default 0, updated bigint default 0 );`

  - `postgres=# create table if not exists transactions ( id varchar(50) not null primary key, user_id varchar(50) not null, order_id varchar(50) not null, amount int default 0, currency varchar(10) default '', created bigint default 0, updated bigint default 0 );`

  - `postgres=# \q`

- Run app(s) with PORT as environment variable(Default = 8080):

  - `$ DATABASE_URL=postgres://admin:password@localhost:5432/mydb REDIS_URL=redis://localhost:6379 node app`

  - `$ DATABASE_URL=postgres://admin:password@localhost:5432/mydb REDIS_URL=redis://localhost:6379 PORT=8081 node app`

  - `$ DATABASE_URL=postgres://admin:password@localhost:5432/mydb REDIS_URL=redis://localhost:6379 PORT=8082 node app`

- Now, Administrator can open server view:

  - `http://localhost:8080/view`

    - Any running server can be OK.

  - or `http://localhost:8080/auth` to start from registering dedicated room.


- User can access to application(s) with different browsers or different tabs:

  - http://localhost:8080/

  - http://localhost:8081/

  - http://localhost:8082/

- Input your name, and Click `Start` button. Then you can draw and share your hand-drawing doodle. 

  - Confirm each message would be sent to all client


## References

https://qiita.com/rihofujino/items/7bf4b99e2176f63ca7ef

https://github.com/rihofujino/pubsub-demo


## Copyright

2021-2022 [K.Kimura @ Juge.Me](https://github.com/dotnsf) all rights reserved.

