---
title: "SST Module (SIP Session Timer)"
description: "The sst module provides a way to update the dialog expire timer based on the SIP INVITE/200 OK Session-Expires header value. You can use the sst module in an OpenSIPS proxy to allow freeing of local resources of dead (expired) calls."
---

## Admin Guide


### Overview {#overview}


The sst module provides a way to update the
		dialog expire timer based on the SIP INVITE/200 OK
		Session-Expires header value. You can use the sst
		module in an OpenSIPS proxy to allow freeing of local
		resources of dead (expired) calls.


You can also use the sst module to validate the
		MIN_SE header value and reply to any request with a
		"422 - Session Timer Too Small" if the value is too
		small for your OpenSIPS configuration.


### How it works


The sst module uses the dialog module to be notified of
	any new or updated dialogs. It will then look for and extract
	the session-expire: header value (if there is one) and
	override the dialog expire timer value for the current context
	dialog.


You flag any call setup INVITE that you want to cause a
	timed session to be established. This will cause OpenSIPS to
	request the use of session times if the UAC does not request
	it.


All of this happens with a properly configured dialog
	and sst module and setting the dialog flag and the sst flag at
	the time any INVITE sip message is seen. There is no
	opensips.cfg script function call required to set the dialog
	expire timeout value. See the dialog module users guide for
	more information.


The sstCheckMin() script function can be used to varify
	the Session-expires / MIN-SE header field values are not too
	small for a proxy. If the SST min_se parameter value is
	smaller then the messages Session-Expires / MIN-SE values, the
	test will return true. You can also configure the function to
	send the 422 response for you.


The following was taken from the RFC as a call flow
	example:


```c title="Session timer call flow"
+-------+    +-------+       +-------+
| UAC-1 |    | PROXY |       | UAC-2 |
+-------+    +-------+       +-------+
    |(1) INVITE  |               |
    |SE: 50      |               |
    |----------->|               |
    |            |(2)sstCheckMin |
    |            |-----+         |
    |            |     |         |
    |            |<----+         |
    |(3) 422     |               |
    |MSE:1800    |               |
    |<-----------|               |
    |            |               |
    |(4)ACK      |               |
    |----------->|               |
    |            |               |
    |(5) INVITE  |               |
    |SE: 1800    |               |
    |MSE: 1800   |               |
    |----------->|               |
    |            |(6)sstCheckMin |
    |            |-----+         |
    |            |     |         |
    |            |<----+         |
    |            |(7)setflag     |
    |            |Dialog flag    |
    |            |Set expire     |
    |            |-----+         |
    |            |     |         |
    |            |<----+         |
    |            |               |
    |            |(8)INVITE      |
    |            |SE: 1800       |
    |            |MSE: 1800      |
    |            |-------------->|
    |            |               |
 ...
     			
```


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded
		before this module:


- *dialog* - dialog module and its decencies. (tm)
- *sl* - stateless module.


#### External Libraries or Applications


The following libraries or applications must be installed before running
	OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### enable_stats (integer) {#param_enable_stats}


If the statistics support should be enabled or
		not. Via statistic variables, the module provide
		information about the dialog processing. Set it to zero to
		disable or to non-zero to enable it.


*Default value is "1" (enabled).*


```c title="Set enable_stats parameter"
...
modparam("sst", "enable_stats", 0)
...
```


#### min_se (integer) {#param_min_se}


The value is used to set the proxies MIN-SE
		value and is used in the 422 reply as the proxies
		MIN-SE: header value if the sstCheckMin() flag is set
		to true and the check fails.


If not set and sstCheckMin() is called with the
		send-reply flag set to true, the default 1800 seconds
		will be used as the compare and the MIN-SE: header
		value if the 422 reply is sent.


*Default value is "1800" seconds.*


```c title="Set min_se parameter"
...
modparam("sst", "min_se", 2400)
...
```


#### sst_interval (integer) {#param_sst_interval}


The sst minimum interval in Session-Expires header if OpenSIPS
		request the use of session times. The used value will be the
		maximum value between OpenSIPS minSE, UAS minSE and this value.


Per default the interval used will be the min_se value


*Default value is "0" seconds.*


```c title="Set sst_interval parameter"
...
modparam("sst", "sst_interval", 2400)
...
```


#### reject_to_small (integer) {#param_reject_to_small}


In the initial INVITE if the UAC has requested a
		Session-Expire: and it's value is smaller then our
		local policies Min-SE (see min_se above), then the
		PROXY has the right to reject the call by replying to
		the message with a 422 Session Timer Too Small and
		state our local Min-SE: value. The INVITE is NOT
		forwarded on through the PROXY.


This flag if true will tell the SST module to
		reject the INVITE with a 422 response. If false, the
		INVITE is forwarded through the PROXY with out any
		modifications.


