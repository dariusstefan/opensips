---
title: "Berkeley DB Module"
description: "This is a module which integrates the Berkeley DB into OpenSIPS. It implements the DB API defined in OpenSIPS."
---

## Admin Guide


### Overview {#overview}


This is a module which integrates the Berkeley DB into OpenSIPS.
		It implements the DB API defined in OpenSIPS.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *No dependencies on other OpenSIPS modules*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *Berkeley Berkeley DB 4.6* - an embedded database.


### Exported Parameters {#exported_parameters}


#### auto_reload (integer) {#param_auto_reload}


The auto-reload will close and reopen a Berkeley DB when the
		files inode has changed. The operation occurs only duing a query. 
		Other operations such as insert or delete, do not invoke auto_reload.


*Default value is 0 (1 - on / 0 - off).*


**Example: Set auto_reload parameter**


```opensips
...
modparam("db_berkeley", "auto_reload", 1)
...
		
```


#### log_enable (integer) {#param_log_enable}


The log_enable boolean controls when to create journal files.
		The following operations can be journaled: 
		INSERT, UPDATE, DELETE. Other operations such as SELECT, do not. 
		This journaling are required if you need to recover from a corrupt 
		DB file. That is, bdb_recover requires these to rebuild 
		the db file. If you find this log feature useful, you may 
		also be interested in the METADATA_LOGFLAGS bitfield that each 
		table has. It will allow you to control which operations to 
		journal, and the destination (like syslog, stdout, local-file). 
		Refer to bdblib_log()  and documentation on METADATA.


*Default value is 0 (1 - on / 0 - off).*


**Example: Set log_enable parameter**


```opensips
...
modparam("db_berkeley", "log_enable", 1)
...
		
```


#### journal_roll_interval (integer seconds) {#param_journal_roll_interval}


The journal_roll_interval will close and open a new log file. 
		The roll operation occurs only at the end of writing a log, 
		so it is not guaranteed to to roll 'on time'.


*Default value is 0 (off).*


**Example: Set journal_roll_interval parameter**


```opensips
...
modparam("db_berkeley", "journal_roll_interval", 3600)
...
		
```


### Exported Functions {#exported_functions}


No function exported to be used from configuration file.


### Exported MI Functions {#exported_mi_functions}


#### bdb_reload {#mi_bdb_reload}


Causes db_berkeley module to re-read the contents of specified table (or dbenv).
		The db_berkeley DB actually loads each table on demand, as opposed to loading all
		at mod_init time. The bdb_reload operation is implemented as a close followed by a reopen.
		Note- bdb_reload will fail if a table has not been accessed before (because the close 
		will fail).


Name: *bdb_reload*


Parameters:


- *table_path* - to reload a particular table
				provide the tablename as the arguement; to reload
				all tables provide the db_path to the db files. The path can be found
				in opensipsc-cli config variable.


MI FIFO Command Format:


```
		opensips-cli -x mi bdb_reload subscriber
		
```


### Installation and Running


First download, compile and install the Berkeley DB. This is 
		outside the scope of this document. Documentation for this 
		procedure is available on the Internet.


Next, prepare to compile OpenSIPS with the db_berkeley module. 
		In the directory /modules/db_berkeley, modify the Makefile to point 
		to your distribution of Berkeley DB. You may also define 'BDB_EXTRA_DEBUG' 
		to compile in extra debug logs. However, it is not a recommended 
		deployment to production servers.


Because the module dependes on an external library, the db_berkeley module is not
		compiled and installed by default. You can use one of the next options.


- edit the "Makefile" and remove "db_berkeley" from "excluded_modules"
			list. Then follow the standard procedure to install OpenSIPS:
			"make all; make install".
- from command line use: 'make all include_modules="db_berkeley";
			make install include_modules="db_berkeley"'.


Installation of OpenSIPS is performed by simply running make install
		as root user of the main directory. This will install the binaries 
		in /usr/local/sbin/.
		If this was successful, OpenSIPS control engine files should now 
		be installed as /usr/local/sbin/opensipsdbctl.


Decide where (on the filesystem) you want to install the Berkeley DB files.
		For instance, '/usr/local/etc/opensips/db_berkeley' directory.
		Make note of this directory as we need to add this path to the opensips-cli config file.
		Note: OpenSIPS will not startup without these DB files.


(Optional) Pre creation step- Customize your meta-data.
		The DB files are initially seeded with necessary meta-data. 
		This is a good time to review the meta-data section details,
		before making modifications to your tables dbschema.
		By default, the files are installed in '/usr/local/share/opensips/db_berkeley/opensips'
		By default these tables are created Read/Write and without any journalling as 
		shown. These settings can be modified on a per table basis.
		Note: If you plan to use bdb_recover, you must change the LOGFLAGS.


