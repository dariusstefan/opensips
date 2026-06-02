---
title: "mysql Module"
description: "This is a module which provides MySQL connectivity for OpenSIPS. It implements the DB API defined in OpenSIPS."
---

## Admin Guide


### Overview {#overview}


This is a module which provides MySQL connectivity for OpenSIPS.
		It implements the DB API defined in OpenSIPS.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *If a [use tls](#param_use_tls) is defined, the **tls_mgm** module will need to be loaded as well*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *libmysqlclient-dev* - the development libraries of mysql-client.


### Exported Parameters {#exported_parameters}


#### exec_query_threshold (integer) {#param_exec_query_threshold}


If queries take longer than 'exec_query_threshold' microseconds, warning
		messages will be written to logging facility.


*Default value is 0 - disabled.*


```c title="Set exec_query_threshold parameter"
...
modparam("db_mysql", "exec_query_threshold", 60000)
...
```


#### timeout_interval (integer) {#param_timeout_interval}


Time interval after which a connection attempt (read or write request)
		is aborted. The value counts three times, as several retries are done
		from the driver before it gives up.


The read timeout parameter is ignored on driver versions prior to
		"5.1.12", "5.0.25" and "4.1.22".
		The write timeout parameter is ignored on version prior to "5.1.12"
		and "5.0.25", the "4.1" release don't support it at all.


*Default value is 2 (6 sec).*


```c title="Set timeout_interval parameter"
...
modparam("db_mysql", "timeout_interval", 2)
...
```


#### max_db_queries (integer) {#param_max_db_queries}


The maximum number of retries to execute a failed query due to connections problems.
            If this parameter is set improperly, it is set to default value.


*Default value is 2.*


```c title="Set max_db_queries parameter"
...
modparam("db_mysql", "max_db_queries", 2)
...
```


#### max_db_retries (integer) {#param_max_db_retries}


The maximum number of database connection retries. If this parameter
                is set improperly, it is set to default value.


*Default value is 3.*


```c title="Set max_db_retries parameter"
...
modparam("db_mysql", "max_db_retries", 2)
...
```


#### ps_max_col_size (integer) {#param_ps_max_col_size}


The maximum size of a column's data, when fetched using prepared
		statements.  Particularly relevant for variable-length data, such as
		CHAR, BLOB, etc.


NOTE: Should a column's data exceed this limit, the value will be
		silently truncated to fit the buffer, without reporting any errors!


*Default value is *1024 (bytes)*.*


```c title="Set ps_max_col_size parameter"
...
modparam("db_mysql", "ps_max_col_size", 4096)
...
```


#### use_tls (integer) {#param_use_tls}


Setting this parameter will allow you to use TLS for MySQL connections.
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


```c title="Set the use_tls parameter"
...
modparam("tls_mgm", "client_domain", "dom1")
modparam("tls_mgm", "certificate", "[dom1]/etc/pki/tls/certs/opensips.pem")
modparam("tls_mgm", "private_key", "[dom1]/etc/pki/tls/private/opensips.key")
modparam("tls_mgm", "ca_list",     "[dom1]/etc/pki/tls/certs/ca.pem")
...
modparam("db_mysql", "use_tls", 1)
...
modparam("usrloc", "db_url", "mysql://root:1234@localhost/opensips?tls_domain=dom1")
...
```


### Exported Functions {#exported_functions}


No function exported to be used from configuration file.


### Installation


Because it dependes on an external library, the mysql module is not
		compiled and installed by default. You can use one of the next options.


- - edit the "Makefile" and remove "db_mysql" from "excluded_modules"
			list. Then follow the standard procedure to install OpenSIPS:
			"make all; make install".
- - from command line use: 'make all include_modules="db_mysql";
			make install include_modules="db_mysql"'.


### Exported Events {#exported_events}


#### E_MYSQL_CONNECTION {#event_E_MYSQL_CONNECTION}


This event is raised when a MySQL connection is lost or recovered.


Parameters:


- *url* - the URL of the connection as specified by the *db_url* parameter.
- *status* - *connected* if the connection recovered, or 
				*disconnected* if the connection was lost.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Jan Janak ([@janakj](https://github.com/janakj)) | 150 | 53 | 5336 | 3190 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 97 | 71 | 1573 | 792 |
| 3. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 57 | 30 | 693 | 1239 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 38 | 31 | 484 | 177 |
| 5. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 28 | 20 | 571 | 154 |
| 6. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 19 | 16 | 231 | 59 |
| 7. | Andrei Pelinescu-Onciul | 16 | 14 | 52 | 49 |
| 8. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 14 | 11 | 159 | 25 |
| 9. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 12 | 8 | 179 | 78 |
| 10. | Jiri Kuthan ([@jiriatipteldotorg](https://github.com/jiriatipteldotorg)) | 11 | 6 | 393 | 2 |


**All remaining contributors**: Nils Ohlmeier, Norman Brandinger ([@NormB](https://github.com/NormB)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Eseanu Marius Cristian ([@eseanucristian](https://github.com/eseanucristian)), Dan Pascu ([@danpascu](https://github.com/danpascu)), Walter Doekes ([@wdoekes](https://github.com/wdoekes)), Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)), Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)), Konstantin Bokarius, Andreas Heise, Razvan Pistolea, Saúl Ibarra Corretgé ([@saghul](https://github.com/saghul)), Sergio Gutierrez, Edson Gellert Schubert, Augusto Caringi.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Dec 2022 |
| 2. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Feb 2011 - Jan 2022 |
| 3. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | Apr 2017 - May 2021 |
| 4. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | Apr 2021 - Apr 2021 |
| 5. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Oct 2011 - Jan 2021 |
| 6. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Aug 2002 - Apr 2019 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Nov 2017 - Jun 2018 |
| 8. | Augusto Caringi | Jul 2017 - Jul 2017 |
| 9. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Mar 2017 - Mar 2017 |
| 10. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | Feb 2016 - Feb 2016 |


**All remaining contributors**: Eseanu Marius Cristian ([@eseanucristian](https://github.com/eseanucristian)), Saúl Ibarra Corretgé ([@saghul](https://github.com/saghul)), Razvan Pistolea, Norman Brandinger ([@NormB](https://github.com/NormB)), Sergio Gutierrez, Henning Westerholt ([@henningw](https://github.com/henningw)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Andreas Heise, Jan Janak ([@janakj](https://github.com/janakj)), Andrei Pelinescu-Onciul, Dan Pascu ([@danpascu](https://github.com/danpascu)), Jiri Kuthan ([@jiriatipteldotorg](https://github.com/jiriatipteldotorg)), Nils Ohlmeier.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Eseanu Marius Cristian ([@eseanucristian](https://github.com/eseanucristian)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Henning Westerholt ([@henningw](https://github.com/henningw)), Jan Janak ([@janakj](https://github.com/janakj)).
### License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
