---
title: "SpeedDial Module"
description: "This module provides on-server speed dial facilities. An user can store records consisting of pairs short numbers (2 digits) and SIP addresses into a table of OpenSIPS. Then it can dial the two digits whenever it wants to call the SIP address associated with them."
---

## Admin Guide


### Overview


This module provides on-server speed dial facilities. An user can store
		records consisting of pairs short numbers (2 digits) and SIP addresses
		into a table of OpenSIPS. Then it can dial the two digits whenever it
		wants to call the SIP address associated with them.


### Dependencies


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *database module (mysql, dbtext, ...)*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *None*.


### Exported Parameters


#### `db_url` (string)


The URL of database where the table containing speed dial records.


*Default value is mysql://opensipsro:opensipsro@localhost/opensips.*


**Example: Set `db_url` parameter**


```opensips
...
modparam("speeddial", "db_url", "mysql://user:xxx@localhost/db_name")
...
```


#### `user_column` (string)


The name of column storing the user name of the owner of the speed dial
		record.


*Default value is "username".*


**Example: Set `user_column` parameter**


```opensips
...
modparam("speeddial", "user_column", "userid")
...
```


#### `domain_column` (string)


The name of column storing the domain of the owner of the speed dial
		record.


*Default value is  "domain".*


**Example: Set `domain_column` parameter**


```opensips
...
modparam("speeddial", "domain_column", "userdomain")
...
```


#### `sd_user_column` (string)


The name of the column storing the user part of the short dial address.


*Default value is  "sd_username".*


**Example: Set `sd_user_column` parameter**


```opensips
...
modparam("speeddial", "sd_user_column", "short_user")
...
```


#### `sd_domain_column` (string)


The name of the column storing the domain of the short dial address.


*Default value is  "sd_domain".*


**Example: Set `sd_domain_column` parameter**


```opensips
...
modparam("speeddial", "sd_domain_column", "short_domain")
...
```


#### `new_uri_column` (string)


The name of the column containing the URI that will be use to replace
		the short dial URI.


*Default value is "new_uri".*


**Example: Set `new_uri_column` parameter**


```opensips
...
modparam("speeddial", "new_uri_column", "real_uri")
...
```


#### `domain_prefix` (string)


If the domain of the owner (From URI) starts with the value of this parameter, then
		it is stripped before performing the lookup of the short number.


*Default value is NULL.*


**Example: Set `domain_prefix` parameter**


```opensips
...
modparam("speeddial", "domain_prefix", "tel.")
...
```


#### `use_domain` (int)


The parameter specifies wheter or not to use the domain when searching a
		speed dial record (0 - no domain, 1 - use domain from From URI,
		2 - use both domains, from From URI and from request URI).


*Default value is 0.*


**Example: Set `use_domain` parameter**


```opensips
...
modparam("speeddial", "use_domain", 1)
...
```


### Exported Functions


#### `sd_lookup(table [, owner])`


The function lookups the short dial number from R-URI in 'table' and replaces the R-URI with associated address.


Meaning of the parameters is as follows:


- *table* (string) - The name of the table storing the
			speed dial records.
- *owner* (string) - The SIP URI of the owner of
			short dialing codes. If not pressent, URI of From header is used.


This function can be used from REQUEST_ROUTE.


**Example: `sd_lookup` usage**


```
...
# 'speed_dial' is the default table name created by opensips db script
if($ru=~"sip:[0-9]{2}@.*")
	sd_lookup("speed_dial");
# use auth username
if($ru=~"sip:[0-9]{2}@.*")
	sd_lookup("speed_dial", "sip:$au@$fd");
...
```


### Installation and Running


#### OpenSIPS config file


Next picture displays a sample usage of speeddial.


**Example: OpenSIPS config script - sample speeddial usage**


```opensips
...
# sample config script to use speeddial module
#

# ----------- global configuration parameters ------------------------

check_via=no	# (cmd. line: -v)
dns=no          # (cmd. line: -r)
rev_dns=no      # (cmd. line: -R)

# ------------------ module loading ----------------------------------

mpath="/usr/local/lib/opensips/modules"
loadmodule "sl.so"
loadmodule "tm.so"
loadmodule "rr.so"
loadmodule "maxfwd.so"
loadmodule "usrloc.so"
loadmodule "registrar.so"
loadmodule "textops.so"
loadmodule "mysql.so"
loadmodule "speeddial.so"
loadmodule "mi_fifo.so"

# ----------------- setting module-specific parameters ---------------

# -- mi_fifo params --

modparam("mi_fifo", "fifo_name", "/tmp/opensips_fifo")

# -- usrloc params --

modparam("usrloc", "db_mode",   0)

# -------------------------  request routing logic -------------------

# main routing logic
route{

	# initial sanity checks 
	if (!mf_process_maxfwd_header("10"))
	{
		sl_send_reply(483,"Too Many Hops");
		exit;
	};
	if ($ml >=  65535 )
	{
		sl_send_reply(513, "Message too big");
		exit;
	};

	if (!$rm=="REGISTER") record_route();

	if (loose_route())
	{
		if (!t_relay())
		{
			sl_reply_error();
		};
		exit;
	};

	if (!is_myself("$rd"))
	{
		if (!t_relay())
		{
			sl_reply_error();
		};
		exit;
	};

	if (is_myself("$rd"))
	{
		if ($rm=="REGISTER")
		{
			save("location");
			exit;
		};

		if($ru=~"sip:[0-9]{2}@.*")
			sd_lookup("speeddial");

		lookup("aliases");
		if (!is_myself("$rd"))
		{
			if (!t_relay())
			{
				sl_reply_error();
			};
			exit;
		};

		if (!lookup("location"))
		{
			sl_send_reply(404, "Not Found");
			exit;
		};
	};

	if (!t_relay())
	{
		sl_reply_error();
	};
}


...
		
```


## Contributors


### By Commit Statistics


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 27 | 23 | 90 | 147 |
| 2. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 21 | 16 | 127 | 137 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 15 | 12 | 38 | 61 |
| 4. | Elena-Ramona Modroiu | 12 | 2 | 1063 | 1 |
| 5. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 8 | 6 | 11 | 8 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 6 | 4 | 31 | 35 |
| 7. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 4 | 2 | 50 | 41 |
| 8. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 4 | 5 |
| 9. | Elena-Ramona Modroiu | 4 | 2 | 4 | 1 |
| 10. | Sergio Gutierrez | 4 | 2 | 2 | 2 |


**All remaining contributors**: Walter Doekes ([@wdoekes](https://github.com/wdoekes)), Anca Vamanu, Andrei Pelinescu-Onciul, Konstantin Bokarius, Julián Moreno Patiño, Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Edson Gellert Schubert.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - May 2024 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 3. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | Apr 2021 - Apr 2021 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - Jul 2020 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Oct 2005 - Mar 2020 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 8. | Julián Moreno Patiño | Feb 2016 - Feb 2016 |
| 9. | Sergio Gutierrez | Nov 2008 - Dec 2008 |
| 10. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | May 2006 - Mar 2008 |


**All remaining contributors**: Konstantin Bokarius, Edson Gellert Schubert, Henning Westerholt ([@henningw](https://github.com/henningw)), Anca Vamanu, Elena-Ramona Modroiu, Andrei Pelinescu-Onciul, Elena-Ramona Modroiu.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation


### Contributors


**Last edited by:** Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Sergio Gutierrez, Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Elena-Ramona Modroiu, Elena-Ramona Modroiu.


*Documentation Copyrights:*


Copyright © 2004 Voice Sistem SRL
