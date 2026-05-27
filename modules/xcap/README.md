---
title: "XCAP Module"
description: "The module contains several parameters and functions common to all modules using XCAP capabilities."
---

## Admin Guide


### Overview {#overview}


The module contains several parameters and functions common to all
        modules using XCAP capabilities.


The module is currently used by the following modules: presence_xml, rls and xcap_client.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *a database module*.


### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *libxml-dev*.


### Exported Parameters {#exported_parameters}


#### db_url(str) {#param_db_url}


The database url.


*Default value is "mysql://opensips:opensipsrw@localhost/opensips".*


```opensips title="Set db_url parameter"
...
modparam("xcap", "db_url", "dbdriver://username:password@dbhost/dbname")
...
                
```


#### xcap_table(str) {#param_xcap_table}


The name of the db table where XCAP documents are stored.


*Default value is "xcap".*


```opensips title="Set xcap_table parameter"
...
modparam("xcap", "xcap_table", "xcap")
...
                
```


#### integrated_xcap_server (int) {#param_integrated_xcap_server}


This parameter is a flag for the type of XCAP server or servers 
		used. If integrated ones, like OpenXCAP from AG Projects, 
		with direct access to database table, the parameter should be
		set to a positive value. Apart from updating in xcap table,
		the integrated server must send an MI command refershWatchers 
		[pres_uri] [event] when a user modifies a rules document.


*Default value is "0".*


```opensips title="Set integrated_xcap_server parameter"
...
modparam("xcap", "integrated_xcap_server", 1)
...
                
```


### Exported Functions {#exported_functions}


None to be used in configuration file.


## Developer Guide


The module exports a number of parameters and functions that are used
            in several other modules.


### bind_xcap_api(xcap_api_t* api)


This function allows binding the needed functions.


```c title="xcap_api structure"
...
typedef struct xcap_api {
        int integrated_server;
        str db_url;
        str xcap_table;
        normalize_sip_uri_t normalize_sip_uri;
        parse_xcap_uri_t parse_xcap_uri;
        get_xcap_doc_t get_xcap_doc;
} xcap_api_t;
...
			
```


### normalize_xcap_uri


This function normalizes a SIP URI found in a XCAP document. It un-escapes it and
                    adds the SIP scheme in case it was missing. Returns a statically allocated string
                    buffer containing the normalized form.


Parameters:


- *uri*-
				the URI that needs to be normalized


### parse_xcap_uri


This function parses the given XCAP URI.


Parameters:


- *uri*-
				the URI that needs to be parsed in string format
- *xcap_uri*-
				xcap_uri_t structure that will be filled with the parsed information

  ```
  Parameter type:
  ...
  typedef struct {
      char buf[MAX_URI_SIZE];
      str uri;
      str root;
      str auid;
      str tree;
      str xui;
      str filename;
      str selector;
  } xcap_uri_t;
  ...
                          
  ```


### get_xcap_doc


This function queries the local DB for the required XCAP document. It will return the document and its
                    corresponding etag.


Parameters:


- *user*-
				user part od the URI of the document owner
- *domain*-
				domain part od the URI of the document owner
- *type*-
                                type of the requested document, represents the AUID, can be one of PRES_RULES, RESOURCE_LISTS,
                                RLS_SERVICES, PIDF_MANIPULATION, OMA_PRES_RULES
- *filename*-
				if specified it will be used to match the document filename, it defaults to 'index'
- *match_etag*-
				if specified the document is only returned its etag matches this one
- *doc*-
				reference to the storage for the returned document
- *etag*-
				reference to the storage for the returned document's etag


### db_url


URL of the database to which the XCAP mdoules witll connect.


### xcap_table


Name of the table used to store XCAP documents. Defaults to 'xcap'.


### integrated_server


Boolean flag indicating if the XCAP server has access to the local database or
                xcap_client will be used to fetch documents.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Saúl Ibarra Corretgé ([@saghul](https://github.com/saghul)) | 15 | 3 | 1260 | 53 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 11 | 8 | 55 | 106 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 7 | 5 | 25 | 21 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 5 | 3 | 6 | 10 |
| 5. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 3 | 1 | 13 | 3 |
| 6. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 3 | 1 | 7 | 7 |
| 7. | Ken Rice | 3 | 1 | 3 | 3 |
| 8. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 2 | 2 |
| 9. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 2 | 1 | 2 | 0 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Ken Rice | Sep 2025 - Sep 2025 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Jul 2014 - May 2024 |
| 3. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Oct 2014 - Mar 2020 |
| 5. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - Sep 2019 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 7. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - May 2017 |
| 8. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Jan 2013 - Jan 2013 |
| 9. | Saúl Ibarra Corretgé ([@saghul](https://github.com/saghul)) | Nov 2012 - Jan 2013 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Saúl Ibarra Corretgé ([@saghul](https://github.com/saghul)).


*Documentation Copyrights:*


Copyright © 2012 AG Projects
