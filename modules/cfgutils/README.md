---
title: "cfgutils Module"
description: "Useful extensions for the server configuration."
---

## Admin Guide


### Overview {#overview}


Useful extensions for the server configuration.


The cfgutils module can be used to introduce randomness to
	the behaviour of the server. It provides setup functions
	and the "rand_event" function. This function return either
	true or false, depending on a random value and a specified probability.
	E.g. if you set via fifo or script a probability value of 5%, then 5% of
	all calls to rand_event will return false.
	The pseudovariable "$RANDOM" could be used to introduce 
	random values e.g. into a SIP reply.


The benefit of this module is the probability of the decision
	can be manipulated by external applications such as web interface
	or command line tools. The probability must be specified as 
	percent value, ranging from 0 to 100.


The module exports commands to FIFO server that can be used to change
	the global settings via FIFO interface. The FIFO commands are:
	"set_prob", "reset_prob" and
	"get_prob".


This module can be used for simple load-shedding, e.g. reply 5% of
	the Invites with a 503 error and a adequate random Retry-After value.


The module provides as well functions to delay the execution of the
	server. The functions "sleep" and "usleep" could
	be used to let the server wait a specific time interval.


It can also hash the config file used from the server with a (weak)
	cryptographic hash function on startup. This value is saved and can be
	later compared to the actual hash, to detect modifications of this file
	after the server start. This functions are available as the FIFO commands
	"check_config_hash" and "get_config_hash".


### Dependencies {#dependencies}


The module depends on the following modules (in the other words the
		listed modules must be loaded before this module):


- *none*


### Exported Parameters {#exported_parameters}


#### initial_probability (string) {#param_initial_probability}


The initial value of the probability.


Default value is 
			"10".


**Example: initial_probability parameter usage**


```opensips
   
modparam("cfgutils", "initial_probability", 15)
   
```


#### hash_file (string) {#param_hash_file}


The config file name for that a hash value should be calculated on startup.


There is no default value, is no parameter is given the hash functionality
		is disabled.


**Example: hash_file parameter usage**


```opensips
   
modparam("cfgutils", "hash_file", "/etc/opensips/opensips.cfg")
   
```


#### shv_hash_size (integer) {#param_shv_hash_size}


The size of the hash table used to store the shared variables ($shv).


Default value is "64".


**Example: shv_hash_size parameter usage**


```opensips
modparam("cfgutils", "shv_hash_size", 1024)
```


#### shvset (string) {#param_shvset}


Set the value of a shared variable ($shv(name)). The parameter
		can be set many times.


The value of the parameter has the format:
		_name_ '=' _type_ ':' _value_


- _name_: shared variable name
- _type_: type of the value

  - "i": integer value
  - "s": string value
- _value_: value to be set


Default value is "NULL".


**Example: shvset parameter usage**


```opensips
...
modparam("cfgutils", "shvset", "debug=i:1")
modparam("cfgutils", "shvset", "pstngw=s:sip:10.10.10.10")
...
```


#### varset (string) {#param_varset}


Set the value of a script variable ($var(name)). The parameter
		can be set many times.


The value of the parameter has the format:
		_name_ '=' _type_ ':' _value_


- _name_: shared variable name
- _type_: type of the value

  - "i": integer value
  - "s": string value
- _value_: value to be set


Default value is "NULL".


**Example: varset parameter usage**


```opensips
...
modparam("cfgutils", "varset", "init=i:1")
modparam("cfgutils", "varset", "gw=s:sip:11.11.11.11;transport=tcp")
...
```


#### lock_pool_size (integer) {#param_lock_pool_size}


The number of dynamic script locks to be allocated at OpenSIPS startup. This
		number must be a power of 2. (i.e. 1, 2, 4, 8, 16, 32, 64 ...)


Note that the *lock_pool_size* parameter only affects
		the number of dynamic locks created at startup. The pool of static locks
		only depends on the number of unique static strings supplied throughout
		the script to the set of static lock functions.


Default value is "32".


**Example: Setting lock_pool_size module parameter**


```opensips
modparam("cfgutils", "lock_pool_size", 64)
```


### Exported Functions {#exported_functions}


#### rand_event([probability]) {#func_rand_event}


