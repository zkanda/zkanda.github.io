+++
author = "Zakatell Kanda"
date = 2017-03-31T02:16:40Z
description = ""
draft = false
slug = "aws-rds-postgres-error-must-be-member-of-role"
title = "aws rds postgres - error:  must be member of role"

+++

I recently had to add a user for postgres in AWS RDS but it's giving me this error:

```sh
ERROR:  must be member of role "test"
```

So here's what I did:

First create a user with CREATEDB capability:

```sql
CREATE USER testuser WITH CREATEDB PASSWORD 'testpassword';
\q
```

Connect again with this new user and create the database:

```sql
CREATE DATABASE testdb OWNER testuser;
```

That's it.
