---
title: "JSON-RPC Module"
description: "This module is an implementation of an JSON-RPC v2.0 client [http://www.jsonrpc.org/specification](http://www.jsonrpc.org/specification). that can send a call to a JSON-RPC server over a TCP connection."
---

## Admin Guide


### Overview {#overview}


This module is an implementation of an JSON-RPC v2.0
		client [http://www.jsonrpc.org/specification](http://www.jsonrpc.org/specification).
		that can send a call to a JSON-RPC server over a TCP connection.


NOTE that the current version of this module does not support TCP
		connection reusage, nor asynchronous commands.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *none*.


#### External Libraries or Applications


The following libraries or applications must be installed before 
		running OpenSIPS with this module loaded:


- *none*


### Exported Parameters {#exported_parameters}


#### connect_timeout (integer) {#param_connect_timeout}


The amount of milliseconds OpenSIPS waits to connect to the the
			JSON-RPC server, until it times out.


*Default value is "500 milliseconds".*


```c title="Set connect_timeout parameter"
...
modparam("jsonrpc", "connect_timeout", 200)
...
```


#### write_timeout (integer) {#param_write_timeout}


The amount of milliseconds OpenSIPS waits to send a RPC command to
			the JSON-RPC server, until it times out.


*Default value is "500 milliseconds".*


```c title="Set write_timeout parameter"
...
modparam("jsonrpc", "write_timeout", 300)
...
```


#### read_timeout (integer) {#param_read_timeout}


The amount of milliseconds OpenSIPS waits for the JSON-RPC server
			to respond to a JSON-RPC request, until it times out. Note that
			these parameter only affects the *jsonrpc_request*
			command.


*Default value is "500 milliseconds".*


```c title="Set read_timeout parameter"
...
modparam("jsonrpc", "read_timeout", 300)
...
```


### Exported Functions {#exported_functions}


#### jsonrpc_request(destination, method, params, ret_var) {#func_jsonrpc_request}


Does a JSON-RPC request to the JSON-RPC server
				indicated in the *destination*
				parameter, and waits for a reply from it.


This function can be used from any route.


The function has the following parameters:


- *destination* (string) - address of the
						JSON-RPC server. The format needs to be
						*IP:port*.
- *method* (string) - the method used in
						the RPC request.
- *params* (string) - these are the parameters
						sent to the RPC method. This parameter needs to be
						a properly formated JSON array, or JSON object,
						according the the JSON-RPC specifications.
- *ret_var* a writeable variable
						used to store the result of the JSON-RPC command. If
						the command returns an error, the variable will be
						populated with the error JSON, otherwise, with the
						body of the JSON-RPC result.


The function has the following return codes:


- *1* - JSON-RPC command executed
						successfully, and the server returned success. You can
						check the *ret_pvar* variable for
						the result.
- *-1* - There was an internal error
						during processing.
- *-2* - There was a connection
						(timeout or connect) error with the destination.
- *-3* - The JSON-RPC was
						successfully run, but the server returned an error.
						Check the *ret_pvar* value to find
						out more information.


```c title="jsonrpc_request() function usage"
	...
	if (!jsonrpc_request("127.0.0.1", "add", "[1,2]", $var(ret))) {
		xlog("JSON-RPC command failed with $var(ret)\n");
		exit;
	}
	xlog(JSON-RPC command returned $var(ret)\n");
	# parse $var(ret) as JSON, or whatever the function returns
	...
	
```


#### jsonrpc_notification(destination, method, params) {#func_jsonrpc_notification}


Does a JSON-RPC notification to the JSON-RPC server
				indicated in the *destination*
				parameter, but unlike [jsonrpc request](#func_jsonrpc_request),
				it does not wait for a reply from the JSON-RPC server.


This function can be used from any route.


The function receives the same parameters as 
				[jsonrpc request](#func_jsonrpc_request), except for the *ret_pvar*. Also, the same values are returned.


```c title="jsonrpc_notification() function usage"
	...
	if (!jsonrpc_notification("127.0.0.1", "block_ip", "{ \"ip": \"$si\" }")) {
		xlog("JSON-RPC notification failed with $rc!\n");
		exit;
	}
	...
	
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 16 | 7 | 931 | 11 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 6 | 4 | 16 | 32 |
| 3. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 6 | 2 | 33 | 128 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 5 | 3 | 4 | 2 |
| 5. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 3 | 1 | 3 | 3 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |
| 7. | rdondeti | 2 | 1 | 12 | 0 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | rdondeti | Mar 2026 - Mar 2026 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Dec 2018 - Apr 2023 |
| 3. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Mar 2018 - Nov 2019 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | Apr 2019 - Apr 2019 |
| 6. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Apr 2018 - Nov 2018 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)).
### License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