Generates a random floating point value between 0 - 100 and returns
			true if the value is less or equal to the currently set probability.
			If "probability" parameter is given, it will
			override the global parameter set by [rand set prob](#func_rand_set_prob).


Parameters:


- probability (int, optional) - probability override


**Example: rand_event() usage**


```opensips
...
if (rand_event()) {
  append_to_reply("Retry-After: 120\n");
  sl_send_reply(503, "Try later");
  exit;
}
# normal message processing follows
...
```


#### rand_set_prob(probability) {#func_rand_set_prob}


Set the "probability" of the decision.


Parameters:


- probability (int) - number ranging from 0 - 99, inclusively


**Example: rand_set_prob() usage**


```
...
rand_set_prob(4);
...
```


#### rand_reset_prob() {#func_rand_reset_prob}


Reset the probability back to the
			[initial probability](#param_initial_probability) value.


**Example: rand_reset_prob() usage**


```
...
rand_reset_prob();
...
```


#### rand_get_prob() {#func_rand_get_prob}


Return the current probability setting, e.g. for logging purposes.


**Example: rand_get_prob() usage**


```
...
rand_get_prob();
   
```


#### sleep(time) {#func_sleep}


Waits "time" seconds.


Meaning of the parameters is as follows:


- *time (int)* - time to wait in seconds


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
			FAILURE_ROUTE, BRANCH_ROUTE.


**Example: sleep usage**


```
...
sleep(1);
...
$var(secs) = 10;
sleep($var(secs));
...
			
```


#### usleep(time) {#func_usleep}


Waits "time" micro-seconds.


Meaning of the parameters is as follows:


- *time (int)* - time to wait in micro-seconds


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
			FAILURE_ROUTE, BRANCH_ROUTE.


**Example: usleep usage**


```
...
usleep(500000); # sleep half a sec
...
			
```


#### abort() {#func_abort}


Debugging function that aborts the server. Depending on the
			configuration of the server a core dump will be created.


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
			FAILURE_ROUTE, BRANCH_ROUTE.


**Example: abort usage**


```
...
abort();
...
			
```


#### pkg_status() {#func_pkg_status}


Debugging function that dumps the status for the private (PKG) memory.
			This information is logged to the default log facility, depending on
			the general log level and the memlog setting. You need to compile
			the server with activated memory debugging to get detailed informations.


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
			FAILURE_ROUTE, BRANCH_ROUTE.


**Example: pkg_status usage**


```
...
pkg_status();
...
			
```


#### shm_status() {#func_shm_status}


Debugging function that dumps the status for the shared (SHM) memory.
			This information is logged to the default log facility, depending on
			the general log level and the memlog setting. You need to compile
			the server with activated memory debugging to get detailed informations.


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
			FAILURE_ROUTE, BRANCH_ROUTE.


**Example: shm_status usage**


```
...
shm_status();
...
			
```


#### set_count(var_to_count, ret_var) {#func_set_count}


Counts the number of values of a given variable.
			It makes sense to call this function only for variables that can
			take more values (AVPs, headers).


The result is returned in the second parameter.


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
			FAILURE_ROUTE, BRANCH_ROUTE.


**Example: set_count usage**


```
...
set_count($avp(dids), $var(num_dids));
...
			
```


#### set_select_weight(int_list_var) {#func_set_select_weight}


This function selects an element from a set formed by the integer
			values of the given "int_list_var" variable. It applies the genetic
			algorithm - roulette-wheel selection to choose an element from a set.
			The probability of selecting a certain element is proportionate with
			its weight. It will return the index of that selected element.


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
			FAILURE_ROUTE, BRANCH_ROUTE.


**Example: set_select_weight usage**


```
...
$var(next_gw_idx) = set_select_weight($avp(gw_success_rates));
...
			
```


#### ts_usec_delta(t1_sec, t1_usec, t2_sec, t2_usec, [delta_str], [delta_int]) {#func_ts_usec_delta}


This function returns the absolute difference between the two given
			timestamps. The result is expressed as *microseconds*
			and can be returned as either string or integer.


**WARNING:** when using
			*delta_int*, the function will return error code
			**-1** in case the difference overflows
			the signed integer holder! (i.e. a diff of ~35 minutes or more)


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
			FAILURE_ROUTE, BRANCH_ROUTE.


**Example: ts_usec_delta usage**


```
...
ts_usec_delta($var(t1s), 300, 10, $var(t2us), $var(diff_str));
...
			
```


#### check_time_rec(time_string, [timestamp]) {#func_check_time_rec}


The function returns a positive value if the specified time recurrence string
		matches the current time, or a negative value otherwise.


For checking some other Unix timestamp than the current one, the second
		parameter will contain the intended timestamp to check.


The syntax of each field is identical to the corresponding field from
		RFC 2445.


This function may be used from any route.  It returns 1 on success
			and -1, -2 or -3 on failure, parsing or internal errors, respectively.


Meaning of the parameters is as follows:


- *time_string (string)* - Time recurrence string which
			will be matched against the current time. Its fields are separated by "|" and
			the order in which they are given is: "timezone | dtstart | dtend | duration | freq
			| until | interval | byday | bymday | byyday | byweekno | bymonth".
None of the fields following "freq" is used unless
			"freq" is defined.  If the string ends in multiple null fields,
			they can all be ommited.
The "timezone" field is optional.  It represents the timezone in
			which to interpret the time recurrence elements (e.g. dtstart,
			dtend, until).  By default, the system time zone is used.
- *timestamp (string, optional)* - A
			specific Unix time to check.  The function simply expects the
			actual Unix time here, there is no need to perform any timezone
			adjustments.


Additionally, more complex time recurrence strings may be built by
		connecting multiple time recurrence strings (described above) using
		the logical AND ("&"), OR ("/") and NEG ("!") operators.
		Furthermore, the expressions may be paranthesized.  Some examples:


- 20210104T080000|20211231T180000||WEEKLY|||MO,TU,WE,TH,FR
					&
				!20210104T120000|20211231T140000||WEEKLY|||MO,TU,WE,TH,FR
This example multi-recurrence expresses the working days schedule for
				company X during 2021:  workdays from 8-18, except the 12-14 interval,
				when everyone is out for lunch break and the business is closed.
				Since the timezone is omitted from each schedule, the operating
				system timezone will be used instead.
- America/New_York|20210104T090000|20210104T170000||WEEKLY|||MO,TU,WE,TH,FR
					&
				!(Europe/Amsterdam|20210427T000000|20210428T000000 / Europe/London|20211227T000000|20211228T000000)
This example multi-recurrence expresses the working days schedule for
				US-based company Y during 2021:  workdays from 9-17 (NY timezone),
				except european holidays such as King's Day (April 27th, NL) or
				the Spring Bank Holiday (May 31st, UK), when most of its
				workforce will have flown back to Europe.


**Example: check_time_rec usage**


```opensips
...
# Only passing if still in 2012 and on a Bucharest-compatible timezone
if (check_time_rec("Europe/Bucharest|20120101T000000|20130101T000000"))
	xlog("Current system time matches the given Romanian time interval\n");
...
# Only passing if less than 30 days have passed from "dtstart", system timezone
if (check_time_rec("20121101T000000||p30d"))
	xlog("Current time matches the given interval\n");
...
			
```


#### get_static_lock(key) {#func_get_static_lock}


Acquire the static lock which corresponds to "key".  In case the
		lock is taken by another process, script execution will halt until the
		lock is released.  Attempting to acquire the lock a second time by the
		same process, without releasing it first, will result in a deadlock.


The static lock functions guarantee that two different strings will never
		point to the same lock, thus avoiding introducing unnecessary
		(and transparent!) synchronization between processes. Their disadvantage is
		the nature of their parameters (static strings), making them inappropriate in
		certain scenarios.


Meaning of the parameters is as follows:


- *key (static string)* - key to be hashed in
				order to obtain the index of a static lock


This function can be used from REQUEST_ROUTE, FAILURE_ROUTE, ONREPLY_ROUTE, 
		BRANCH_ROUTE, LOCAL_ROUTE, STARTUP_ROUTE, TIMER_ROUTE, EVENT_ROUTE.


**Example: get_static_lock usage**


```
# acquire and release a static lock 
...
get_static_lock("Zone_1");
...
release_static_lock("Zone_1");
...
```


#### release_static_lock(key) {#func_release_static_lock}


Release the static lock corresponding to "key". Nothing will happen if
		the lock is not acquired.


Meaning of the parameters is as follows:


- *key (static string)* - key to be hashed in
				order to obtain the index of a static lock.


This function can be used from REQUEST_ROUTE, FAILURE_ROUTE, ONREPLY_ROUTE, 
		BRANCH_ROUTE, LOCAL_ROUTE, STARTUP_ROUTE, TIMER_ROUTE|EVENT_ROUTE.


**Example: release_static_lock usage**


```
# acquire and release a static lock 
...
get_static_lock("Zone_1");
...
release_static_lock("Zone_1");
...
```


#### get_dynamic_lock(key) {#func_get_dynamic_lock}


Acquire the dynamic lock corresponding to "key".  In case the lock is
		taken by another process, script execution will halt until the lock is
		released.  Attempting to acquire the lock a second time by
		the same process, without releasing it first, will result in a deadlock.


The dynamic lock functions have the advantage of allowing string
		variables to be given as parameters, but the drawback to this is that
		two strings may have the same hashed value, thus pointing to the same lock.
		As a consequence, either two totally separate regions of the script will be
		synchronized (they will not execute in parallel), or a process could end up
		in a deadlock by acquiring two locks in a row on two different (but equally
		hashed) strings. To address the latter issue, use the
		[strings share lock](#func_strings_share_lock) function to test if two
		strings hash into the same dynamic lock.


Meaning of the parameters is as follows:


- *key (var)* - key to be hashed in order to
			obtain the index of a dynamic lock from the pool


This function can be used from REQUEST_ROUTE, FAILURE_ROUTE, ONREPLY_ROUTE, 
		BRANCH_ROUTE, LOCAL_ROUTE, STARTUP_ROUTE, TIMER_ROUTE|EVENT_ROUTE.


**Example: get_dynamic_lock usage**


```opensips
...
# acquire and release a dynamic lock on the "Call-ID" header field value
if (!get_dynamic_lock($ci)) {
	xlog("Error while getting dynamic lock!\n");
}
...
if (!release_dynamic_lock($ci) {
	xlog("Error while releasing dynamic lock!\n");
}
...
```


#### release_dynamic_lock(key) {#func_release_dynamic_lock}


Release the dynamic lock corresponding to "key".  Nothing will happen
		if the lock is not acquired.


Meaning of the parameters is as follows:


- *key (var)* - key to be hashed in order to
			obtain the index of a dynamic lock from the pool


This function can be used from REQUEST_ROUTE, FAILURE_ROUTE, ONREPLY_ROUTE, 
		BRANCH_ROUTE, LOCAL_ROUTE, STARTUP_ROUTE, TIMER_ROUTE|EVENT_ROUTE.


**Example: release_dynamic_lock usage**


```opensips
...
# acquire and release a dynamic lock on the "Call-ID" header field value
if (!get_dynamic_lock($ci)) {
	xlog("Error while getting dynamic lock!\n");
}
...
if (!release_dynamic_lock($ci) {
	xlog("Error while releasing dynamic lock!\n");
}
...
```


#### strings_share_lock(key1, key2) {#func_strings_share_lock}


A function used to test if two strings will generate the same hash value.
		Its purpose is to prevent deadlocks resulted when a process successively
		acquires two dynamic locks on two strings which happen to point to the same
		lock.


Theoretically, the chance of two strings generating the same hash value 
		decreases proportionally to the increase of the
		[lock pool size](#param_lock_pool_size) parameter. In
		other words, the more dynamic locks you configure the module with, the higher
		the chance that all individual protected regions of your script will run in
		parallel, without waiting for each other.


Meaning of the parameters is as follows:


- *key1, key2 (string)* - strings which will have
			their hash values compared


This function can be used from REQUEST_ROUTE, FAILURE_ROUTE, ONREPLY_ROUTE, 
		BRANCH_ROUTE, LOCAL_ROUTE, STARTUP_ROUTE, TIMER_ROUTE|EVENT_ROUTE.


**Example: strings_share_lock usage**


```opensips
...
# Proper way of acquiring two dynamic locks successively
if (!get_dynamic_lock($avp(foo))) {
	xlog("Error while getting dynamic lock!\n");
}

if (!strings_share_lock($avp(foo), $avp(bar)) {
	if (!get_dynamic_lock($avp(bar))) {
		xlog("Error while getting dynamic lock!\n");
	}
}
...
if (!strings_share_lock($avp(foo), $avp(bar)) {
	if (!release_dynamic_lock($avp(bar)) {
		xlog("Error while releasing dynamic lock!\n");
	}
}

if (!release_dynamic_lock($avp(foo)) {
	xlog("Error while releasing dynamic lock!\n");
}
...
```


#### get_accurate_time(sec, usec, [str_sec_usec]) {#func_get_accurate_time}


Fetch the current Unix time epoch with microsecond precision.
		Optionally, print this value as a floating point number (3rd parameter).


Meaning of the parameters is as follows:


- *sec (int)* - the current Unix timestamp (integer part)
- *usec (int)* - the current Unix timestamp (decimal part)
- *str_sec_usec (string, optional)* - the current Unix
					timestamp as a floating point number (6-digit precision)


This function can be used from REQUEST_ROUTE, FAILURE_ROUTE, ONREPLY_ROUTE,
		BRANCH_ROUTE, LOCAL_ROUTE, STARTUP_ROUTE, TIMER_ROUTE, EVENT_ROUTE.


**Example: get_accurate_time usage**


```opensips
...
get_accurate_time($var(sec), $var(usec));
xlog("Current Unix timestamp: $var(sec) s, $var(usec) us\n");
...
```


#### shuffle_avps(name)
				 {#func_shuffle_avps}


Randomly shuffles AVPs with *name*.


Meaning of the parameters is as follows:


- *name (variable)* - name of AVP to shuffle.


This function can be used from REQUEST_ROUTE, FAILURE_ROUTE,
			BRANCH_ROUTE, LOCAL_ROUTE and ONREPLY_ROUTE.


**Example: shuffle_avps usage**


```opensips
...
$avp(foo) := "str1";
$avp(foo)  = "str2";
$avp(foo)  = "str3";
xlog("Initial AVP list is: $(avp(foo)[*])\n");       # str3 str2 str1
if(shuffle_avps( $avp(foo) ))
    xlog("Shuffled AVP list is: $(avp(foo)[*])\n");  # str1, str3, str2 (for example)
...
				
```


### Exported Asyncronous Functions


#### sleep(seconds) {#afunc_sleep}


Waits a number of seconds. This function does exactly the same as
		[sleep](#func_sleep),
		but in an asynchronous way. The script execution is suspended until
		the waiting is done; then OpenSIPS resumes the script execution via 
		the resume route.


To read and understand more on the asynchronous functions, how to 
		use them and what are their advantages, please refer to the OpenSIPS
		online Manual.


**Example: async sleep usage**


```
{
...
async( sleep("5"), after_sleep );
}

route[after_sleep] {
...
}
```


#### usleep(seconds) {#afunc_usleep}


Waits a number of micro-seconds. This function does exactly the same as
		[usleep](#func_usleep),
		but in an asynchronous way. The script execution is suspended until
		the waiting is done; then OpenSIPS resumes the script execution via 
		the resume route.


To read and understand more on the asynchronous functions, how to 
		use them and what are their advantages, please refer to the OpenSIPS
		online Manual.


**Example: async usleep usage**


```
{
...
async( usleep("1000"), after_usleep );
}

route[after_usleep] {
...
}
```


### Exported MI Functions {#exported_mi_functions}


#### rand_set_prop {#mi_rand_set_prop}


Set the probability value to the given parameter.


Parameters:


- *prob_proc* - the parameter should be
					a percent value (number from 0 to 99).


**Example: rand_set_prob usage**


```
...
$ opensips-cli -x mi rand_set_prob 10
...
```


#### rand_reset_prob {#mi_rand_reset_prob}


Reset the probability value to the inital start value.


This command don't need a parameter.


**Example: rand_reset_prob usage**


```
...
$ opensips-cli -x mi rand_reset_prob
...
```


#### rand_get_prob {#mi_rand_get_prob}


Return the actual probability setting.


The function return the actual probability value.


**Example: rand_get_prob usage**


```
...
$ opensips-cli -x mi get_prob
The actual probability is 50 percent.
...
```


#### check_config_hash {#mi_check_config_hash}


Check if the actual config file hash is identical to the stored one.


The function returns 200 OK if the hash values are identical, 400 if
				there are not identical, 404 if no file for hashing has been configured
				and 500 on errors. Additional a short text message is printed.


**Example: check_config_hash usage**


```
...
$ opensips-cli -x mi check_config_hash
The actual config file hash is identical to the stored one.
...
```


#### get_config_hash {#mi_get_config_hash}


Return the stored config file hash.


The function returns 200 OK and the hash value on success or 404 if no
				file for hashing has been configured.


**Example: get_config_hash usage**


```
...
$ opensips-cli -x mi get_config_hash
1580a37104eb4de69ab9f31ce8d6e3e0
...
```


#### shv_set {#mi_shv_set}


Set the value of a shared variable ($shv(name)).


Parameters:


- *name* : shared variable name
- *type* : type of the value

  - "int": integer value
  - "str": string value
- *value* : value to be set


**Example: shv_set usage**


```
...
$ opensips-cli -x mi shv_set debug int 0
...
```


#### shv_get {#mi_shv_get}


Get the value of a shared variable ($shv(name)).


Parameters:


- *name* : shared variable name. If this parameter
			is missing, all shared variables are returned.


**Example: shv_get usage**


```
...
$ opensips-cli -x mi shv_get debug
$ opensips-cli -x mi shv_get
...
```


### Exported Pseudo-Variables {#exported_pseudo_variables}


#### $env(name) {#pv_env}


This PV provides access to the environment variable 'name'.


**Example: env(name) pseudo-variable usage**


```opensips
...
xlog("PATH environment variable is $env(PATH)\n");
...
				 
```


#### $RANDOM {#pv_RANDOM}


Returns a random value from the [0 - 2^31) range.


**Example: RANDOM pseudo-variable usage**


```opensips
...
$avp(10) = ($RANDOM / 16777216); # 2^24
if ($avp(10) < 10) {
   $avp(10) = 10;
}
append_to_reply("Retry-After: $avp(10)\n");
sl_send_reply(503, "Try later");
exit;
# normal message processing follows
   
				 
```


#### $ctime(name) {#pv_ctime}


The PV provides access to broken-down time attributes.


The "name" can be:


- *sec* - return seconds (int 0-59)
- *min* - return minutes (int 0-59)
- *hour* - return hours (int 0-23)
- *mday* - return the day of month (int 0-59)
- *mon* - return the month (int 1-12)
- *year* - return the year (int, e.g., 2008)
- *wday* - return the day of week (int, 1=Sunday - 7=Saturday)
- *yday* - return the day of year (int, 1-366)
- *isdst* - return daylight saving time status (int, 0 - DST off, >0 DST on)


**Example: ctime(name) pseudo-variable usage**


```opensips
...
if ($ctime(year) == 2008) {
	xlog("request: $rm from $fu to $ru in year 2008\n");
}
...
				 
```


#### $shv(name) {#pv_shv}


It is a class of pseudo-variables stored in shared memory. The
				value of $shv(name) is visible across all opensips processes.
				Each "shv" has single value and it is initialized
				to integer 0. You can use "shvset" parameter to
				initialize the shared variable. The module exports a set of MI
				functions to get/set the value of shared variables.


**Example: shv(name) pseudo-variable usage**


```opensips
...
modparam("cfgutils", "shvset", "debug=i:1")
...
if ($shv(debug) == 1) {
	xlog("request: $rm from $fu to $ru\n");
}
...
				 
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 94 | 44 | 2966 | 1512 |
| 2. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 30 | 18 | 1088 | 86 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 29 | 23 | 462 | 109 |
| 4. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 21 | 7 | 357 | 548 |
| 5. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 20 | 15 | 289 | 79 |
| 6. | Elena-Ramona Modroiu | 14 | 3 | 1183 | 7 |
| 7. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 12 | 10 | 84 | 32 |
| 8. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 7 | 5 | 24 | 18 |
| 9. | Anca Vamanu | 6 | 3 | 220 | 14 |
| 10. | Ionel Cerghit ([@ionel-cerghit](https://github.com/ionel-cerghit)) | 5 | 1 | 18 | 161 |


**All remaining contributors**: Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Sergio Gutierrez, Konstantin Bokarius, Walter Doekes ([@wdoekes](https://github.com/wdoekes)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Edson Gellert Schubert, Alexandra Titoc.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Alexandra Titoc | Sep 2024 - Sep 2024 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Sep 2012 - May 2024 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jul 2007 - Feb 2024 |
| 4. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Dec 2015 - Nov 2023 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - May 2023 |
| 6. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Oct 2010 - Jan 2020 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 8. | Ionel Cerghit ([@ionel-cerghit](https://github.com/ionel-cerghit)) | Dec 2015 - Dec 2015 |
| 9. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | Jan 2015 - Jan 2015 |
| 10. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Jan 2013 - Jul 2014 |


**All remaining contributors**: Anca Vamanu, Sergio Gutierrez, Henning Westerholt ([@henningw](https://github.com/henningw)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Elena-Ramona Modroiu, Konstantin Bokarius, Edson Gellert Schubert.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Anca Vamanu, Sergio Gutierrez, Henning Westerholt ([@henningw](https://github.com/henningw)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Elena-Ramona Modroiu, Konstantin Bokarius, Edson Gellert Schubert.


*Documentation Copyrights:*


Copyright © 2007-2008 1und1 Internet AG


Copyright © 2007-2008 BASIS AudioNet GmbH


Copyright © 2007-2008 Elena-Ramona Modroiu