*Default value is "1" (true/on).*


```c title="Set reject_to_small parameter"
...
modparam("sst", "reject_to_small", 0)
...
```


#### sst_flag (string) {#param_sst_flag}


Keeping with OpenSIPS, the module will not do
		anything to any message unless instructed to do so via
		the opensips.cfg script. You must set the sst_flag
		value in the setflag() call of the INVITE you want the
		sst module to process. But before you can do that, you
		need to tell the sst module which flag value you are
		assigning to sst.


In most cases when ever you create a new dialog
		via create_dialog() function,you will want to set the sst flag. 
		If create_dialog() is not called and the sst flag is set, 
		it will not have any effect.


This parameter must be set of the module will
		not load.


*Default value is "Not set!".*


```c title="Set sst_flag parameter"
...
modparam("sst", "sst_flag", "SST_FLAG")
...
route {
  ...
  if ($rm=="INVITE") {
    setflag(SST_FLAG); # Set the sst flag
    create_dialog(); # and then create the dialog
  }
  ...
}
```


### Exported Functions {#exported_functions}


#### sstCheckMin(send_reply_flag) {#func_sstCheckMin}


Check the current Session-Expires / MIN-SE values
		against the sst_min_se parameter value. If the
		Session-Expires or MIN_SE header value is less then
		modules minimum value, this function will return
		true.


If the fuction is called with the
		send_reply_flag set to true (1) and the requested
		Session-Expires / MIN-SE values are too small, a 422
		reply will be sent for you. The 422 will carry a
		MIN-SE: header with the sst min_se parameter value
		set.


Meaning of the parameters is as follows:


- *min_allowed* (int, optional) - The value
			to compare the MIN_SE header value to.


```c title="sstCheckMin usage"
...
modparam("sst", "sst_flag", 6)
modparam("sst", "min_se", 2400) # Must be >= 90
...

route {
  if ($rm=="INVITE") {
	if (sstCheckMin(1)) {
		xlog("L_ERR", "422 Session Timer Too Small reply sent.\n");
		exit;
	}
	# track the session timers via the dialog module
	setflag(6);
	create_dialog();
  }
}

... or ...

route {
  if ($rm=="INVITE") {
	if (sstCheckMin(0)) {
		xlog("L_ERR", "Session Timer Too Small, dropping request\n");
		exit;
	}
	# track the session timers via the dialog module
	setflag(5);
	setflag(6);
  }
}
...
```


### Exported Statistics {#exported_statistics}


#### expired_sst {#stat_expired_sst}


Number of dialogs which got expired session timer.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 30 | 25 | 125 | 129 |
| 2. | Ron Winacott | 27 | 5 | 2083 | 265 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 18 | 15 | 54 | 65 |
| 4. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 15 | 12 | 104 | 104 |
| 5. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 14 | 10 | 106 | 140 |
| 6. | Andrei Datcu ([@andrei-datcu](https://github.com/andrei-datcu)) | 8 | 6 | 122 | 28 |
| 7. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 7 | 5 | 23 | 37 |
| 8. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 6 | 4 | 7 | 15 |
| 9. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 6 | 3 | 171 | 44 |
| 10. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 5 | 3 | 15 | 18 |


**All remaining contributors**: Anca Vamanu, Christophe Sollet ([@csollet](https://github.com/csollet)), Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)), Damien Sandras ([@dsandras](https://github.com/dsandras)), Konstantin Bokarius, Dan Pascu ([@danpascu](https://github.com/danpascu)), Ezequiel Lovelle ([@lovelle](https://github.com/lovelle)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Edson Gellert Schubert, Elena-Ramona Modroiu.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Oct 2006 - May 2020 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Jun 2011 - Sep 2019 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Jan 2013 - May 2019 |
| 4. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 5. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 6. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | Oct 2016 - Oct 2016 |
| 7. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Jun 2011 - Feb 2015 |
| 8. | Ezequiel Lovelle ([@lovelle](https://github.com/lovelle)) | Oct 2014 - Oct 2014 |
| 9. | Andrei Datcu ([@andrei-datcu](https://github.com/andrei-datcu)) | Jul 2014 - Jul 2014 |
| 10. | Damien Sandras ([@dsandras](https://github.com/dsandras)) | Jul 2013 - Jul 2013 |


**All remaining contributors**: Christophe Sollet ([@csollet](https://github.com/csollet)), Anca Vamanu, Henning Westerholt ([@henningw](https://github.com/henningw)), Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Dan Pascu ([@danpascu](https://github.com/danpascu)), Elena-Ramona Modroiu, Ron Winacott.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Christophe Sollet ([@csollet](https://github.com/csollet)), Henning Westerholt ([@henningw](https://github.com/henningw)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Elena-Ramona Modroiu, Ron Winacott.
