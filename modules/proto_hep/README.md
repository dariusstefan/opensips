---
title: "proto_hep Module"
description: "The **proto_hep** module is a transport module which implements hepV1 and hepV2 UDP-based communication and hepV3 TCP-based communication. It also offers an API with which you can register callbacks which are called after the HEP header is parsed and also can pack sip messages to HEP mess..."
---

## Admin Guide


### Overview {#overview}


The **proto_hep** module is a
		transport module which implements hepV1 and hepV2 UDP-based communication
		and hepV3 TCP-based communication. It also offers an API with which
		you can register callbacks which are called after the HEP header is
		parsed and also can pack sip messages to HEP messages.The unpacking
		part is done internally.


Once loaded, you will be able to define HEP listeners in your
		configuration file by adding their IP and, optionally, a listening port.
		You can define both TCP and UDP listeners. On UDP you will be able to
		receive HEP v1, v2 and v3 packets, on TCP only HEPv3.
	```

...
#HEPv3 listener
socket= hep_tcp:127.0.0.1:6061 		# change the listening IP
#HEPv1, v2, v3 listener
socket= hep_udp:127.0.0.1:6061 		# change the listening IP
...
```


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *None*.


#### External Libraries or Applications


The following libraries or applications must be installed before
		running OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### hep_id (str) {#param_hep_id}


Specify a destination for HEP packets and the version of
			HEP protocol used. All parameters inside
			**hep_id** must be separated by
			**;**. The parameters
			are given in key-value format, the possible keys being
			**uri**, **transport**
			and **version**, except destiantion's
			URI which doesn't have a key and is in **host:port**. **transport** key can be
			**TCP** or **UDP**.
			**TCP** works only for HEP version 3.
			**Version** is the hep protocol version
			and can be **1**, **2**
			or **3**.


HEPv1 and HEPv2 can use only UDP. HEPv3 can use both TCP and UDP having the
			default set to TCP. If no hep version defined, the default is version 3 with TCP.


NO default value. If **hep_id** the module
		can't be used for HEP tracing.


**Example: Set hep_id parameter**


```opensips
...
/* define a destination to localhost on port 8001 using hepV3 on tcp */
modparam("proto_hep", "hep_id",
"[hep_dst] 127.0.0.1:8001; transport=tcp; version=3")
/* define a destination to 1.2.3.4 on port 5000 using hepV2; no transport(default UDP) */
modparam("proto_hep", "hep_id", "[hep_dst] 1.2.3.4:5000; version=2")
/* define only the destination uri; version will be 3(default) and transport TCP(default) */
modparam("proto_hep", "hep_id", "[hep_dst] 1.2.3.4:5000")
```


#### homer5_on (int) {#param_homer5_on}