```
		METADATA_READONLY
		0
		METADATA_LOGFLAGS
		0
		
```


Execute opensipsdbctl - There are three (3) groups of tables you may need depending
		on your situation.


```
		opensipsdbctl create   		(required)
		opensipsdbctl presence 		(optional)
		opensipsdbctl extra    		(optional)
		
```


Modify the OpenSIPS configuration file to use db_berkeley module. 
		The database URL for modules must be the path to the directory where 
		the Berkeley DB table-files are located, prefixed by "berkeley://", 
		e.g., "berkeley:///usr/local/etc/opensips/db_berkeley".


A couple other IMPORTANT things to consider are the 'db_mode' and the 'use_domain' 
		modparams. The description of these parameters are found in usrloc documentation.


Note on db_mode- 
		The db_berkeley module will only journal the moment usrloc writes back
		to the DB. The safest mode is mode 3 , since the db_berkeley journal files will always
		be up-to-date. The main point is the db_mode vs. recovery by journal file interaction.
		
		Writing journal entries is 'best effort'. So if the hard drive becomes full, the
		attempt to write a journal entry may fail.


Note on use_domain-
		The db_berkeley module will attempt natural joins when performing a query.
		This is basically a lexigraphical string compare using the keys provided.
		In most places in the db_berkeley dbschema (unless you customize), the domainname 
		is identified as a natural key. 
		Consider an example where use_domain = 0. In table subscriber, the db will be keying on 
		'username|NULL' because the default value will be used when that key column is not provided.
		This effectivly means that later queries must consistently use the username (w.o domain)
		in order to find a result to that particular subscriber query.
		The main point is 'use_domain' can not be changed once the db_berkeley is setup.


### Database Schema and Metadata


All Berkeley DB tables are created via the opensipsdbctl script. 
	This section provides details as to the content and 
	format of the DB file upon creation.


Since the Berkeley DB stores key value pairs, the database is seeded 
	with a few meta-data rows . The keys to these rows must begin with 'METADATA'. 
	Here is an example of table meta-data, taken from the table 'version'.


Note on reserved character- 
	The '|' pipe character is used as a record delimiter within the 
	Berkeley DB implementation and must not be present in any DB field.


**Example: METADATA_COLUMNS**


```
METADATA_COLUMNS
table_name(str) table_version(int)
METADATA_KEY
0
	
```


In the above example, the row METADATA_COLUMNS defines the column names 
	and type, and the row METADATA_KEY defines which column(s) form the key. 
	Here the value of 0 indicates that column 0 is the key(ie table_name). 
	With respect to column types, the db_berkeley modules only has the following 
	types: string, str, int, double, and datetime. The default type is string, 
	and is used when one of the others is not specified. The columns of the 
	meta-data are delimited by whitespace.


The actual column data is stored as a string value, and delimited by 
	the '|' pipe character. Since the code tokenizes on this delimiter, 
	it is important that this character not appear in any valid data field. 
	The following is the output of the 'db_berkeley.sh dump version' command. 
	It shows contents of table 'version' in plain text.


**Example: contents of version table**


```
VERSION=3
format=print
type=hash
h_nelem=21
db_pagesize=4096
HEADER=END
 METADATA_READONLY
 1
 address|
 address|3
 aliases|
 aliases|1004
 dbaliases|
 dbaliases|1
 domain|
 domain|1
 speed_dial|
 speed_dial|2
 subscriber|
 subscriber|6
 uri|
 uri|1
 METADATA_COLUMNS
 table_name(str) table_version(int)
 METADATA_KEY
 0
 acc|
 acc|4
 grp|
 grp|2
 location|
 location|1004
 missed_calls|
 missed_calls|3
 re_grp|
 re_grp|1
 silo|
 silo|5
 trusted|
 trusted|4
 usr_preferences|
 usr_preferences|2
DATA=END
	
```


### METADATA_COLUMNS (required)


The METADATA_COLUMNS row contains the column names and types. 
	Each is space delimited. Here is an example of the data taken from table subscriber :


**Example: METADATA_COLUMNS**


```
METADATA_COLUMNS
username(str) domain(str) password(str) ha1(str) ha1b(str) first_name(str) last_name(str) email_address(str) datetime_created(datetime) timezone(str) rpid(str)
 	
```


Related (hardcoded) limitations:


- maximum of 32 columns per table.
- maximum tablename size is 64.
- maximum data length is 2048


Currently supporting these five types: str, datetime, int, double, string.


### METADATA_KEYS (required)


The METADATA_KEYS row indicates the indexes of the key columns, 
	with respect to the order specified in METADATA_COLUMNS. 
	Here is an example taken from table subscriber that brings up a good point:


**Example: METADATA_KEYS**


