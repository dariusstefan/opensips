---
title: "unixodbc Module"
description: "This module allows to use the unixodbc package with OpenSIPS. It have been tested with mysql and the odbc connector, but it should work also with other database. The auth_db module works."
---

## Admin Guide


### Overview {#overview}


This module allows to use the unixodbc package with OpenSIPS. It have been 
	tested with mysql and the odbc connector, but it should work also with 
	other database. The auth_db module works.


For more information, see the [http://www.unixodbc.org/](http://www.unixodbc.org/) project web page.


To see what DB engines can be used via unixodbc, look at 
	[http://www.unixodbc.org/drivers.html](http://www.unixodbc.org/drivers.html).


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *No dependencies on other OpenSIPS modules*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### auto_reconnect (int) {#param_auto_reconnect}


Turns on or off the auto_reconnect mode.


*Default value is "1", this means it is enabled.*


```c title="Set the 'auto_reconnect' parameter"
...
modparam("db_unixodbc", "auto_reconnect", 0)
...
```


#### use_escape_common (int) {#param_use_escape_common}


Escape values in query using internal escape_common() function.
		It escapes single quote ''', double quote '"', backslash '\',
		and NULL characters.


You should enable this parameter if you know that the ODBC driver
		considers the above characters as special (for marking begin and end
		of a value, escape other characters ...). It prevents against SQL
		injection.


*Default value is "0" (0 = disabled; 1 = enabled).*


```c title="Set the 'use_escape_common' parameter"
...
modparam("db_unixodbc", "use_escape_common", 1)
...
```


### Exported Functions {#exported_functions}


NONE


### Installation and Running


#### Installing


Prerequirement: you should first install unixodbc (or another program that 
	implements the odbc standard, such iodbc), your database, and the right 
	connector. Set the DSN in the odbc.ini file and the connector drivers in 
	the odbcinst.ini file.


#### Configuring and Running


In the opensips.conf file, add the line:


```c
....
loadmodule "/usr/local/lib/opensips/modules/db_unixodbc.so"
....
```


You should also uncomment this:


```c
....
loadmodule "/usr/local/lib/opensips/modules/auth.so"
loadmodule "/usr/local/lib/opensips/modules/auth_db.so"
modparam("usrloc", "db_mode", 2)
modparam("auth_db", "calculate_ha1", yes)
modparam("auth_db", "password_column", "password")
....
```


and setting the DSN specified in the odbc.ini, inserting this with the 
	url adding this line:


```c
....
modparam("usrloc|auth_db", "db_url", 
    "unixodbc://opensips:opensipsrw@localhost/my_dsn")
....
```


replacing my_dsn with the correct value.


HINT: if unixodbc don't want to connect to mysql server, try restarting 
	mysql server with:


```c
shell>safe_mysqld --user=mysql --socket=/var/lib/mysql/mysql.sock
```


The connector search the socket in /var/lib/mysql/mysql.sock and not 
	in /tmp/mysql.sock


## Developer Guide


The module implements the OpenSIPS DB API, in order to 
	be used by other modules.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 45 | 22 | 385 | 1129 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 44 | 31 | 617 | 418 |
| 3. | Marco Lorrai | 24 | 1 | 2660 | 0 |
| 4. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 20 | 15 | 221 | 140 |
| 5. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 19 | 11 | 136 | 315 |
| 6. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 11 | 9 | 64 | 26 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 4 | 2 | 8 | 7 |
| 8. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 4 | 4 |
| 9. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 4 | 2 | 3 | 3 |
| 10. | Anca Vamanu | 4 | 1 | 89 | 103 |


**All remaining contributors**: Elena-Ramona Modroiu, Anonymous, Konstantin Bokarius, Alex Massover, Razvan Pistolea, Norman Brandinger ([@NormB](https://github.com/NormB)), Edson Gellert Schubert, Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Carsten Bock.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Oct 2011 - Jul 2025 |
| 2. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Feb 2025 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - May 2024 |
| 4. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Dec 2005 - Apr 2019 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 7. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Jul 2011 - Jul 2011 |
| 8. | Razvan Pistolea | Jul 2009 - Jul 2009 |
| 9. | Alex Massover | Mar 2009 - Mar 2009 |
| 10. | Carsten Bock | May 2008 - May 2008 |


**All remaining contributors**: Henning Westerholt ([@henningw](https://github.com/henningw)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Anca Vamanu, Elena-Ramona Modroiu, Norman Brandinger ([@NormB](https://github.com/NormB)), Anonymous, Marco Lorrai.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Henning Westerholt ([@henningw](https://github.com/henningw)), Elena-Ramona Modroiu, Marco Lorrai.
### License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
