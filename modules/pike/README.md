---
title: "pike Module"
description: "The module provides a simple mechanism for DOS protection - DOS based on floods at network level. The module keeps trace of all (or selected ones) IPs of incoming SIP traffic (as source IP) and blocks the ones that exceeded some limit. Works simultaneous for IPv4 and IPv6 addresses."
---

## Admin Guide


### Overview {#overview}


The module provides a simple mechanism for DOS protection - DOS based
		on floods at network level. The module keeps trace of all (or selected
		ones) IPs of incoming SIP traffic (as source IP) and blocks the ones
		that exceeded some limit.
		Works simultaneous for IPv4 and IPv6 addresses.


The module does not implement any actions on blocking - it just simply
		reports that there is a high traffic from an IP; what to do, is
		the administator decision (via scripting).


### How to use


There are 2 ways of using this module (as detecting flood attacks and
		as taking the right action to limit the impact on the system):


- *manual* - from routing script you can force
				the check of the source IP of an incoming requests, using
				"pike_check_req" function. Note that this checking works only
				for SIP requests and you can decide (based on scripting logic)
				what source IPs to be monitored and what action to be taken
				when a flood is detected.
- *automatic* - the module will install
				internal hooks to catch all incoming requests and replies (even
				if not well formed from SIP point of view) - more or less the
				module will monitor all incoming packages (from the network) on
				the SIP sockets. Each time the source IP of a package needs to
				be analyse (to see if trusted or not), the module will run a
				script route - see "check_route" module parameter -, where,
				based on custom logic, you can decide if that IP needs to be
				monitored for flooding or not. As action, when flood is
				detected, the module will automatically drop the packages.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *No dependencies on other OpenSIPS modules*.


#### External Libraries or Applications


The following libraries or applications must be installed before
		running OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### sampling_time_unit (integer) {#param_sampling_time_unit}


Time period used for sampling (or the sampling accuracy ;-) ). The
		smaller the better, but slower. If you want to detect peaks, use a
		small one. To limit the access (like total number of requests on a
		long period of time) to a proxy resource (a gateway for ex), use
		a bigger value of this parameter.


IMPORTANT: a too small value may lead to performance penalties due
		timer process overloading.


*Default value is 2.*


```c title="Set sampling_time_unit parameter"
...
modparam("pike", "sampling_time_unit", 10)
...
```


#### reqs_density_per_unit (integer) {#param_reqs_density_per_unit}


