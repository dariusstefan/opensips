---
title: "mqueue Module"
description: "The mqueue module offers a generic message queue system in shared memory for inter-process communication using the config file. One example of usage is to send time consuming operations to one or several timer processes that consumes items in the queue, without affecting SIP message handl..."
---

## Admin Guide


### Overview {#overview}


The mqueue module offers a generic message queue system in shared
		memory for inter-process communication using the config file.
		One example of usage is to send time consuming operations to one or
		several timer processes that consumes items in the queue, without
		affecting SIP message handling in the socket-listening process.


There can be many defined queues. Access to queued values is done via
		pseudo variables.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *None*.


#### External Libraries or Applications


The following libraries or applications must be installed before
		running OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### db_url (str) {#param_db_url}


The URL to connect to database for loading values
		in mqueue table at start up and/or saving values at shutdown.


*Default value is NULL (do not connect).*


```c title="Set db_url parameter"
...
modparam("mqueue", "db_url", "mysql://opensips:opensipsrw@localhost/opensips")

# Example of table in sqlite,
# you have the set the fields to support the length according
# to the data that will be present in the mqueue
CREATE TABLE mqueue_name (
id INTEGER PRIMARY KEY AUTOINCREMENT,
key character varying(64) DEFAULT "" NOT NULL,
val character varying(4096) DEFAULT "" NOT NULL
);
...
```


#### mqueue (string) {#param_mqueue}


Definition of a memory queue


*Default value is "none".*


Value must be a list of parameters: attr=value;...


- Mandatory attributes:

  - *name*: name of the queue.
- Optional attributes:

  - *size*: size of the queue.
				Specifies the maximum number of items in queue.
				If exceeded the oldest one is removed.
				If not set the queue will be limitless.
  - *dbmode*: If set to 1, the content of the
				queue is written to database table when the SIP server is
				stopped (i.e., ensure persistency over restarts).
				If set to 2, it is written at shutdown but not read at startup.
				If set to 3, it is read at sartup but not written at shutdown.
				Default value is 0 (no db table interaction).
  - *addmode*: how to add new (key,value) pairs.
					
					
						*0*:
						Will push all new (key,value) pairs at the end of
						the queue. (default)
					
					
						*1*:
						Will keep oldest (key,value) pair in the queue,
						based on the key.
					
					
						*2*:
						Will keep newest (key,value) pair in the queue,
						based on the key.


The parameter can be set many times, each holding the
		definition of one queue.


```c title="Set mqueue parameter"
...
modparam("mqueue", "mqueue", "name=myq;size=20;")
modparam("mqueue", "mqueue", "name=myq;size=10000;addmode=2")
modparam("mqueue", "mqueue", "name=qaz")
modparam("mqueue", "mqueue", "name=qaz;addmode=1")
...
```


### Exported Functions {#exported_functions}


#### mq_add(queue, key, value) {#func_mq_add}


Add a new item (key, value) in the queue. If max size of queue is
		exceeded, the oldest one is removed.


```c title="mq_add usage"
...
mq_add("myq", "$rU", "call from $fU");
...
```


#### mq_fetch(queue) {#func_mq_fetch}


Take oldest item from queue and fill $mqk(queue) and
		$mqv(queue) pseudo variables.


Return: true on success (1); false on failure (-1) or
		no item fetched (-2).


```c title="mq_fetch usage"
...
while(mq_fetch("myq"))
{
	xlog("$mqk(myq) - $mqv(myq)\n");
}
...
```


#### mq_pv_free(queue) {#func_mq_pv_free}


Free the item fetched in pseudo-variables. It is optional,
		a new fetch frees the previous values.


```c title="mq_pv_free usage"
...
mq_pv_free("myq");
...
```


#### mq_size(queue) {#func_mq_size}


Returns the current number of elements in the mqueue.


If the mqueue is empty, the function returns -1. If the
		mqueue is not found, the function returns -2.


```c title="mq_size usage"
...
$var(q_size) = mq_size("queue");
xlog("L_INFO", "Size of queue is: $var(q_size)\n");
...
```


### Exported MI Functions {#exported_mi_functions}


#### mq_get_size {#mi_mq_get_size}


Get the size of a memory queue.


Parameters:


- name


```c title="mq_get_size usage"
...
opensips-cli -x mq_get_size xyz
...
```


#### mq_fetch {#mi_mq_fetch}


Fetch one (or up to limit) key-value pair from a memory queue.


Parameters:


- name
- limit
limit


```c title="mq_fetch usage"
...
opensips-cli -x mq_fetch xyz
...
```


#### mq_get_sizes {#mi_mq_get_sizes}


Get the size for all memory queues.


Parameters: none


```c title="mq_get_sizes usage"
...
opensips-cli -x mq_get_sizes
...
```


### Exported Pseudo-Variables {#exported_pseudo_variables}


#### $mqk(mqueue) {#pv_mqk}


The variable is read-only and returns the most recent item key
			fetched from the specified mqueue.


#### $mqv(mqueue) {#pv_mqv}


The variable is read-only and returns the most recent item value
			fetched from the specified mqueue.


#### $mq_size(mqueue) {#pv_mq_size}


The variable is read-only and returns the size of the specified
			mqueue.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 19 | 2 | 1843 | 34 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 3 | 1 | 75 | 16 |
| 3. | Alexandra Titoc | 3 | 1 | 13 | 9 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Feb 2025 - Feb 2025 |
| 2. | Alexandra Titoc | Sep 2024 - Sep 2024 |
| 3. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Feb 2024 - Feb 2024 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)).
