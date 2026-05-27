---
title: "AAA_DIAMETER MODULE"
description: "This module provides a Diameter implementation for the core AAA API interface."
---

## Admin Guide


### Overview {#overview}


This module provides a Diameter implementation for the core AAA API interface.


Any module that wishes to use it has to do the following:


- *include aaa.h*
- *make a bind call with a proper Diameter-specific URL, e.g. "diameter:freeDiameter-client.conf"*


### Dependencies {#dependencies}


#### OpenSIPS Modules


None.


#### External Libraries or Applications


All Diameter message building and parsing, as well as the peer state
		machine and Diameter-related network communication are all powered by
		[the freeDiameter project](http://www.freediameter.net/trac/)
		and C libraries, dynamically linking with the "aaa_diameter" module.


The following libraries must be installed before running
		OpenSIPS with this module loaded:


- *libfdcore* v1.2.1 or higher
- *libfdproto* v1.2.1 or higher


### Exported Parameters {#exported_parameters}


#### fd_log_level (integer) {#param_fd_log_level}


This parameter measures the *quietness* of the logging
		done by the freeDiameter library. Possible values:


- 0 (ANNOYING)
- 1 (DEBUG)
- 3 (NOTICE, default)
- 5 (ERROR)
- 6 (FATAL)


NOTE: since freeDiameter logs to standard output, you must also enable
		the new core parameter, **log_stdout**,
		before getting any logs from the library.


```c title="Setting the fd_log_level parameter"
modparam("aaa_diameter", "fd_log_level", 0)
```


#### realm (string) {#param_realm}


The unique realm to be used by all participating Diameter peers.


Default value is *"diameter.test"*.


```c title="Setting the realm parameter"
modparam("aaa_diameter", "realm", "opensips.org")
```


#### peer_identity (string) {#param_peer_identity}


The identity (realm subdomain) of the Diameter server peer, to which
		the OpenSIPS Diameter client peer will connect.


Default value is *"server"*
				(i.e. "server.diameter.test").


```c title="Setting the peer_identity parameter"
modparam("aaa_diameter", "peer_identity", "server")
```


#### aaa_url (string) {#param_aaa_url}


URL of the diameter client: the configuration file, with an optional
			extra-avps-file, where the Diameter client is configured.


By default, the connection is not created.


```c title="Setting the aaa_url parameter"
modparam("aaa_diameter", "aaa_url", "diameter:freeDiameter-client.conf")
```


```c title="Setting the aaa_url parameter"
modparam("aaa_diameter", "aaa_url", "diameter:freeDiameter-client.conf;extra-avps-file:dictionary.opensips")
```


#### answer_timeout (integer) {#param_answer_timeout}


Time, in milliseconds, after which a [dm send request](#func_dm_send_request)
		function call with no received reply will time out and return a
		**-2** code.


Default value is *2000* ms.


```c title="Setting the answer_timeout parameter"
modparam("aaa_diameter", "answer_timeout", 5000)
```


### Exported Functions {#exported_functions}


#### dm_send_request(app_id, cmd_code, avps_json, [rpl_avps_pv]) {#func_dm_send_request}


Perform a blocking Diameter request over to the interconnected peer
		and return the Result-Code AVP value from the reply.


*Parameters*


- *app_id* (integer) - ID of the application.
				A custom application must be defined in the dictionary.opensips
				Diameter configuration file before it can be recognized.
- *cmd_code* (integer) - ID of the command.  A
				custom command code, name and AVP requirements must be defined
				in the dictionary.opensips Diameter configuration file beforehand.
				body of the HTTP response.
- *avps_json* (string) - A JSON Array containing
				the AVPs to include in the message payload.
- *rpl_avps_pv* (var, optional) - output variable which will
				hold all AVP names from the Diameter Answer along with their values, packed
				as a JSON Array string.  The "json" module and its *$json*
				variable could be used to iterate this array.


*Return Codes*


- **1** - Success
- **-1** - Internal Error
- **-2** - Request timeout
			(the [answer timeout](#param_answer_timeout) was exceeded
			before an Answer could be processed)


This function can be used from any route.


```c title="dictionary.opensips extended syntax"
# Example of defining custom Diameter AVPs, Application IDs,
# Requests and Replies in the "dictionary.opensips" file

ATTRIBUTE out_gw            232 string
ATTRIBUTE trunk_id          233 string

ATTRIBUTE rated_duration    234 integer
ATTRIBUTE call_cost         235 integer

ATTRIBUTE Exponent          429 integer32
ATTRIBUTE Value-Digits      447 integer64

ATTRIBUTE Cost-Unit 424 grouped
{
	Value-Digits | REQUIRED | 1
	Exponent | OPTIONAL | 1
}

ATTRIBUTE Currency-Code     425 unsigned32

ATTRIBUTE Unit-Value  445 grouped
{
	Value-Digits | REQUIRED | 1
	Exponent | OPTIONAL | 1
}

ATTRIBUTE Cost-Information  423 grouped
{
	Unit-Value | REQUIRED | 1
	Currency-Code | REQUIRED | 1
	Cost-Unit | OPTIONAL | 1
}

APPLICATION 42 My Diameter Application

REQUEST 92001 My-Custom-Request
{
	Origin-Host | REQUIRED | 1
	Origin-Realm | REQUIRED | 1
	Destination-Realm | REQUIRED | 1
	Transaction-Id | REQUIRED | 1
	Sip-From-Tag | REQUIRED | 1
	Sip-To-Tag | REQUIRED | 1
	Acct-Session-Id | REQUIRED | 1
	Sip-Call-Duration | REQUIRED | 1
	Sip-Call-Setuptime | REQUIRED | 1
	Sip-Call-Created | REQUIRED | 1
	Sip-Call-MSDuration | REQUIRED | 1
	out_gw | REQUIRED | 1
	call_cost | REQUIRED | 1
	Cost-Information | OPTIONAL | 1
}

ANSWER 92001 My-Custom-Answer
{
	Origin-Host | REQUIRED | 1
	Origin-Realm | REQUIRED | 1
	Destination-Realm | REQUIRED | 1
	Transaction-Id | REQUIRED | 1
	Result-Code | REQUIRED | 1
}
```


```c title="dm_send_request usage"
# Building an sending an My-Custom-Request (92001) for the
# My Diameter Application (42)
$var(payload) = "[
	{ \"Origin-Host\": \"client.diameter.test\" },
	{ \"Origin-Realm\": \"diameter.test\" },
	{ \"Destination-Realm\": \"diameter.test\" },
	{ \"Sip-From-Tag\": \"dc93-4fba-91db\" },
	{ \"Sip-To-Tag\": \"ae12-47d6-816a\" },
	{ \"Acct-Session-Id\": \"a59c-dff0d9efd167\" },
	{ \"Sip-Call-Duration\": 6 },
	{ \"Sip-Call-Setuptime\": 1 },
	{ \"Sip-Call-Created\": 1652372541 },
	{ \"Sip-Call-MSDuration\": 5850 },
	{ \"out_gw\": \"GW-774\" },
	{ \"cost\": \"10.84\" },
	{ \"Cost-Information\": [
		{\"Unit-Value\": [{\"Value-Digits\": 1000}]},
		{\"Currency-Code\": 35}
		]}
]";

$var(rc) = dm_send_request(42, 92001, $var(payload), $var(rpl_avps));
xlog("rc: $var(rc), AVPs: $var(rpl_avps)\n");
$json(avps) := $var(rpl_avps);
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 91 | 26 | 5928 | 960 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 12 | 9 | 235 | 36 |
| 3. | Larry Laffer | 3 | 1 | 6 | 5 |
| 4. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 3 | 1 | 5 | 5 |
| 5. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |
| 6. | Alexandra Titoc | 2 | 1 | 7 | 0 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | May 2023 - Aug 2025 |
| 2. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jul 2025 - Jul 2025 |
| 3. | Larry Laffer | Jul 2025 - Jul 2025 |
| 4. | Alexandra Titoc | Sep 2024 - Sep 2024 |
| 5. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | May 2021 - Feb 2024 |
| 6. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)).


*Documentation Copyrights:*


Copyright © 2021 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
