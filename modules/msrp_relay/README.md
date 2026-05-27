---
title: "MSRP Relay Module"
description: "This modules implements a Relay for the MSRP protocol, according to the specifications of RFC 4976. Once loaded, the module will automatically forward messages and manage MSRP sessions for the MSRP listeners defined in the script."
---

## Admin Guide


### Overview {#overview}


This modules implements a Relay for the MSRP protocol, according to
		the specifications of RFC 4976. Once loaded, the module will
		automatically forward messages and manage MSRP sessions for the MSRP
		listeners defined in the script.


For authenticating MSRP clients, a dedicated script route is run in order
    	to check the Digest credentials via pseudo-variables.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *proto_msrp*


#### External Libraries or Applications


The following libraries or applications must be installed 
			before running OpenSIPS with this module loaded:


- *openssl* or
				*libssl*
- *openssl-dev* or
					*libssl-dev*


### Exported Parameters {#exported_parameters}


#### hash_size (int) {#param_hash_size}


The size of the hash table that stores the MSRP sessions.
				It is the 2 logarithmic value of the real size.


*Default value is "10"*
			 (1024 records).


```opensips title="Set server_hsize parameter"
...
modparam("msrp_relay", "hash_size", 10)
...
		
```


#### cleanup_interval (int) {#param_cleanup_interval}


The interval between full iterations of the sessions table
			in order to clean up expired MSRP sessions. Note that a session
			will be kept in memory as long as the *Expires*
			value provided in the 200 OK response to the AUTH request indicates.


*Default value is "60".*


```opensips title="Set cleanup_interval parameter"
...
modparam("msrp_relay", "cleanup_interval", 30)
...
		
```


#### auth_route (str) {#param_auth_route}


