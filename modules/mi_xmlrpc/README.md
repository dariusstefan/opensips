---
title: "mi_xmlrpc Module"
description: "This module implements a xmlrpc server that handles xmlrpc requests and generates xmlrpc responses. When a xmlrpc message is received a default method is executed."
---

## Admin Guide


### Overview {#overview}


This module implements a xmlrpc server that handles xmlrpc
		requests and generates xmlrpc responses.
		When a xmlrpc message is received a default method is executed.


At first, it looks up the MI command.
		If found it parses the called procedure's parameters
		into a MI tree and the command is executed.
		A MI reply tree is returned that is formatted back in xmlrpc.
		The response is built in two ways - like a string that
		contains the MI tree nodes information (name, values and
		attributes) or like an array whose elements are consisted
		of each MI tree node stored information.


### Dependencies {#dependencies}


#### External Libraries or Applications


The following libraries or applications must be installed before
		running OpenSIPS with this module loaded:


- *libxml2*


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *httpd* module.


### Exported Parameters {#exported_parameters}


#### http_root(string) {#param_http_root}


Specifies the root path for xmlrpc requests:
		http://[opensips_IP]:[opensips_httpd_port]/[http_root]


*The default value is "RPC2".*


```c title="Set http_root parameter"
...
modparam("mi_xmlrpc", "http_root", "opensips_mi_xmlrpc")
...
```


#### trace_destination (string) {#param_trace_destination}


Trace destination as defined in the tracing module. Currently
		the only tracing module is **proto_hep**.
		This is where traced mi messages will go.


**WARNING:**A tracing module must be
			loaded in order for this parameter to work. (for example
			**proto_hep**).


*Default value is none(not defined).*


```c title="Set trace_destination parameter"
...
modparam("proto_hep", "trace_destination", "[hep_dest]10.0.0.2;transport=tcp;version=3")

modparam("mi_xmlrpc", "trace_destination", "hep_dest")
...
```


#### trace_bwlist (string) {#param_trace_bwlist}


Filter traced mi commands based on a blacklist or a whitelist.
		**trace_destination** must be defined for
		this parameter to have any purpose. Whitelists can be defined using
		'w' or 'W', blacklists using 'b' or 'B'. The type is separate by the
		actual blacklist by ':'. The mi commands in the list must be separated
		by ','.


Defining a blacklists means all the commands that are not blacklisted
			will be traced. Defining a whitelist means all the commands that are
			not whitelisted will not be traced.
			**WARNING:** One can't define both
			a whitelist and a blacklist. Only one of them is allowed. Defining
			the parameter a second time will just overwrite the first one.


**WARNING:**A tracing module must be
			loaded in order for this parameter to work. (for example
			**proto_hep)**.


*Default value is none(not defined).*


```c title="Set trace_destination parameter"
...
## blacklist ps and which mi commands
## all the other commands shall be traced
modparam("mi_xmlrpc", "trace_bwlist", "b: ps, which")
...
## allow only sip_trace mi command
## all the other commands will not be traced
modparam("mi_xmlrpc", "trace_bwlist", "w: sip_trace")
...
```


### Exported Functions {#exported_functions}


No function exported to be used from configuration file.


### Known issues


Commands with large responses (like ul_dump) will fail if the
		configured size of the httpd buffer is to small (or if there
		isn't enough pkg memory configured).


Future realeases of the httpd and mi_xmlrpc modules
		will address this issue.


### Example


This is an example showing the xmlrpc format for the
		"get_statistics net: shmem:" MI commad:
		response.


```c title="XMLRPC request"
POST /xmlrpc HTTP/1.0
Host: my.host.com
User-Agent: My xmlrpc UA
Content-Type: text/xml
Content-Length: 216

<?xml version='1.0'?>
<methodCall>
	<methodName>get_statistics</methodName>
	<params>
		<param>
		<value>
		<struct>
		<member>
			<name>statistics</name>
			<value>
			<array>
			<data>
				<value><string>shmem:</string></value>
				<value><string>core:</string></value>
			</data>
			</array>
			</value>
		</member>
		</struct>
		</value>
		</param>
	</params>
</methodCall>


HTTP/1.0 200 OK
Content-Length: 236
Content-Type: text/xml; charset=utf-8
Date: Mon, 8 Mar 2013 12:00:00 GMT

<?xml version="1.0" encoding="UTF-8"?>.
<methodResponse>
<params><param>
<value><struct><member><name>net:waiting_udp</name><value><string>0</string></value></member><member><name>net:waiting_tcp</name><value><string>0</string></value></member><member><name>net:waiting_tls</name><value><string>0</string></value></member><member><name>shmem:total_size</name><value><string>268435456</string></value></member><member><name>shmem:used_size</name><value><string>40032</string></value></member><member><name>shmem:real_used_size</name><value><string>277112</string></value></member><member><name>shmem:max_used_size</name><value><string>277112</string></value></member><member><name>shmem:free_size</name><value><string>268158344</string></value></member><member><name>shmem:fragments</name><value><string>194</string></value></member></struct></value></param></params>
</methodResponse>.
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 134 | 42 | 10036 | 376 |
| 2. | Lavinia Andrei Spirea | 22 | 2 | 2206 | 7 |
| 3. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 10 | 8 | 44 | 20 |
| 4. | Juha Heinanen ([@juha-h](https://github.com/juha-h)) | 6 | 4 | 56 | 8 |
| 5. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 5 | 3 | 30 | 11 |
| 6. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 5 | 3 | 26 | 3 |
| 7. | Ancuta Onofrei | 4 | 1 | 64 | 97 |
| 8. | Elena-Ramona Modroiu | 3 | 1 | 7 | 7 |
| 9. | Konstantin Bokarius | 3 | 1 | 2 | 5 |
| 10. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 3 | 1 | 1 | 1 |


**All remaining contributors**: Edson Gellert Schubert, Dan Pascu ([@danpascu](https://github.com/danpascu)).


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Dec 2006 - Oct 2014 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Jul 2014 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Feb 2012 - Jan 2013 |
| 4. | Dan Pascu ([@danpascu](https://github.com/danpascu)) | Aug 2008 - Aug 2008 |
| 5. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | Feb 2007 - Mar 2008 |
| 6. | Konstantin Bokarius | Mar 2008 - Mar 2008 |
| 7. | Edson Gellert Schubert | Feb 2008 - Feb 2008 |
| 8. | Juha Heinanen ([@juha-h](https://github.com/juha-h)) | Dec 2006 - Oct 2007 |
| 9. | Ancuta Onofrei | Sep 2007 - Sep 2007 |
| 10. | Henning Westerholt ([@henningw](https://github.com/henningw)) | Aug 2007 - Aug 2007 |


**All remaining contributors**: Elena-Ramona Modroiu, Lavinia Andrei Spirea.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Juha Heinanen ([@juha-h](https://github.com/juha-h)), Elena-Ramona Modroiu, Lavinia Andrei Spirea.
### License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
