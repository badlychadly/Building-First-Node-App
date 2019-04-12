# Node.js with Postgres

This past week I decided to start learning Node.js. Node is an open source JavaScript runtime that enables a developer to build out a server using JavaScript. We can build out a full stack application using all JavaScript! The great thing about Node is the ability to handle more traffic because of its non-blocking I/O. By executing the processes asynchronously the queue is freed up and we don't have to wait for heavy tasks before moving on.

### What's used
For my first app I used `Node.js` with the `Express` server framework and `Postgres` for the Database.

### Create new directory for project
Start by running mkdir 'projectName' and `cd` into new directory.

### Database
Install Postgres if you do not have already by seeing the [docs](https://www.postgresql.org). Once installed you can create the database by running `creatdb 'dbname'`. After successfully creating the database it's time to create the migrations folder, add the first migration file. Use a time stamp when naming a migration file to keep track of the order they should be ran. Run `Date.now()` in the browser console to get this quickly, or open REPL with `node` and run it there. It should look similar to this `migrate-1555098843941.sql`

Now we will create the table in the migration file.
```
DROP TABLE IF EXISTS myTable;

CREATE TABLE IF NOT EXISTS myTable (
  id BIGSERIAL PRIMARY KEY,
  name TEXT
);
```
Here we just created a table with 2 attributes(id and name). The id attribute uses the `PRIMARY KEY` to identify a row uniquely in a table. Every new instance of the table we create will have an different id that is incremented by 1.

In order to get this table into the database we need to run the file. Run the file with `psql -d dbname -f db/migrations/migrate-1555098843941.sql`. The flag `-d` specifies the database and the `-f` flag is for the file. It is helpfull to have some seed data to work with so let's do that.
Inside the db folder create a new folder called seeds, then create a file inside seeds called myTable.sql.
```
# myTable.sql

INSERT INTO quotes (name) VALUES
  ('Tom'),
  ('Mike'),
  ('Terry'),
  ('Tim'),
  ('Chris'),
  ('Arnold');
```
Run the same as before except the file name must be changed `psql -d dbname -f db/seeds/myTable.sql`

### Connect the Server and Database
TO configure the server to the route we have let Node know what data base to use, so in the db folder create a file called config.js. Add the following.
```
const options = {
  query: (e) => {
    console.log(e.query)
  }
}

const pgp = require('pg-promise')(options);

const setDataBase = () => {
  if (process.env.NODE_ENV === 'development' || !process.env.NODE_ENV){
    return pgp({
      database: 'dbname',
      port: 5432,
      host: 'localhost',
      password: 'password'
    })
  }
  else if (process.env.NODE_ENV === 'production'){
    return pgp(process.env.DATABASE_URL)
  }
}

const db = setDataBase();
module.exports = db;
```
pg-promise is a package we can use to interface for Postgres. In `setDataBase()` tell Node the dbname, port, host, and password. Then We export the `setDataBase()` function in variable called db. 

So far we have covered creating a database, the table, and some seed data. We also created a config.js file to interface to our pg database and export that functionality into other files when we need data. We still need to create the server though, and make a model to represent the db table, along with the controller! I will dive into this in my next walkthrough!
