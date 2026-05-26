---
title: "db_virtual Module"
---

## Admin Guide


### Overview


#### The idea


A virtual DB will expose the same front DB api however, it will
				backed by many real DB. This means that a virtual DB URL 
				translates to many real DB URLs. This virtual layer also 
				enables us to use the real dbs in multiple ways such as: 
				parallel, failover(hotswap) and round-robin.

				Therefore:
					each virtual DB URL with associated real dbs and
					a way to use(mode) it's real dbs must be specified.


#### Modes


The implemented modes are:


- Use the first URL; if it fails, take the next
								URL and redo the operation.
- Use all the URLs in the virtual DB URL set.
								Fails if all the URLs fail.
- Use the next URL each time; if it fails, 
								use the next one, redo operation.


When choosing the db virtual mode, be sure that there is a full
			compatibility between the DB operations you want to do (inserts, 
			updates, deletes,...) and the relation (if any) between the real
			DB URLs you have in the set - can be completely independent, can be
			nodes of the same cluster, or any other combination.


#### Capabilities


For each set (or new virtual DB URL), the capabilities are
			automatically calculated based on the capabilities provided by the
			real DB URLs from the set. A logical AND is done for each
			cabability over all the URLs in the set. Shortly, in order for the
			virtual URL to provide a certain capability, ALL its real URLs 
			must provide that capability.


Note that starting with version 2.2 db_virtual supports 
			async_raw_query and async_raw_resume functions currently
			implemented only by the mysql database engine.


#### Failures


```
	When an operation from a process on a real DB fails:
		it is marked (global and local CAN flag down)
		its connection closed

	Later a timer process (probe):
	foreach virtual db_url
		foreach real db_url
			if global CAN down
				try to connect
			if ok
				global CAN up
				close connection

	Later each process:
		if local CAN down and global CAN up
			if db_max_consec_retrys *
				try to connect
		if ok
			local CAN up

				
```


Note *: there could be inconsistencies between the probe and each process so a retry limit is in order.
				It is reset and ignored by an MI command.


#### The timer process


The timer process(probe) is a process that tries to reconnect to failed dbs from time to time.
				It is a separate process so that when it blocks (for a timeout on the connection) it doesn't matter.


### Dependencies


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *At least one real DB module*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *None*.


### Exported Parameters


#### `db_urls` (str)


Multiple value parameter used for virtual DB URLs declaration.


**Example: Set
					`db_urls` parameter**


```opensips
...

modparam("group","db_url","virtual://set1")
modparam("presence|presence_xml", "db_url","virtual://set2")

modparam("db_virtual", "db_urls", "define set1 PARALLEL")
modparam("db_virtual", "db_urls", "mysql://opensips:opensipsrw@localhost/testa")
modparam("db_virtual", "db_urls", "postgres://opensips:opensipsrw@localhost/opensips")

modparam("db_virtual", "db_urls", "define set2 FAILOVER")
modparam("db_virtual", "db_urls", "mysql://opensips:opensipsrw@localhost/testa")
...
				
```


#### `db_probe_time` (integer)


Time interval after which a registered timer process attempts to check
		failed(as reported by other processes) connections to real dbs. The probe will connect and
		disconnect to the failed real DB and announce others.


*Default value is 10 (10 sec).*


**Example: Set
					`db_probe_time` parameter**


```opensips
...
modparam("db_virtual", "db_probe_time", 20)
...
				
```


#### `db_max_consec_retrys` (integer)


After the timer process has reported that it can connect to the real db,
		other processes will try to reconnect to it. There are cases where although
		the probe could connect some might fail. This parameter represents the number
		of consecutive failed retries that a process will do before it gives up.
		This value is reset and suppressed by a MI function(db_set).


*Default value is 10 (10 consecutive times).*


**Example: Set
					`db_max_consec_retrys` parameter**


```opensips
...
modparam("db_virtual", "db_max_consec_retrys", 20)
...

				
```


### Exported MI Functions


#### `db_get`


Return information about global state of the real dbs.


Name:
				*db_get*


Parameters:


- None.


MI FIFO Command Format:


```
				opensips-cli -x mi db_get
			
```


#### `db_set`


Sets the permissions for real dbs access per set per db.


Sets the reconnect reset flag.


Name:
				*db_set*


Parameters:


- 
- 
- 
- 


db_set 3 2 0 1 means:


- 
- 
- 
- 


MI FIFO Command Format:


```
				opensips-cli -x mi db_set 3 2 0 1
			
```


## Contributors


### By Commit Statistics


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Razvan Pistolea | 31 | 7 | 2244 | 297 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 22 | 18 | 104 | 128 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 16 | 12 | 105 | 140 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 14 | 12 | 21 | 19 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 9 | 5 | 85 | 145 |
| 6. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | 6 | 3 | 232 | 9 |
| 7. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 4 | 5 |
| 8. | Zero King ([@l2dy](https://github.com/l2dy)) | 3 | 1 | 5 | 5 |
| 9. | Anca Vamanu | 3 | 1 | 3 | 3 |
| 10. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | 3 | 1 | 2 | 2 |


**All remaining contributors**: Julián Moreno Patiño, Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)).


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Aug 2009 - May 2022 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Sep 2011 - Jan 2021 |
| 4. | Zero King ([@l2dy](https://github.com/l2dy)) | Mar 2020 - Mar 2020 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 7. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Oct 2013 - Jun 2018 |
| 8. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | Feb 2016 - Mar 2017 |
| 9. | Julián Moreno Patiño | Feb 2016 - Feb 2016 |
| 10. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | Jun 2014 - Jun 2014 |


**All remaining contributors**: Anca Vamanu, Razvan Pistolea.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation


### Contributors


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Julián Moreno Patiño, Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)), Razvan Pistolea.


*Documentation Copyrights:*


Copyright © 2009 Voice Sistem SRL
