---
title: "db_cachedb Module"
---

## Admin Guide


### Overview {#overview}


#### The idea


The db_cachedb module will expose the same front db api, however it will run on top
				of a NoSQL back-end, emulating the SQL calls to the back-end specific queries.

				Thus, any OpenSIPS module that would regularily need a regular SQL-based database,
				will now be able to run over a NoSQL back-end, allowing for a much easier distribution
				and integration of the currently existing OpenSIPS modules in a distributed environment.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *At least one NoSQL cachedb_* module*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
	OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### cachedb_url (str)


The URL for the CacheDB back-end to be used. It can be set more than one time.


```c title="Set cachedb_url parameter"
...
modparam("db_cachedb","cachedb_url","mongodb:mycluster://127.0.0.1:27017/db.col")
...
                
```


### Examples of Usage


#### Distributed Subscriber Base


In order to achieve such a setup, one would have to  set the db_url parameter of the auth_db module to point to the DB_CACHEDB URL.


```c title="OpenSIPS CFG Snippet for using DB_CACHEDB"
loadmodule "auth_db.so"
modparam("auth_db", "load_credentials", "$avp(user_rpid)=rpid")

loadmodule "db_cachedb.so"
loadmodule "cachedb_mongodb.so"
...
modparam("db_cachedb","cachedb_url","mongodb:mycluster://127.0.0.1:27017/my_db.col")
modparam("auth_db","db_url","cachedb://mongodb:mycluster")
...
                
```


With such a setup, the auth_db module will load the subscribers from the MongoDB cluster, in the 'my_db' database, in the 'subscriber' collection.


The same mechanism/setup can be used to run other modules ( like usrloc, dialog, permissions, drouting, etc ) on top of a cachedb cluster.


### Current Limitations


#### CacheDB modules integration


Currently the only cachedb_* module that implements this functionality is the cachedb_mongodb module, so currently you can only emulate SQL queries to a MongoDB instance/cluster.

				There are plans to also extend this functionality to other cachedb_* backends, like Cassandra and CouchBase.


#### Extensive Testing Needed


Since there are many OpenSIPS modules that currently use the DB interface, it wasn't feasible to test all scenarios with all modules, and there still might be some incompatibilities.  

				The module was tested with some regularily used modules ( like usrloc, dialog, permissions, drouting ), but more testing is very much welcome, and feedback is appreciated.


#### CacheDB Specific 'schema' and other incompatibilities


Since the NoSQL backends do not usually have a strict schema involved,
				we do not provide scripts for creating such schemas, since the insertion ops will trigger the dynamically creation of the schema and info.

				Still, a specific data collection needs to be present, and that is the equivalent of the 'version' table from the SQL. Since most modules check the version table at the module setup, it's the user's responsability to setup such a 'version' collection in the respective NoSQL back-end.

				For example, for the MongoDB cluster, 'version' is a reserved keyword, so one would have to change the default version table that OpenSIPS uses ( via the 'db_version_table' global parameter ) and then manually insert the version number with something like db.my_version_table.insert({table_version : NumberInt(5), table_name : "address"})


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 10 | 8 | 34 | 35 |
| 2. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 10 | 3 | 626 | 7 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 7 | 5 | 7 | 5 |
| 4. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 3 | 4 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 4 | 2 | 3 | 2 |
| 6. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 4 | 2 | 3 | 1 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |
| 8. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Apr 2021 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - Sep 2019 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Oct 2014 - Apr 2019 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 7. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | May 2014 - May 2014 |
| 8. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Feb 2013 - Mar 2013 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Walter Doekes ([@wdoekes](https://github.com/wdoekes)), Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)).
### License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
