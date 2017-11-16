---
title: Rebrewing PostgreSQL
layout: post
---

I'm currently following along with Lynda's 
"[Learning Flask](https://www.lynda.com/Flask-tutorials/Learning-Flask/521231-2.html)," which
requires one to use some Postgres.

"Cool," I thought, "I installed that a while back."

No, not cool. Things weren't working. Or I just didn't remember how to use it. Or both.

I kepth getting error messages, like
* no database directory specified and environment variable PGDATA unset
* no operation specified
* could not connect to server: No such file or directory

At one point it just seemed easier to uninstall everything postgres-related, then reinstall and
start over clean.

Only this didn't work...

Thankfully [other people](https://stackoverflow.com/questions/27700596/homebrew-postgres-broken) already had this problem!

Basically, in short, I had to do this:
```
brew uninstall postgres
rm -rf /usr/local/var/postgres/
brew install postgres
```

You will see a new a directory at `/usr/local/var/postgres/`.


### Begin the database service
If you try to run `psql` at this point, you will receive a message:
> psql: could not connect to server: No such file or directory
>	Is the server running locally and accepting
>	connections on Unix domain socket "/tmp/.s.PGSQL.5432"?

This is because you need to launch a PostgreSQL server.

```
# Option A: Have launchd start postgresql now and restart at login
brew services start postgresql
# Option B: If you don't want/need a background service
pg_ctl -D /usr/local/var/postgres start
```

### Create the username database
Still, if you try to run `psql`, you will receive another message:
> FATAL:  database "<yourUserName>" does not exist

Time to make it exist!

```
createdb `whoami`
```

Now you should be able to interactively work with Postgres. 
```
psql
```

To exit `psql`:
```
\q
```


