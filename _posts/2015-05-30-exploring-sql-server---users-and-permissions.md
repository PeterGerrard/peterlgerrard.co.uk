---
layout: post
tags: SQL Server
---
Following on from my first post about [Exploring SQL Server]({% post_url 2015-05-29-exploring-sql-server %}), I would like to talk about the things I learnt about how SQL Server handles users and permissions.

#### Surely a user is a user?
You would think so, but SQL Server has two types of user:
- A Login:
  - A Login is what you can use to access the SQL Server instance and can either be your Windows account (or group) or a created User name and password.
  - Relates to what you can do to the instance (e.g. Creating databases or admin functions)
- A User:
  - A User is scoped to a database, and is optionally linked to a login.
  - Relates to what they can do to the database (e.g. view it, view data, modify the schema)

Also you also have roles and server roles.
- Each login can have multiple server roles.
- Each user can have multiple roles (the roles are also scoped to the database)
- Note that a (server) role can also have a different (server) roles in the same way users/logins can.

#### And how do i make use of users and logins?
Well you only allow certain users/logins to have certain actions. To help explain this I am going to bring in some terminology:
- Principal: A user/login/role/server role
- Securable: Something that needs protecting
- Actions: Things that can be performed on a securable (create, modify, delete being the main ones)

Just the terminology should give a hint at how you do this, for each securable you give principals the ability to perform a set of actions they can perform and a set of actions they can't perform.

#### Great, What can I secure?
Pretty much everything, from viewing data in a particular column to creating/deleting logins on the server.

Hopefully this sheds some light on how SQL Server handles Users, roles and permissions.
