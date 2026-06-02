---
title: "proto_bin Module"
description: "The **proto_bin** module is a transport module which implements Binary Interface TCP-based communication. It does not handle TCP connections management, but only offers higher-level primitives to read and write BIN messages over TCP. It calls registered callback functions for every comple..."
---

## Admin Guide


### Overview {#overview}


The **proto_bin** module is a
		transport module which implements Binary Interface TCP-based communication. It does
		not handle TCP connections management, but only offers higher-level
		primitives to read and write BIN messages over TCP. It calls registered
		callback functions for every complete message received.


Once loaded, you will be able to define BIN listeners in your
		configuration file by adding their IP and, optionally, a listening port,
		similar to this example:
	```c

...
socket= bin:127.0.0.1 		# change the listening IP
socket= bin:127.0.0.1:5080	# change the listening IP and port
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


#### bin_port (integer) {#param_bin_port}


The default port to be used by all TCP listeners.


*Default value is 5555.*


```c title="Set bin_port parameter"
...
modparam("proto_bin", "bin_port", 6666)
...
```


#### bin_send_timeout (integer) {#param_bin_send_timeout}


Time in milliseconds after a TCP connection will be closed if it is
		not available for blocking writing in this interval (and OpenSIPS wants
		to send something on it).


*Default value is 100 ms.*


```c title="Set bin_send_timeout parameter"
...
modparam("proto_bin", "bin_send_timeout", 200)
...
```


#### bin_max_msg_chunks (integer) {#param_bin_max_msg_chunks}


The maximum number of chunks in which a BIN message is expected to
			arrive via TCP. If a received packet is more fragmented than this,
			the connection is dropped (either the connection is very
			overloaded and this leads to high fragmentation - or we are the
			victim of an ongoing attack where the attacker is sending very
			fragmented traffic in order to decrease server performance).


*Default value is 32.*


```c title="Set bin_max_msg_chunks parameter"
...
modparam("proto_bin", "bin_max_msg_chunks", 8)
...
```


#### bin_async (integer) {#param_bin_async}


Specifies whether the TCP connect and write operations should be
			done in an asynchronous mode (non-blocking connect and
			write) or not. If disabled, OpenSIPS will block and wait for TCP
			operations like connect and write.


*Default value is 1 (enabled).*


```c title="Set bin_async parameter"
...
modparam("proto_bin", "bin_async", 0)
...
```


#### bin_async_max_postponed_chunks (integer) {#param_bin_async_max_postponed_chunks}


If *bin_async* is enabled, this specifies the
			maximum number of BIN messages that can be stashed for later/async
			writing. If the connection pending writes exceed this number, the
			connection will be marked as broken and dropped.


*Default value is 1024.*


```c title="Set bin_async_max_postponed_chunks parameter"
...
modparam("proto_bin", "bin_async_max_postponed_chunks", 1024)
...
```


#### bin_async_local_connect_timeout (integer) {#param_bin_async_local_connect_timeout}


If *bin_async* is enabled, this specifies the
			number of milliseconds that a connect will be tried in blocking
			mode (optimization). If the connect operation lasts more than
			this, the connect will go to async mode and will be passed to TCP
			MAIN for polling.


*Default value is 100 ms.*


```c title="Set bin_async_local_connect_timeout parameter"
...
modparam("proto_bin", "bin_async_local_connect_timeout", 200)
...
```


#### bin_async_local_write_timeout (integer) {#param_bin_async_local_write_timeout}


If *bin_async* is enabled, this specifies the
			number of milliseconds that a write op will be tried in blocking
			mode (optimization). If the write operation lasts more than this,
			the write will go to async mode and will be passed to bin MAIN for
			polling.


*Default value is 10 ms.*


```c title="Set bin_async_local_write_timeout parameter"
...
modparam("proto_bin", "tcp_async_local_write_timeout", 100)
...
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 29 | 20 | 62 | 467 |
| 2. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 16 | 4 | 954 | 140 |
| 3. | Ionel Cerghit ([@ionel-cerghit](https://github.com/ionel-cerghit)) | 15 | 3 | 1196 | 38 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 13 | 10 | 63 | 63 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 9 | 7 | 29 | 9 |
| 6. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 5 | 3 | 34 | 36 |
| 7. | Eseanu Marius Cristian ([@eseanucristian](https://github.com/eseanucristian)) | 4 | 2 | 1 | 5 |
| 8. | Nick Altmann ([@nikbyte](https://github.com/nikbyte)) | 3 | 1 | 4 | 4 |
| 9. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - Jul 2025 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2016 - Dec 2024 |
| 3. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Nov 2023 |
| 4. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Oct 2021 |
| 5. | Nick Altmann ([@nikbyte](https://github.com/nikbyte)) | May 2021 - May 2021 |
| 6. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Mar 2017 - Apr 2021 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 8. | Ionel Cerghit ([@ionel-cerghit](https://github.com/ionel-cerghit)) | Jul 2015 - Dec 2016 |
| 9. | Eseanu Marius Cristian ([@eseanucristian](https://github.com/eseanucristian)) | Jul 2015 - Jul 2015 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Ionel Cerghit ([@ionel-cerghit](https://github.com/ionel-cerghit)).
## License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
