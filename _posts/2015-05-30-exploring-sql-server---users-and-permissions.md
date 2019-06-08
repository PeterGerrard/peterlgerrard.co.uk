---
layout: post
author: PeterGerrard
---
Following on from my first post about <a title="Exploring SQL Server" href="https://peterlgerrard.wordpress.com/2015/05/29/exploring-sql-server/">Exploring SQL Server</a>, I would like to talk about the things I learnt about how SQL Server handles users and permissions.
<h4>Surely a user is a user?</h4>
You would think so, but SQL Server has two types of user:
<ul>
	<li>A Login:
<ul>
	<li>A LoginÂ is what you can use to access the SQL Server instance and can either be your Windows account (or group) or a created User name and password.</li>
	<li>Relates to what you can do to the instance (e.g. Creating databases or admin functions)</li>
</ul>
</li>
	<li>A User:
<ul>
	<li>A User is scoped to a database, and is optionally linked to a login.</li>
	<li>Relates to what they can do to the database (e.g. view it, view data, modify the schema)</li>
</ul>
</li>
</ul>
AlsoÂ you also have roles and server roles.
<ul>
	<li>Each login can have multiple server roles.</li>
	<li>Each user can have multiple roles (the roles are also scoped to the database)</li>
	<li>Note that a (server) role can also have a different (server) roles in the same way users/logins can.</li>
</ul>
<h4>And how do i make use of users and logins?</h4>
Well you only allow certain users/logins to have certain actions. To help explain this I am going to bring in some terminology:
<ul>
	<li>Principal: A user/login/role/server role</li>
	<li>Securable: Something that needs protecting</li>
	<li>Actions: Things that can be performed on a securable (create, modify, delete being the main ones)</li>
</ul>
Just the terminology should give a hint at how you do this, for each securable you give principals the ability to perform a set of actions they can perform and a set of actions they can't perform.
<h4>Great, What can I secure?</h4>
Pretty much everything, from viewing data in a particular column to creating/deleting logins on the server.

Hopefully this sheds some light on how SQL Server handles Users, roles and permissions.
