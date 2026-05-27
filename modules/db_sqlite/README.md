---
title: "db_sqlite Module"
description: "This is a module which provides SQLite support for OpenSIPS. It implements the DB API defined in OpenSIPS."
---

## Admin Guide


### Overview {#overview}


This is a module which provides SQLite support for OpenSIPS.
		It implements the DB API defined in OpenSIPS.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *No dependencies on other OpenSIPS modules*.


Also this module provides two ways of creating the query. One is to use
		sqlite3_bind_* functions after opensips creates the prepared statement query.
		The second one directly uses only sqlite3_snprintf function to print the
		values into the opensips created query. In theory, the second one should
		be faster and should allow you to make more queries to the database in
		the same time, so by default this one will be active. You can use the
		sqlite3_bind_* interface by simply uncommenting the SQLITE_BIND line
		the Makefile.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *libsqlite3-dev* - the development libraries of sqlite.


### Exported Parameters {#exported_parameters}


#### alloc_limit (integer) {#param_alloc_limit}


Since the library does not support a function to return the number of rows
		in a query, this number is obtained using "count(*)" query. If we use multiple
		processes there is the risk ,since "count(*)" query and the actual "select"
		query, the number of rows in the result query to have changed, so realloc
		will be needed if the number is bigger. Using *alloc_limit*
		parameter you can specify the number with which the number of allocated rows in the
		result is raised.


*Default value is 10.*


```opensips title="Set alloc_limit parameter"
...
modparam("db_sqlite", "alloc_limit", 25)
...
```


#### load_extension (string) {#param_load_extension}


This parameter enables extension loading, similiar to ".load" functionality in sqlite3,
		extenions like sqlite3-pcre which enables REGEX function. In order to use this functionality
		you must specify the library path (.so file) and the entry point which represents the function
		to be called by the sqlite library (read more at sqlite
		[load_extension](https://www.sqlite.org/capi3ref.html#sqlite3_load_extension)
		official documentation), separated by   ";"   delimiter. The entry point paramter
		can miss, so you won't need to use the delimitier in this case.


*By default, no extension is loaded.*


```opensips title="Set db_sqlite_alloc_limit parameter"
...
modparam("db_sqlite", "load_extension", "/usr/lib/sqlite3/pcre.so")
modparam("db_sqlite", "load_extension", "/usr/lib/sqlite3/pcre.so;sqlite3_extension_init")
...
```


### Exported Functions {#exported_functions}


No function exported to be used from configuration file.


### Installation


Because it dependes on an external library, the sqlite module is not
		compiled and installed by default. You can use one of the next options.


- - edit the "Makefile" and remove "db_sqlite" from "excluded_modules"
			list. Then follow the standard procedure to install OpenSIPS:
			"make all; make install".
- - from command line use: 'make all include_modules="db_sqlite";
			make install include_modules="db_sqlite"'.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | 80 | 28 | 3744 | 1276 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 15 | 13 | 105 | 40 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 15 | 12 | 53 | 62 |
| 4. | Jarrod Baumann ([@jarrodb](https://github.com/jarrodb)) | 5 | 3 | 7 | 4 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 4 | 2 | 34 | 37 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 4 | 2 | 3 | 2 |
| 7. | Aron Podrigal ([@ar45](https://github.com/ar45)) | 3 | 1 | 10 | 1 |
| 8. | Daniel Fussia | 3 | 1 | 4 | 22 |
| 9. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 3 | 1 | 2 | 2 |
| 10. | Eric Green | 3 | 1 | 1 | 10 |


**All remaining contributors**: Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)).


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | May 2016 - Apr 2026 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Apr 2019 - Jan 2025 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - Oct 2024 |
| 4. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 5. | Eric Green | Aug 2020 - Aug 2020 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 8. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | Apr 2015 - Feb 2017 |
| 9. | Daniel Fussia | Jun 2016 - Jun 2016 |
| 10. | Jarrod Baumann ([@jarrodb](https://github.com/jarrodb)) | Apr 2015 - Mar 2016 |


**All remaining contributors**: Aron Podrigal ([@ar45](https://github.com/ar45)).


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)).


*Documentation Copyrights:*


Copyright © 2015 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
