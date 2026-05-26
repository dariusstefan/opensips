---
title: "Load Balancer Module"
description: "The Load-Balancer module comes to provide traffic routing based on load. Shortly, when OpenSIPS routes calls to a set of destinations, it is able to keep the load status (as number of ongoing calls) of each destination and to choose to route to the less loaded destination (at that moment). ..."
---

## Admin Guide


### Overview {#overview}


The Load-Balancer module comes to provide traffic routing based on load. 
	Shortly, when OpenSIPS routes calls to a set of destinations, it is able 
	to keep the load status (as number of ongoing calls) of each destination 
	and to choose to route to the less loaded destination (at that moment). 
	OpenSIPS is aware of the capacity of each destination - it is preconfigured 
	with the maximum load accepted by the destinations. To be more precise, 
	when routing, OpenSIPS will consider the less loaded destination not the 
	destination with the smallest number of ongoing calls, but the destination 
	with the largest available slot.


Also the module has the capability to do failover (to try a new destination
	if the selected one does not respond), to keep state of the destinations 
	(to remember the failed destination and avoid using them agai) and to 
	check the health of the destination (by doing probing of the destination 
	and auto re-enabling).


### How it works


Please refer to the Load-Balancer tutorial from the OpenSIPS website:
		[https://opensips.org/Documentation/Tutorials-LoadBalancing-1-9](https://opensips.org/Documentation/Tutorials-LoadBalancing-1-9).


### Probing and Disabling destinations


The module has the capability to monitor the status of the destinations by
	doing SIP probing (sending SIP requests like OPTIONS).


For each destination, you can configure what kind of probing should be 
	done (probe_mode column):


- *(0)* - no probing at all;
- *(1)* - probing only when the destination is
		in disabled mode (disabling via MI command will competely stop the 
		probing also). The destination will be automatically re-enabled
		when the probing will succeed next time;
- *(2)* - probing all the time. If disabled, 
		the destination will be automatically re-enabled when the probing 
		will succeed next time;


A destination can become disabled in two ways:


- script detection
- MI command


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *Dialog* - Dialog module
*freeswitch*. - only if 
				"fetch_freeswitch_stats" is enabled.
- *dialog* - TM module (only if probing is
				enabled)
- *clusterer* - only if "cluster_id"
				option is enabled.
- *database* - one of the DB modules


#### External Libraries or Applications


The following libraries or applications must be installed before 
		running OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### db_url (string) {#param_db_url}


The URL pointing to the database where the load-balancing rules 
		are stored.


*Default value is "mysql://opensips:opensipsrw@localhost/opensips".*


**Example: Set db_url parameter**


```opensips
...
modparam("load_balancer", "db_url", "dbdriver://username:password@dbhost/dbname")
...
```


#### db_table (string) {#param_db_table}


The name of the DB table containing the load-balancing rules.


*Default value is "load_balancer".*


**Example: Set db_table parameter**


```opensips
...
modparam("load_balancer", "db_table", "lb")
...
```


#### probing_interval (integer) {#param_probing_interval}


How often (in seconds) the probing of a destination should be done. If
		set to 0, the probing will be disabled as functionality (for all 
		destinations)


*Default value is "30".*


**Example: Set probing_interval parameter**


```opensips
...
modparam("load_balancer", "probing_interval", 60)
...
```


#### probing_method (string) {#param_probing_method}


The SIP method to be used for the probing requests.


*Default value is ""OPTIONS"".*


**Example: Set probing_method parameter**


```opensips
...
modparam("load_balancer", "probing_method", "INFO")
...
```


#### probing_from (string) {#param_probing_from}


The FROM SIP URI to be advertised in the SIP probing requests.


*Default value is ""sip:prober@localhost"".*


**Example: Set probing_from parameter**


```opensips
...
modparam("load_balancer", "probing_from", "sip:pinger@192.168.2.10")
...
```


#### probing_reply_codes (string) {#param_probing_reply_codes}


A comma separted list of SIP reply codes. The codes defined here 
		will be considered as valid reply codes for probing messages,
		apart for 200.


*Default value is "NULL".*


**Example: Set probing_reply_codes parameter**


```opensips
...
modparam("load_balancer", "probing_reply_codes", "501, 403")
...
```


#### probing_verbose (number) {#param_probing_verbose}


A boolean option to enable extra logging related to the 
		enabling or disabling of the destinations based on probing
		replies and MI commands.


A 0 value means disabled, anything else means enabled.


The extra logging will be done on INFO level.


*Default value is "0" (disabled).*


**Example: Set probing_verbose parameter**


```opensips
...
modparam("load_balancer", "probing_verbose", 1)
...
```


#### lb_define_blacklist (string) {#param_lb_define_blacklist}


Defines a blacklist based on a lb group. This list will contain the IPs
		(no port, all protocols) of the destinations matching the given group.


Multiple instances of this param are allowed.


*Default value is "NULL".*


**Example: Set the lb_define_blacklist parameter**


```opensips
...
modparam("load_balancer", "lb_define_blacklist", "list= 1,4,3")
modparam("load_balancer", "lb_define_blacklist", "blist2= 2,10,6")
...
```


#### fetch_freeswitch_stats (integer) {#param_fetch_freeswitch_stats}


If enabled, the maximum value of a resource may also consist of
		FreeSWITCH Event Socket Layer URLs, e.g. *"channels=fs://:password@freeswitch.example.com"*
		or *"channels=fs://user:password@127.0.0.1:8021"*. The default ESL port is 8021.


OpenSIPS will establish a connection with the given socket and
		periodically update the internal maximum value of the given resource
		using statistics pushed by the FreeSWITCH box.


The max value of a resource is updated every *event_heartbeat_interval*
		seconds (see the "freeswitch" OpenSIPS module for more details
		regarding this setting), as the stats arrive from FreeSWITCH.


Given the following format for FreeSWITCH heartbeat messages:


```
{
  ...
  "FreeSWITCH-Hostname": "pbx2",
  "FreeSWITCH-IPv4": "172.17.0.3",
  "Idle-CPU": "78.400000",
  "Max-Sessions": "1000",
  "Session-Count": "0",
  ...
}
```


, the load balancer uses the following formula in order to periodically
		update its "max_load" values for each FreeSWITCH box (FreeSWITCH data
		is highlighted in bold):


*max_load = (**Idle-CPU** / 100)
				* (**Max-Sessions** -
				(**Session-Count** -
				current_load))*


*Default value is "0" (disabled).*


**Example: Set the fetch_freeswitch_load parameter**


```opensips
...
modparam("load_balancer", "fetch_freeswitch_stats", 1)
...
```


#### initial_freeswitch_load (integer) {#param_initial_freeswitch_load}


This parameter is only relevant for some seconds after module startup/reload,
		when no statistics from newly loaded FreeSWITCH ESL sockets have arrived, yet the
		routing of calls must remain unaffected. Any FreeSWITCH-enabled resource will
		inherit this value for the entire interval mentioned above (up to 20 seconds!).


*Default value is "1000".*


**Example: Set the initial_freeswitch_load parameter**


```opensips
...
modparam("load_balancer", "initial_freeswitch_load", 200)
...
```


#### cluster_id (integer) {#param_cluster_id}


The ID of the cluster the module is part of. The clustering support is 
		used in load-balancer module for two purposes: for sharing the status 
		of the destinations and for controlling the pinging to destinations.


If clustering enbled, the module will automatically share changes
		over the status of the destinations with the other 
		OpenSIPS instances that are part of a cluster. Whenever such a status 
		changes (following an MI command, a probing result, a script command),
		the module will replicate this status change to all the nodes in this 
		given cluster.


The clustering with sharing tag support may be used to control which 
		node in the cluster will perform the pinging/probing to 
		destinations. See the
		[cluster sharing tag](#param_cluster_sharing_tag) option.


This OpenSIPS cluster exposes the **"load_balancer-status-repl"**
capability in order to mark nodes as eligible for becoming data donors during an
arbitrary sync request. Consequently, the cluster must have *at least
one node* marked with the **"seed"** value
as the *clusterer.flags* column/property in order to be fully functional.
Consult the [clusterer - Capabilities](/modules/clusterer#capabilities)
chapter for more details.


For more info on how to define and populate a cluster (with OpenSIPS 
		nodes) see the "clusterer" module.


*Default value is "0 (none)".*


**Example: Set cluster_id parameter**


```opensips
...
# replicate destination status with all OpenSIPS in cluster ID 9
modparam("load_balancer", "cluster_id", 9)
...
```


#### cluster_sharing_tag (string) {#param_cluster_sharing_tag}


The name of the sharing tag (as defined per clusterer modules) to 
		control which node is responsible for perform the self-triggered
		actions in the module. Such actions may be the destination probing or 
		sharing the changes in the destination status.
		If defined, only the node with active status of this tag will 
		perform the actions (pinging and sharing status).


The [cluster id](#param_cluster_id) must be defined for this option
		to work.


This is an optional parameter. If not set, all the nodes in the cluster
		will individually do the probing and share the status changes.


*Default value is "empty (none)".*


**Example: Set cluster_sharing_tag parameter**


```opensips
...
# only the node with the active "vip" sharing tag will perform pinging
# and broadcast the status changes
modparam("load_balancer", "cluster_id", 9)
modparam("load_balancer", "cluster_sharing_tag", "vip")
...
```


### Exported Functions {#exported_functions}


#### lb_start(grp,resources[,flags],[attrs]) {#func_lb_start}


The function starts a new load-balancing session over the available
		destinations. This translates into finding the less loaded destination
		that can provide the requested resources and belong to a requested
		group.


Meaning of the parameters is as follows:


- *grp* (int) - group id for the destinations;
			the destination may be grouped in several groups you can you for 
			differnet scenarios.
- *resources* (string) - a 
			semi-colon separated list of resources required by the current
			call.
- *flags* (string, optional) - various flags
			to controll the LB algorithm ( or computing the available load on
			the system):

  - *n* - Negative availability  - use
				destinations with negative availability (exceeded capacity);
				do not ignore resources with negative availability, and thus 
				able to select for load balancing destinations with exceeded 
				capacity. This might be needed in scenarios where we want to 
				limit generic calls volume and always pass 
				important/high-priority calls.
  - *r* - Relative value - the relative
				available load (how many percentages are free) is used in
				computing the load of each pear/resource; Without this flag,
				the Absolute value is assumed - the effective
				available load ( maximum_load - current_load) is used in
				computing the load of each pear/resource.
  - *s* - Pick a random destination if
				multiple destinations with the same load are found, instead
				of always picking first matched destination.
				This could help to offload an excessive load from the first
				destination and distribute load in situations when failed
				calls always routed to first destination, since they almost
				does not affect load counters of destinations.
- *attrs* (var, optional) - a writable variable 
			to be populated with the attributes of the selected destination.


The function may return:


- *1 (true)* - if a new destination URI is 
			set, pointing to the selected destination. NOTE that the RURI will
			not be changed by this function.
- *-1 (false)* - generic internal error
			(memory allocation, parsing)
- *-2 (false)* - no capacity available 
			(detinations are up and available, but they do not have any 
			availabe channels)
- *-3 (false)* - no destinations available 
			(the requested resources did not match any active destination)
- *-4 (false)* - bad resources 
			(requested resources do not exist)


This function can be used from REQUEST_ROUTE, BRANCH_ROUTE and
		FAILURE_ROUTE.


**Example: lb_start usage**


```opensips
...
if (lb_start(1,"trascoding;conference")) {
	# dst URI points to the new destination
	xlog("sending call to $du\n");
	t_relay();
	exit;
}
...
```


#### lb_next([attrs]) {#func_lb_next}


Function to be used to pull the next available (and less loaded)
		destination. You need to have an ongoing LB session (started with
		lb_start()).


This function is mainly used for implementing failover for the LB
		destinations.


Meaning of the parameters is as follows:


- *attrs* (var, optional) - a writable variable 
			to be populated with the attributes of the selected destination.


The function may return:


- *1 (true)* - if a new destination URI is 
			set, pointing to the selected destination. NOTE that the RURI will
			not be changed by this function.
- *-1 (false)* - generic internal error
			(memory allocation, parsing)
- *-2 (false)* - no capacity available 
			(detinations are up and available, but they do not have any 
			availabe channels)
- *-3 (false)* - no more destinations 
			available (the requested resources did not match any active 
			destination)


This function can be used from REQUEST_ROUTE and FAILURE_ROUTE.


**Example: lb_next() usage**


```opensips
...
if (t_check_status("(408)|(5[0-9][0-9])")) {
	/* check next available LB destination */
	if ( lb_next() ) {
		t_on_failure("1");
		xlog("-----------new dst is $du\n");
		t_relay();
		exit;
	}
}

...
```


#### lb_start_or_next(grp,resources[,flags],[attrs]) {#func_lb_start_or_next}


This is just a wrapper function to simplify scripting. If there is no
		ongoing LB session, it acts as lb_start(); If there is an ongoing LB 
		session, it acts as lb_next().


#### load_balance(grp,resources[,flags],[attrs]) {#func_load_balance}


Old name of the lb_start_or_next() function.


Take care, this will become obsolete.


#### lb_reset() {#func_lb_reset}


Function to stop and flush a current LB session. To be used in 
		failure route, if you want to stop the current LB session (not to try
		any other destinations from this session) and to start a completly new
		one.


This function can be used from REQUEST_ROUTE and FAILURE_ROUTE.


**Example: lb_next() usage**


```opensips
...
if (t_check_status("(5[0-9][0-9])")) {
	/* check next available LB destination */
	if ( lb_next() ) {
		t_on_failure("1");
		xlog("-----------new dst is $du\n");
		t_relay();
		exit;
	}
} else if (t_check_status("(408)")) {
	lb_reset();
	if (lb_start(1,"conference")) {
		t_relay();
		exit;
	}
}
...
```


#### lb_is_started() {#func_lb_is_started}


Function to check if there is any ongoing LB session. Returns true if
		so.


This function can be used in any type of route.


#### lb_disable_dst() {#func_lb_disable_dst}


Marks as disabled the last destination that was used for the current
		call. The disabling done via this function will prevent the 
		destination to be used for usage from now on. The probing mechanism
		can re-enable this peer (see the probing section in the beginning)


This function can be used from REQUEST_ROUTE and FAILURE_ROUTE.


**Example: lb_disable_dst() usage**


```opensips
...
if (t_check_status("(408)|(5[0-9][0-9])")) {
	lb_disable_dst();
	if ( lb_next() ) {
		t_on_failure("1");
		xlog("-----------new dst is $du\n");
		t_relay();
	} else {
		t_reply(500,"Error");
	}
}

...
```


#### lb_is_destination(ip,port,[group],[active],[attrs]]) {#func_lb_is_destination}


Checks if the given IP and PORT belongs to a destination configured in
		the load-balancer's list. Returns true if found and active (see the
		"active" parameter).


This function can be used from REQUEST_ROUTE, FAILURE_ROUTE,
		ONREPLY_ROUTE, BRANCH_ROUTE and LOCAL_ROUTE.


Meaning of the parameters is as follows:


- *ip* (string) - IP to be checked
- *port* (int) - PORT to be checked. 
			A value 0 means "any" - will match any port.
- *group* (int, optional) - in what LB group
			the destination should be looked for; If not specified, the search
			will be in all groups.
- *active*  (int, optional)- if "1", the search will be
			performed only over "active" (not disabled) destinations. If 
			missing, the search will consider any kind of destinations.
- *attrs* (var, optional) - a writable variable 
			to be populated with the attributes of the identified destination.


**Example: lb_is_destination usage**


```
...
if (lb_is_destination($si,$sp) ) {
	# request from a LB destination
}
...
```


#### lb_count_call(ip,port,grp,resources[,undo]) {#func_lb_count_call}


The function counts the current call as load for a given destination 
		with some given resources. Note that this call is not going through
		the load-balancing logic (there are not routing decision taken for the
		call); it is simply counted by LB as ongoing call for a destination;


Meaning of the parameters is as follows:


- *ip* (string) - IP to identify the destination
			the call has to be counted for.
- *port* (int) - PORT to identify the destination
			the call has to be counted for.
- *grp* (int) - group id for the destinations; if
			no knows, "-1" will mean all groups.
- *resources* - (string) a semi-colon separated
			list of resources required by the current call.
- *undo* - (int, optional) if set to a non zero
			value, it will force the function to un-count -
			actually it will undo the counting of this call as load in the 
			current LB session; this might be needed if we count call for
			particular resources and then need to un-count it.


Function returns true if the call was properly taken into consideration
		for estimating the load on the destination.


This function can be used from REQUEST_ROUTE, BRANCH_ROUTE and
		FAILURE_ROUTE.


**Example: lb_count_call usage**


```opensips
...
# count as load also the calls orgininated by lb destinations
if (lb_is_destination($si,$sp) ) {
	# inbound call from destination
	lb_count_call($si,$sp,-1,"conference");
} else {
	# outbound call to destinations
	if ( !load_balance(1,"conference") ) {
		send_reply(503,"unavailable");
		exit();
	}
	# dst URI points to the new destination
	xlog("sending call to $du\n");
	t_relay();
	exit;
}
...
```


### Exported MI Functions {#exported_mi_functions}


#### lb_reload {#mi_lb_reload}


Trigers the reload of the load balancing data from the DB.


MI FIFO Command Format:


```
		opensips-cli -x mi lb_reload
		
```


#### lb_resize {#mi_lb_resize}


Changes the capacity for a resource of a destination.


Parameters:


- *destination_id* - the ID (as per DB) of the destination.
- *res_name* - name of the resource you want to resize.
- *new_capacity* - new resource capacity.


MI FIFO Command Format:


```
		opensips-cli -x mi lb_resize 11 voicemail 56
		
```


#### lb_list {#mi_lb_list}


Lists all the destinations and the maximum and current load for each 
		resource of the destination.


**Example: lb_list usage**


```
$ opensips-cli -x mi lb_list
Destination:: sip:127.0.0.1:5100 id=1 enabled=yes auto-re=on
        Resource:: pstn max=3 load=0
        Resource:: transc max=5 load=1
        Resource:: vm max=5 load=2
Destination:: sip:127.0.0.1:5200 id=2 enabled=no auto-re=on
        Resource:: pstn max=6 load=0
        Resource:: trans max=57 load=0
        Resource:: vm max=5 load=0
```


#### lb_status {#mi_lb_status}


Gets or sets the status (enabled or disabled) of a destination.


Parameters:


- *destination_id* - the ID (as per DB) of the destination.
- *new_status* (optional) - If no new status is given, the
				function will return the current status. If a new status is given 
				(0 - disable, 1 - enable), this status will be forced for the 
				destination.


**Example: lb_status usage**


```
$ opensips-cli -x mi lb_status 2
enable:: no
$ opensips-cli -x mi lb_status 2 1
$ opensips-cli -x mi lb_status 2
enable:: yes
```


### Exported Events {#exported_events}


#### E_LOAD_BALANCER_STATUS {#event_E_LOAD_BALANCER_STATUS}


This event is raised when the module changes the state of a destination,
			either through MI or probing.


Parameters:


- *group* - the group of the destination.
- *uri* - the URI of the destination.
- *status* - *disabled* if
				the destination was disabled or *enabled* if 
				the destination is being used.


## Developer Guide


### Available Functions


NONE


## Frequently Asked Questions


**Q: Where can I find more about OpenSIPS?**


Take a look at [https://opensips.org/](https://opensips.org/).


**Q: Where can I post a question about this module?**


First at all check if your question was already answered on one of
			our mailing lists:

E-mails regarding any stable OpenSIPS release should be sent to 
			users@lists.opensips.org and e-mails regarding development versions
			should be sent to devel@lists.opensips.org.

If you want to keep the mail private, send it to 
			users@lists.opensips.org.


**Q: How can I report a bug?**


Please follow the guidelines provided at:
			[https://github.com/OpenSIPS/opensips/issues](https://github.com/OpenSIPS/opensips/issues).


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 124 | 74 | 4331 | 755 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 43 | 34 | 688 | 172 |
| 3. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 38 | 19 | 623 | 766 |
| 4. | Sergey Khripchenko ([@shripchenko](https://github.com/shripchenko)) | 28 | 10 | 1058 | 516 |
| 5. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 27 | 21 | 266 | 148 |
| 6. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 5 | 3 | 7 | 5 |
| 7. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 5 | 3 | 6 | 7 |
| 8. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | 5 | 3 | 3 | 3 |
| 9. | Jeremy Martinez ([@JeremyMartinez51](https://github.com/JeremyMartinez51)) | 5 | 2 | 193 | 1 |
| 10. | Ezequiel Lovelle ([@lovelle](https://github.com/lovelle)) | 4 | 2 | 3 | 3 |


**All remaining contributors**: Anca Vamanu, Andrei Dragus, James Van Vleet, Dusan Klinec ([@ph4r05](https://github.com/ph4r05)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Zero King ([@l2dy](https://github.com/l2dy)), agree.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Oct 2010 - Oct 2024 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Sep 2012 - Sep 2024 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Feb 2009 - Oct 2023 |
| 4. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | Mar 2017 - Jul 2022 |
| 6. | agree | Jan 2022 - Jan 2022 |
| 7. | Zero King ([@l2dy](https://github.com/l2dy)) | Mar 2020 - Mar 2020 |
| 8. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 9. | Jeremy Martinez ([@JeremyMartinez51](https://github.com/JeremyMartinez51)) | Feb 2017 - Feb 2017 |
| 10. | Dusan Klinec ([@ph4r05](https://github.com/ph4r05)) | Dec 2015 - Dec 2015 |


**All remaining contributors**: Sergey Khripchenko ([@shripchenko](https://github.com/shripchenko)), Ezequiel Lovelle ([@lovelle](https://github.com/lovelle)), Walter Doekes ([@wdoekes](https://github.com/wdoekes)), Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Andrei Dragus, James Van Vleet, Anca Vamanu.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), agree, Zero King ([@l2dy](https://github.com/l2dy)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Sergey Khripchenko ([@shripchenko](https://github.com/shripchenko)), Ezequiel Lovelle ([@lovelle](https://github.com/lovelle)), Walter Doekes ([@wdoekes](https://github.com/wdoekes)), Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)).


*Documentation Copyrights:*


Copyright © 2009 Voice Sistem SRL
