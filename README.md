# Friday Session on PostgreSQL

Watch this space.

## Topics

### Getting started

* How to get a PostgreSQL installation to play with (Docker)
* Using `.pgpass` and `PGHOST`

### Cool Stuff

* `information_schema`
* JSON columns
* Lateral joins
* Window functions
* Underused aggregates such as array_agg
* Constructing JSON API replies in the database
* Common Table Expressions (WITH statement)

## Getting started

### How to get a PostgreSQL installation to play with (Docker)

With Docker:

    # Install PostgreSQL locally (just for the client programs, we won't start the server)
    brew install postgresql

    # Start the server in background with Docker
    docker run --rm -d -n postgres -p 5432:5432 -v postgres-data:/var/lib/postgresql postgres

    # See the section on PGHOST below.
    export PGHOST=127.0.0.1

    # Create a user and database for yourself
    createuser -U postgres $USER
    createdb -U postgres -O $USER $USER

    # Now you should be able to connect to your database this easily:
    psql

You probably want to `brew install postgresql` just for the client programs even if you intend to run the PostgreSQL server in Docker. It's also possible to use `docker run --rm -it postgres psql` to run the client via Docker but it's relatively cumbersome.

### Saving passwords with `.pgpass`

Our playground PostgreSQL does not require a password to connect. However, when using Amazon RDS etc. you always need a password or some other means of authentication.

The `.pgpass` file in your home directory lets you save passwords. See the documentation on it. TL;DR

    server:port:database:username:password

Eg.

    foodb:5432:foodb:foouser:foopassword

Use `*` to represent "any":

    foodb:*:*:foouser:foopassword

Please don't use an asterisk in the first column (server hostname).

Please be smart about where you leave `.pgpass` files lying around :)

### Setting the server with `PGHOST`

Normally you'd set the server to connect via `-h foodb`. But this is cumbersome if you do a lot of stuff with external databases (eg. Amazon RDS). You can set the server to connect to with an environment variable:

    export PGHOST=foodb

If `PGHOST` and `-h` are unset, the default is to connect via UNIX file sockets. If you used the above recipe to run a PostgreSQL server in Docker, you need to explicitly use TCP/IP to connect to your database instead:

    export PGHOST=127.0.0.1