The name of the script route to be called when authorizing
			MSRP clients (receiving an AUTH request with an Authorization header).
			Here you should provide the appropriate password (or pre-calculated HA1
			string) for the credentials via the [password var](#param_password_var)
			pseudo-variable, in order for the relay to check the client response.


*No default value; this parameter is mandatory.*


```opensips title="Set auth_route parameter"
...
modparam("msrp_relay", "auth_route", "auth")
...
		
```


#### username_var (string) {#param_username_var}


This name of the pseudo-variable that holds the authentication
		username.


Default value is "$var(username)".


```opensips title="username_var parameter usage"
modparam("msrp_relay", "username_var", "$var(msrp_auth_user)")
```


#### realm_var (string) {#param_realm_var}


This name of the pseudo-variable that hols the authentication
		Realm.


Default value is "$var(realm)".


```opensips title="realm_var parameter usage"
modparam("msrp_relay", "realm_var", "$var(msrp_auth_realm)")
```


#### password_var (string) {#param_password_var}


This name of the pseudo-variable that should be set in the
		[auth route](#param_auth_route) script route in order to check
		the client response when authenticating. The value to be set can be
		either the plaintext password or pre-calculated HA1 string, based on
		the  parameter.


Default value is "$var(password)".


```opensips title="password_var parameter usage"
modparam("msrp_relay", "password_var", "$var(msrp_auth_password)")
```


#### calculate_ha1 (integer) {#param_calculate_ha1}


This parameter configures whether the value of the
		[password var](#param_password_var) pseudo-variable should be
		treated as a plaintext password or a pre-calculated HA1 string.


Default value of this parameter is 0 (HA1 string).


```opensips title="calculate_ha1 parameter usage"
modparam("msrp_relay", "calculate_ha1", 1)
```


#### socket_route (str) {#param_socket_route}


The optional name of the script route to be called when
			start relaying a new MSRP session (upon the first SEND). The
			purpose of this route is to allow you to select the appropriate
			outbound socket to be be used for sending out the MSRP request.


Inside the route, the following information from the received
			request will be exposed:


- *source network information* via the
			`$si`, `$sp`,
			`$sP` and `$socket_in`
			variables.
- *destination URL schema* via the
			[dst schema var](#param_dst_schema_var) variable
- *destination URL host* via the
			[dst host var](#param_dst_host_var) variable


In this route you should optionally set the desired MSRP(S)
			outbound socket via the `$socket_out` variable.
			If none is set, the inbound interface will also be used as
			outbound if the schema (MSRP versus MSRPS) is the same. If the
			schema changes, the first socket (matching the out schema) will
			be used.


Default value is "NULL" (none).


```opensips title="Set socket_route parameter"
...
modparam("msrp_relay", "socket_route", "msrp_routing")

route[msrp_routing] {
	xlog("MSRP request comming from $si:$sp on $socket_in socket\n");
	xlog("trying to go to $var(dst_schema)://$var(dst_host)\n");

	$socket_out = "msrp:1.2.3.4:9999";
}
...
		
```


#### dst_schema_var (string) {#param_dst_schema_var}


This name of the variable to provide the schema ("msrp" or "msrps")
		of the destination URL in the socket route. See more on
		[socket route](#param_socket_route) parameter.


Default value is "$var(dst_schema)".


#### dst_host_var (string) {#param_dst_host_var}


This name of the variable to provide the host of the 
		destination URL in the socket route. See more on 
		[socket route](#param_socket_route) parameter.


Default value is "$var(dst_host)".


#### auth_realm (string) {#param_auth_realm}


The realm to be provided in the WWW-Authenticate header when the relay
		automatically challanges an MSRP client.


If this parameter is not set, the realm chose by the relay is the
		domain part of the top MSRP URI in the To-Path header of the AUTH request.


```opensips title="auth_realm parameter usage"
modparam("msrp_relay", "auth_realm", "opensips.org")
```


#### auth_expires (int) {#param_auth_expires}


The *Expires* header value to be provided in the 200 OK
		response to an AUTH request, if the client does not explicitly request
		one. This represents how long the MSRP URI provided by the relay in the
		Use-Path header is valid.


*Default value is "1800"*
		 (1024 records).


```opensips title="Set server_hsize parameter"
...
modparam("msrp_relay", "auth_expires", 600)
...
	
```


#### auth_min_expires (int) {#param_auth_min_expires}


The minimum value accepted by the relay in the *Expires*
		header, if the client provides it in the AUTH request. If the requested value
		is lower that this parameter, the relay will include a
		*Min-Expires* header with the configured value, in the
		423 Interval Out-of-Bounds response.


If not set, the relay will accept any value.


```opensips title="Set auth_min_expires parameter"
...
modparam("msrp_relay", "auth_min_expires", 60)
...
	
```


#### auth_max_expires (int) {#param_auth_max_expires}


The maximum value accepted by the relay in the *Expires*
		header, if the client provides it in the AUTH request. If the requested value
		is higher that this parameter, the relay will include a
		*Max-Expires* header with the configured value, in the
		423 Interval Out-of-Bounds response.


If not set, the relay will accept any value.


```opensips title="Set auth_max_expires parameter"
...
modparam("msrp_relay", "auth_max_expires", 60)
...
	
```


#### nonce_expire (integer) {#param_nonce_expire}


Nonces have limited lifetime. After a given period of time nonces 
		will be considered invalid. This is to protect replay attacks. 
		Credentials containing a stale nonce will be not authorized, but the 
		user agent will be challenged again. This time the challenge will 
		contain `stale` parameter which will indicate to the
		client that it doesn't have to disturb user by asking for username 
		and password, it can recalculate credentials using existing username 
		and password.


The value is in seconds and default value is 30 seconds.


```opensips title="nonce_expire parameter example"
modparam("msrp_relay", "nonce_expire", 15)   # Set nonce_expire to 15s
```


#### my_uri (string) {#param_my_uri}


MSRP URI of this relay, that will be matched against the first URI in
		the To-Path header of any request or response received. Messages that
		are not addressed to this relay will be dropped.


The MSRP URI provided by the relay in the Use-Path header, will be
		chosen based on the URI in the To-Path header of the AUTH request.


This parameter can be set multiple times


If the port is not set explicitly, the default value of 2855 wil
		be assumed. The session-id part of the URI should not be set


```opensips title="my_uri parameter usage"
modparam("msrp_relay", "my_uri", "msrp://opensips.org:2855;tcp")
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 22 | 7 | 1646 | 16 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 7 | 4 | 200 | 20 |
| 3. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | Mar 2022 - May 2023 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Apr 2022 - May 2023 |
| 3. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)).


*Documentation Copyrights:*


Copyright © 2022 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