```
 METADATA_KEY
 0 1
 	
```


The point is that both the username and domain name are require 
	as the key to this record. Thus, usrloc modparam 
	use_domain = 1 must be set for this to work.


### METADATA_READONLY (optional)


The METADATA_READONLY row contains a boolean 0 or 1. 
	By default, its value is 0. On startup the DB will 
	open initially as read-write (loads metadata) and then if this 
	is set=1, it will close and reopen as read only (ro). 
	I found this useful because readonly has impacts on the 
	internal db locking etc.


### METADATA_LOGFLAGS (optional)


The METADATA_LOGFLAGS row contains a bitfield that customizes the 
	journaling on a per table basis. If not present the default value 
	is taken as 0. Here are the masks so far (taken from bdb_lib.h):


**Example: METADATA_LOGFLAGS**


```
#define JLOG_NONE 0
#define JLOG_INSERT 1
#define JLOG_DELETE 2
#define JLOG_UPDATE 4
#define JLOG_STDOUT 8
#define JLOG_SYSLOG 16
	
```


This means that if you want to journal INSERTS to local file and syslog the value 
	should be set to 1+16=17. Or if you do not want to journal at all, set this to 0.


### DB Recovery : bdb_recover


The db_berkeley module uses the Concurrent Data Store (CDS) architecture. 
	As such, no transaction or journaling is provided by the DB natively. 
	The application bdb_recover is specifically written to recover data from 
	journal files that OpenSIPS creates.  
	The bdb_recover application requires an additional text file that contains 
	the table schema.


The schema is loaded with the '-s' option and is required for all operations.
	Provide the path to the db_berkeley plain-text schema files. By default, these
	install to '/usr/local/share/opensips/db_berkeley/opensips/'.


The '-h' home option is the DB_PATH path. Unlike the Berkeley utilities, 
	this application does not look for the DB_PATH environment variable, 
	so you have to specify it. If not specified, it will assume the current 
	working directory. The last argument is the operation. 
	There are fundamentally only two operations- create and recover.


The following illustrates the four operations available to the administrator.


**Example: bdb_recover usage**


```
usage: ./bdb_recover -s schemadir [-h home] [-c tablename]
	This will create a brand new DB file with metadata.

usage: ./bdb_recover -s schemadir [-h home] [-C all]
	This will create all the core tables, each with metadata.

usage: ./bdb_recover -s schemadir [-h home] [-r journal-file]
	This will rebuild a DB and populate it with operation from journal-file. 
	The table name is embedded in the journal-file name by convention.

usage: ./bdb_recover -s schemadir [-h home] [-R lastN]
	This will iterate over all core tables enumerated. If journal files exist in 'home', 
	a new DB file will be created and populated with the data found in the last N files. 
	The files are 'replayed' in chronological order (oldest to newest). This 
	allows the administrator to rebuild the db with a subset of all possible 
	operations if needed. For example, you may only be interested in 
	the last hours data in table location.
	
```


Important note- A corrupted DB file must be moved out of the way before bdb_recover is executed.


### Known Limitations


The Berkeley DB does not nativly support an autoincrement (or sequence) mechanism.
	Consequently, this version does not support surragate keys in dbschema. These
	are the id columns in the tables.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | William Quan | 40 | 1 | 4682 | 0 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 28 | 23 | 153 | 154 |
| 3. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 26 | 10 | 381 | 688 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 24 | 19 | 149 | 170 |
| 5. | Anonymous | 16 | 4 | 681 | 341 |
| 6. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 13 | 10 | 19 | 79 |
| 7. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 10 | 8 | 47 | 30 |
| 8. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 6 | 4 | 34 | 28 |
| 9. | Andrei Dragus | 5 | 1 | 135 | 103 |
| 10. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 5 | 5 |


**All remaining contributors**: Jan Janak ([@janakj](https://github.com/janakj)), Konstantin Bokarius, Razvan Pistolea, Walter Doekes ([@wdoekes](https://github.com/wdoekes)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Edson Gellert Schubert, Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)).


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - May 2024 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Sep 2011 - Jul 2020 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jul 2008 - Apr 2019 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 7. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | May 2014 - May 2014 |
| 8. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Oct 2010 - Oct 2010 |
| 9. | Andrei Dragus | Oct 2009 - Oct 2009 |
| 10. | Razvan Pistolea | Jul 2009 - Jul 2009 |


**All remaining contributors**: Jan Janak ([@janakj](https://github.com/janakj)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Henning Westerholt ([@henningw](https://github.com/henningw)), Konstantin Bokarius, Edson Gellert Schubert, Anonymous, William Quan.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Henning Westerholt ([@henningw](https://github.com/henningw)), Anonymous, William Quan.


*Documentation Copyrights:*


Copyright © 2007 Cisco Systems
