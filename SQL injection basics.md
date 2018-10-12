# SQL injection basics

**Needed knowledge:**
- basic understanding of what is sql and simple syntax.
- simple php.
- common sense.
- a computer..?

**Notes:**
- this artical is based on sqlite.

`SQL injection` is probably my favourite web vulnerability, maybe after `PHP object injection`, but that's a complete another article.

So, before we dive into the famous `SQL injection` (aka *SQLi*) we need to know what SQL actually is, right? of course.

```
SQL is a domain-specific language used in programming and designed for managing data held in a relational database management system (RDBMS), or for stream processing in a relational data stream management system (RDSMS).
It is particularly useful in handling structured data where there are relations between different entities/variables of the data.
```
taken from the [SQL page](https://en.wikipedia.org/wiki/SQL) in wikipedia.

in other (and more simple, no offense to wikipedia tho) words, sql is a language used to communicate with table based databases. for more information about sql I suggest visiting [www.sqlitetutorial.net](http://www.sqlitetutorial.net/). (about sqlite, which is a specific sql based database). Enough sql for now, it's time for the real thing.


Sql Injection is server-side vulnerability where the attacker inject sql (user controled input) to dump data from the database,
and even sometimes used to get _RCE_ on the server.

So let's take a look at a simple sqlite query: ($input is the user input)
`SELECT * FROM users WHERE password = '$input'`
This sql query will return all data about a username with a password given by the user. (i'm pretty bad in explaining sql queries)

### **_It's exploiting time!_**
how can we exploit this simple sql query? well that's simple.
let's say that this query is what loggin you into the website, consider the following php code:
```
<?php
$res = doQuery("SELECT * FROM users WHERE password = '$input'");
if (!empty($res)) {
  login();
}
else {
  die();
}
```
We would want to make the statement `WHERE password = '$input'` return True and then we cant get a row from the query and login.
So if we insert this input: `or 1=1` then the statement will return True, right? well, no.
the query would look like this `SELECT * FROM users WHERE password = 'or 1=1'` and the WHERE caluse will search for a username with the password `or 1=1`.
so the _payload_(input) for this would be `' or 'x'='x`, and the query would look like this: 
`'SELECT * FROM users WHERE password = '' or 'x'='x'`
that would make the statement always True, the first quote is to close the first clause in the original statement and the second clause is used to close the second clause in the original code.

### Artical payloads:
`' or 'x'='x` to bypass `SELECT * FROM users WHERE password = '$input'`.

### Buzzwords:
- payload: payload is the actual data in the transmitted message.
- RCE: stands for `Remote Code Execution`.