How many requests should be allowed per sampling_time_unit before
		blocking all the incoming request from that IP. Practically, the
		blocking limit is between ( let's have x=reqs_density_per_unit) x
		and 3*x for IPv4 addresses and between x and 8*x for ipv6 addresses.


*Default value is 30.*


```c title="Set reqs_density_per_unit parameter"
...
modparam("pike", "reqs_density_per_unit", 30)
...
```


#### remove_latency (integer) {#param_remove_latency}


For how long the IP address will be kept in memory after the last
		request from that IP address. It's a sort of timeout value.


*Note:* If the *remove_latency*
		value is lower than *sampling_time_unit* value,
		nodes might expire before being unblocked, therefore losing some
		UNBLOCK events. In order to prevent this, if the
		*remove_latency* is lower, OpenSIPS internally
		forces its value to *sampling_time_unit + 1*.


*Default value is 120.*


```c title="Set remove_latency parameter"
...
modparam("pike", "remove_latency", 130)
...
```


#### check_route (integer) {#param_check_route}


The name of the script route to be triggers (in automatic way) when a
		package is received from the network. If you do a "drop" in this route,
		it will indicate to the module that the source IP of the package does
		not need to be monitored. Otherwise, the source IP will be
		automatically monitered.


By defining this parameter, the automatic checking mode is enabled.


*Default value is NONE (no auto mode).*


```c title="Set check_route parameter"
...
modparam("pike", "check_route", "pike")
...
route[pike]{
    if ($si==111.222.111.222)  /*trusted, do not check it*/
        drop;
    /* all other IPs are checked*/
}
....
```


#### pike_log_level (integer) {#param_pike_log_level}


Log level to be used by module to auto report the blocking (only first
		time) and unblocking of IPs detected as source of floods.


*Default value is 1 (L_WARN).*


```c title="Set pike_log_level parameter"
...
modparam("pike", "pike_log_level", -1)
...
```


### Exported Functions {#exported_functions}


#### pike_check_req() {#func_pike_check_req}


Process the source IP of the current request and returns false if
		the IP was exceeding the blocking limit.


Return codes:


- *1 (true)* - IP is not to be blocked or
				internal error occurred.

  > **Warning:** 
- *-1 (false)* - IP is source of
				flooding, being previously detected
- *-2 (false)* - IP is detected as a new
				source of flooding - first time detection


This function can be used from REQUEST_ROUTE.


```c title="pike_check_req usage"
...
if (!pike_check_req()) { exit; };
...
```


### Exported MI Functions {#exported_mi_functions}


#### pike_list {#mi_pike_list}


Lists the nodes in the pike tree.


Name: *pike_list*


Parameters: *none*


MI FIFO Command Format:


```c
		opensips-cli -x mi pike_list
		
```


#### pike_rm {#mi_pike_rm}


Remove a node from the pike tree by IP address.


Name: *pike_rm*


Parameters:


- *IP* - IP address currently blocked.


MI FIFO Command Format:


```c
		opensips-cli -x mi pike_rm 10.0.0.106
		
```


### Exported Events {#exported_events}


#### E_PIKE_BLOCKED {#event_E_PIKE_BLOCKED}


This event is raised when the *pike* module
			decides that an IP should be blocked.


Parameters:


- *ip* - the IP address that has been blocked.


### Provided Status/Report Identifiers {#sr_identifiers}


The module provides the "pike" Status/Report group, only with
	the "main"/default SR identifier.


There is no usefull status published by the module.


In terms of reports/logs, the following events will be reported:


- IP X.Y.Z.W detected as flooding


For how to access and use the Status/Report information, please see
	[https://www.opensips.org/Documentation/Interface-StatusReport-3-3](>https://www.opensips.org/Documentation/Interface-StatusReport-3-3).


## Developer Guide


One single tree (for both IPv4 and IPv6) is used. Each node contains a byte, the IP
	addresses stretching from root to the leafs.


```c title="Tree of IP addresses"
	   / 193 - 175 - 132 - 164
tree root /                  \ 142
	  \ 195 - 37 - 78 - 163
	   \ 79 - 134
```


To detect the whole address, step by step, from the root to the leafs, the nodes corresponding
	to each byte of the ip address are expanded. In order to be expended a node has to be hit
	for a given number of times (possible by different addresses; in the previous example, the
	node "37" was expended by the 195.37.78.163 and 195.37.79.134 hits).


For 193.175.132.164 with x= reqs_density_per_unit:


- After first req hits -> the "193" node is built.
- After x more hits, the "175" node is build; the hits of
		"193" node are split between itself and its child--both of them gone
		have x/2.
- And so on for node "132" and "164".
- Once "164" build the entire address can be found in the
		tree. "164" becomes a leaf. After it will be hit as a leaf for x
		times, it will become "RED" (further request from this address will
		be blocked).


So, to build and block this address were needed 3*x hits. Now, if reqs start coming from
	193.175.132.142, the first 3 bytes are already in the tree (they are shared with the previous
	address), so I will need only x hits (to build node "142" and to make it
	"RED") to make this address also to be blocked.  This is the reason for the
	variable number of hits necessary to block an IP.


The maximum number of hits to turn an address red are (n is the address's number of bytes):


1 (first byte) + x (second byte) + (x / 2) * (n - 2) (for the rest of the bytes) + (n - 1)
	(to turn the node to red).


So, for IPv4 (n = 4) will be 3x and for IPv6 (n = 16) will be 9x. The minimum number of hits
	to turn an address red is x.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 137 | 58 | 4206 | 2669 |
| 2. | Andrei Pelinescu-Onciul | 15 | 8 | 120 | 336 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 13 | 11 | 94 | 18 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 12 | 9 | 29 | 67 |
| 5. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 11 | 9 | 24 | 20 |
| 6. | Jan Janak ([@janakj](https://github.com/janakj)) | 9 | 4 | 386 | 34 |
| 7. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 8 | 6 | 73 | 52 |
| 8. | Jiri Kuthan ([@jiriatipteldotorg](https://github.com/jiriatipteldotorg)) | 6 | 3 | 257 | 0 |
| 9. | Jarrod Baumann ([@jarrodb](https://github.com/jarrodb)) | 5 | 3 | 111 | 22 |
| 10. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 4 | 2 | 2 | 2 |


**All remaining contributors**: Elena-Ramona Modroiu, Ancuta Onofrei, Konstantin Bokarius, Julián Moreno Patiño, Jesus Rodrigues, Norman Brandinger ([@NormB](https://github.com/NormB)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Edson Gellert Schubert.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jun 2002 - Feb 2022 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Nov 2020 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | May 2011 - Sep 2019 |
| 4. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 5. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 6. | Julián Moreno Patiño | Feb 2016 - Feb 2016 |
| 7. | Jarrod Baumann ([@jarrodb](https://github.com/jarrodb)) | Apr 2015 - Apr 2015 |
| 8. | Norman Brandinger ([@NormB](https://github.com/NormB)) | Aug 2013 - Aug 2013 |
| 9. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | Nov 2006 - Mar 2008 |
| 10. | Konstantin Bokarius | Mar 2008 - Mar 2008 |


**All remaining contributors**: Edson Gellert Schubert, Henning Westerholt ([@henningw](https://github.com/henningw)), Jesus Rodrigues, Ancuta Onofrei, Elena-Ramona Modroiu, Andrei Pelinescu-Onciul, Jan Janak ([@janakj](https://github.com/janakj)), Jiri Kuthan ([@jiriatipteldotorg](https://github.com/jiriatipteldotorg)).


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Julián Moreno Patiño, Jarrod Baumann ([@jarrodb](https://github.com/jarrodb)), Norman Brandinger ([@NormB](https://github.com/NormB)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Jesus Rodrigues, Elena-Ramona Modroiu, Jan Janak ([@janakj](https://github.com/janakj)).
## License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
