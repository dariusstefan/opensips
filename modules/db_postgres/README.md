---
title: "db_postgres Module"
description: "Module description"
---

## Admin Guide


### Overview


Module description


### Dependencies


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *No dependencies on other OpenSIPS modules*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *PostgreSQL library* - e.g., libpq5.
- *PostgreSQL devel library* - to compile
				the module (e.g., libpq-dev).


### Exported Parameters


#### exec_query_threshold (integer)


If queries take longer than 'exec_query_threshold' microseconds, warning
		messages will be written to logging facility.


*Default value is 0 - disabled.*


**Example: Set exec_query_threshold parameter**


```opensips
...
modparam("db_postgres", "exec_query_threshold", 60000)
...
```


#### max_db_queries (integer)


The maximum number of database queries to be executed. 
                If this parameter is set improperly, it is set to default value.


*Default value is 2.*


**Example: Set max_db_queries parameter**


```opensips
...
modparam("db_postgres", "max_db_queries", 2)
...
```


#### timeout (integer)


The number of seconds the PostgreSQL library waits to connect and query
			the server. If the connection does not succeed within the given timeout,
			the connection fails.


*Note:*If the timeout is a negative value and
			connection does not succeed, OpenSIPS will block until the connection
			becomes back available and gets successfully established. This is the
			default behavior of the library and is the behavior prior to the
			adition of this parameter.


*Default value is 5.*


**Example: Set timeout parameter**


```opensips
...
modparam("db_postgres", "timeout", 2)
...
```


#### use_tls (integer)


Parameter to control the way the SSL support is used when connecting
		to the Postgres server, as follows:


- *use_tls=0* (default) - the SSL support
				is disabled and there is no attempt to use it;
- *use_tls=1* with "tls_domain" present
				in the DB URL - the SSL support is enabled, either 
				"require", either "verify-ca", depending on the certificate
				settings;
- *use_tls=1* with no "tls_domain" present
				in the DB URL - the SSL support is enabled in best effort mode
				(or "prefer"); if supported by the server, it will be used,
				otherwise it will fall back to non-SSL.


Warning: the *tls_openssl* module cannot be used
		when setting this parameter. Use the *tls_wolfssl*
		module instead if a TLS/SSL Library is required.


Setting this parameter will allow you to use TLS for PostgreSQL connections.
		In order to enable TLS for a specific connection, you can use the
		"tls_domain=*dom_name*" URL parameter in the db_url of
		the respective OpenSIPS module. This should be placed at the end of the
		URL after the '?' character.


When using this parameter, you must also ensure that
		*tls_mgm* is loaded and properly configured. Refer to
		the the module for additional info regarding TLS client domains.


Note that if you want to use this feature, the TLS domain must be
		provisioned in the configuration file, *NOT* in
		the database. In case you are loading TLS certificates from the
		database, you must at least define one domain in the
		configuration script, to use for the initial connection to the DB.


Also, you can *NOT* enable TLS for the connection
		to the database of the *tls_mgm* module itself.


*Default value is **0** (not enabled)*


**Example: Set the use_tls parameter**


```opensips
...
modparam("tls_mgm", "client_domain", "dom1")
modparam("tls_mgm", "certificate", "[dom1]/etc/pki/tls/certs/opensips.pem")
modparam("tls_mgm", "private_key", "[dom1]/etc/pki/tls/private/opensips.key")
modparam("tls_mgm", "ca_list",     "[dom1]/etc/pki/tls/certs/ca.pem")
...
modparam("db_postgres", "use_tls", 1)
...
modparam("usrloc", "db_url", "postgres://root:1234@localhost/opensips?tls_domain=dom1")
...
```


### Exported Functions


NONE


### Installation and Running


Notes about installation and running.


## Contributors


### By Commit Statistics


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 67 | 29 | 554 | 1963 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 61 | 45 | 1088 | 349 |
| 3. | Norman Brandinger ([@NormB](https://github.com/NormB)) | 55 | 4 | 1449 | 2247 |
| 4. | Greg Fausak | 42 | 3 | 4472 | 2 |
| 5. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 27 | 20 | 350 | 203 |
| 6. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 18 | 15 | 45 | 87 |
| 7. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 15 | 12 | 210 | 27 |
| 8. | Jan Janak ([@janakj](https://github.com/janakj)) | 12 | 8 | 300 | 23 |
| 9. | Klaus Darilion | 10 | 6 | 139 | 67 |
| 10. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 9 | 7 | 102 | 34 |


**All remaining contributors**: Maksym Sobolyev ([@sobomax](https://github.com/sobomax)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Ancuta Onofrei, Norman Brandinger, Andrei Pelinescu-Onciul, Dusan Klinec ([@ph4r05](https://github.com/ph4r05)), Eseanu Marius Cristian ([@eseanucristian](https://github.com/eseanucristian)), Ruslan Bukin, Ryan Bullock ([@rrb3942](https://github.com/rrb3942)), Konstantin Bokarius, Razvan Pistolea, Aron Podrigal, Dan Pascu ([@danpascu](https://github.com/danpascu)), Ken Rice, Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Edson Gellert Schubert, Jarrod Baumann ([@jarrodb](https://github.com/jarrodb)).


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Ken Rice | Sep 2025 - Sep 2025 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Oct 2011 - Jul 2024 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Sep 2012 - May 2024 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jul 2005 - Feb 2024 |
| 5. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Apr 2004 - Feb 2023 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Oct 2021 |
| 7. | Norman Brandinger ([@NormB](https://github.com/NormB)) | Aug 2006 - Oct 2021 |
| 8. | Dan Pascu ([@danpascu](https://github.com/danpascu)) | May 2019 - May 2019 |
| 9. | Ryan Bullock ([@rrb3942](https://github.com/rrb3942)) | Mar 2019 - Mar 2019 |
| 10. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Jan 2011 - Feb 2019 |


**All remaining contributors**: Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Jarrod Baumann ([@jarrodb](https://github.com/jarrodb)), Dusan Klinec ([@ph4r05](https://github.com/ph4r05)), Aron Podrigal, Eseanu Marius Cristian ([@eseanucristian](https://github.com/eseanucristian)), Razvan Pistolea, Ruslan Bukin, Henning Westerholt ([@henningw](https://github.com/henningw)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Ancuta Onofrei, Klaus Darilion, Norman Brandinger, Jan Janak ([@janakj](https://github.com/janakj)), Greg Fausak, Andrei Pelinescu-Onciul.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation


### Contributors


**Last edited by:** Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Norman Brandinger ([@NormB](https://github.com/NormB)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Aron Podrigal, Eseanu Marius Cristian ([@eseanucristian](https://github.com/eseanucristian)), Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Henning Westerholt ([@henningw](https://github.com/henningw)), Jan Janak ([@janakj](https://github.com/janakj)).


*Documentation Copyrights:*


Copyright © 2003 Greg Fausak