Specify how the data should be encapsulated in the HEP packet. If set to
			*0*, then the JSON based HOMER 6 format will be used. Otherwise,
			if set to anything different than *0*, the plain text HOMER 5
			format will be used for encapsulation. On the capturing node, this parameter
			affects the behavior of the *report_capture* function from the
			[sipcapture](../sipcapture#func_report_capture)
			module.


Default value 1, HOMER5 format.


**Example: Set homer5_on parameter**


```opensips
modparam("proto_hep", "homer5_on", 0)
```


#### homer5_delim (str) {#param_homer5_delim}


In case **homer5_on** is set
		(different than 0), with this parameter you will be able to set
		the delmiter between different payload parts.


Default value ":".


**Example: Set homer5_on parameter**


```opensips
modparam("proto_hep", "homer5_delim", "##")
```


#### hep_port (integer) {#param_hep_port}


The default port to be used by all TCP/UDP listeners.


*Default value is 5656.*


**Example: Set hep_port parameter**


```opensips
...
modparam("proto_hep", "hep_port", 6666)
...
```


#### hep_send_timeout (integer) {#param_hep_send_timeout}


Time in milliseconds after a TCP connection will be closed if it is
		not available for blocking writing in this interval (and OpenSIPS wants
		to send something on it).


*Default value is 100 ms.*


**Example: Set hep_send_timeout parameter**


```opensips
...
modparam("proto_hep", "hep_send_timeout", 200)
...
```


#### hep_max_msg_chunks (integer) {#param_hep_max_msg_chunks}


The maximum number of chunks in which a HEP message is expected to
			arrive via TCP. If a received packet is more fragmented than this,
			the connection is dropped (either the connection is very
			overloaded and this leads to high fragmentation - or we are the
			victim of an ongoing attack where the attacker is sending very
			fragmented traffic in order to decrease server performance).


*Default value is 32.*


**Example: Set hep_max_msg_chunks parameter**


```opensips
...
modparam("proto_hep", "hep_max_msg_chunks", 8)
...
```


#### hep_async (integer) {#param_hep_async}


Specifies whether the TCP connect and write operations should be
			done in an asynchronous mode (non-blocking connect and
			write) or not. If disabled, OpenSIPS will block and wait for TCP
			operations like connect and write.


*Default value is 1 (enabled).*


**Example: Set hep_async parameter**


```opensips
...
modparam("proto_hep", "hep_async", 0)
...
```


#### hep_async_max_postponed_chunks (integer) {#param_hep_async_max_postponed_chunks}


If *hep_async* is enabled, this specifies the
			maximum number of HEP messages that can be stashed for later/async
			writing. If the connection pending writes exceed this number, the
			connection will be marked as broken and dropped.


*Default value is 32.*


**Example: Set hep_async_max_postponed_chunks parameter**


```opensips
...
modparam("proto_hep", "hep_async_max_postponed_chunks", 16)
...
```


#### hep_capture_id (integer) {#param_hep_capture_id}


The parameter indicate the capture agent ID for HEPv2/v3 protocol.
		Limitation: 16-bit integer.


*Default value is "1".*


**Example: Set hep_capture_id parameter**


```opensips
...
modparam("proto_hep", "hep_capture_id", 234)
...
```


#### hep_async_local_connect_timeout (integer) {#param_hep_async_local_connect_timeout}


If *hep_async* is enabled, this specifies the
			number of milliseconds that a connect will be tried in blocking
			mode (optimization). If the connect operation lasts more than
			this, the connect will go to async mode and will be passed to TCP
			MAIN for polling.


*Default value is 100 ms.*


**Example: Set hep_async_local_connect_timeout parameter**


```opensips
...
modparam("proto_hep", "hep_async_local_connect_timeout", 200)
...
```


#### hep_async_local_write_timeout (integer) {#param_hep_async_local_write_timeout}


If *hep_async* is enabled, this specifies the
			number of milliseconds that a write op will be tried in blocking
			mode (optimization). If the write operation lasts more than this,
			the write will go to async mode and will be passed to bin MAIN for
			polling.


*Default value is 10 ms.*


**Example: Set hep_async_local_write_timeout parameter**


```opensips
...
modparam("proto_hep", "hep_async_local_write_timeout", 100)
...
```


### Exported Functions {#exported_functions}


#### correlate(hep_id, type1, correlation1, type2, correlation2) {#func_correlate}


Send a hep message with an extra correlation id containing the two correlation given
			as arguments. The two types must differ. This will help
			on the capturing side to correlate two calls for example, being given their callid
			as correlation ids.


This function can be used from REQUEST_ROUTE, FAILURE_ROUTE, ONREPLY_ROUTE, BRANCH_ROUTE, LOCAL_ROUTE.


Meaning of the parameters is as follows:


- *hep_id (string)*
				the name of the *hep_id* defined in modparam section,
				specifying where to do the tracing.
- *type1 (string)*
				the key name identify the first correlation id.
- *correlation1 (string)*
				the first extra correlation id that will be put in the extra correlation chunk.
- *type2 (string)*
				the key name identify the second correlation id.
- *correlation2 (string)*
				the second extra correlation id that will be put in the extra correlation chunk.


**Example: correlate usage**


```
...
/* see declaration of hep_dst in trace_id section */
/* we suppose we have two correlations in two varibles: cor1 and cor2 */
	correlate("hep_dst", "correlation-no-1",$var(cor1),"correlation-no-2", $var(cor2));
...
```


## Developer Guide


### Available Functions


#### pack_hep(from, to, proto, payload, plen, retbuf, retlen)
			


The function packs connection details and sip message into HEP message. It's
		your job to free both the old and the new buffer.


Meaning of the parameters is as follows:


- *sockaddr_union *from* - sockaddr_union describing
			sending socket
- *sockaddr_union *to* - sockaddr_union describing
			receiving socket
- *int proto* - protocol used in hep header;
- *char *payload* SIP payload buffer
- *int plen* SIP payload buffer length
- *char **retbuf* HEP message buffer
- *int *retlen* HEP message buffer length


#### register_hep_cb(cb)
			


The function register callbacks to be called whenever a HEP message
		is received. The callbacks parameters are struct hep_desc*(see hep.h for
		details) a structure that holds all details about the hep header and the
		receive_info* structure. The callback can return HEP_SCRIPT_SKIP which
		stops the HEP message from being passed thrrough scripts.


Meaning of the parameters is as follows:


- *hep_cb_t cb* HEP callback


#### hep_version
			


Current version of hep used.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | 149 | 66 | 8047 | 998 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 37 | 28 | 84 | 487 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 26 | 19 | 384 | 170 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 17 | 14 | 77 | 88 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 7 | 4 | 36 | 84 |
| 6. | Nick Altmann ([@nikbyte](https://github.com/nikbyte)) | 3 | 1 | 2 | 2 |
| 7. | Dan Pascu ([@danpascu](https://github.com/danpascu)) | 3 | 1 | 1 | 1 |
| 8. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |
| 9. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Nov 2015 - Oct 2023 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2016 - Jul 2022 |
| 3. | Nick Altmann ([@nikbyte](https://github.com/nikbyte)) | May 2021 - May 2021 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jan 2017 - Apr 2021 |
| 5. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | May 2020 - May 2020 |
| 6. | Dan Pascu ([@danpascu](https://github.com/danpascu)) | May 2019 - May 2019 |
| 7. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 8. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 9. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | Oct 2015 - Apr 2017 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)).


*Documentation Copyrights:*


Copyright © 2015 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
