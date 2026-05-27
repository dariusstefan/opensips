---
title: "Peering Module"
description: "Peering module allows SIP providers (operators or organizations) to verify from a broker if source or destination of a SIP request is a trusted peer."
---

## Admin Guide


### Overview {#overview}


Peering module allows SIP
	providers (operators or organizations) to verify from a broker
	if source or destination of a SIP request is a trusted peer.


In order to participate in the trust community provided by a
	broker, each SIP provider registers with the broker the domains
	(host parts of SIP URIs) that they serve.  When a SIP proxy of a
	provider needs to send a SIP request to a non-local domain, it
	can find out from the broker using verify_destination() function
	if the non-local domain is served by a trusted peer.  If so, the
	provider receives from the broker a hash of the SIP request and
	a timestamp that it includes in the request to the non-local
	domain.  When a SIP
	proxy of the non-local domain receives the SIP request, it, in
	turn, can verify from the broker using verify_source() function
	if the request came from a trusted peer.


Verification functions communicate with the broker using an AAA
        protocol.


Comments and suggestions for improvements are welcome.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The module depends on the following modules
			(in the other words 
			the listed modules must be loaded before this module):


- *an AAA implementing module*


### Exported Parameters {#exported_parameters}


#### aaa_url (string) {#param_aaa_url}


This is the url representing the AAA protocol used and the location of the configuration file of this protocol.


If the parameter is set to empty string, the AAA accounting support
			will be disabled (even if compiled).


Default value is "NULL".


```c title="Set aaa_url parameter"
...
modparam("peering", "aaa_url", "radius:/etc/radiusclient-ng/radiusclient.conf")
...
```


#### verify_destination_service_type (integer) {#param_verify_destination_service_type}


This is the value of the Service-Type AAA attribute to be
		used, when sender of SIP Request verifies request's
	destination using verify_destination() function.


Default value is dictionary value of "Sip-Verify-Destination"
		Service-Type.


```c title="verify_destination_service_type parameter usage"
...
modparam("peering", "verify_destination_service_type", 21)
...
```


#### verify_source_service_type (integer)


This is the value of the Service-Type AAA attribute to be
		used, when receiver of SIP Request verifies request's
	source using verify_source() function.


Default value is dictionary value of "Sip-Verify-Source"
		Service-Type.


```c title="verify_source_service_type parameter usage"
...
modparam("peering", "verify_source_service_type", 22)
...
```


### Exported Functions {#exported_functions}


#### verify_destination() {#func_verify_destination}


Function verify_destination() queries from
		broker's AAA server if domain (host part) of Request
	URI is served by a trusted peer.  AAA request contains the
	following attributes/values:


- User-Name - Request-URI host
- SIP-URI-User - Request-URI user
- SIP-From-Tag - From tag
- SIP-Call-Id - Call id
- Service-Type - verify_destination_service_type


Function returns value 1 if domain of Request URI is
	served by a trusted peer and -1 otherwise.  In case of positive
	result, AAA server returns a set of SIP-AVP reply attributes.
	Value of each SIP-AVP is of form:


[#]name(:|#)value


Value of each SIP-AVP reply attribute is mapped to an
		 OpenSIPS AVP.  Prefix # in front of name or value indicates a
	string name or string value, respectively.


One of the SIP-AVP reply attributes contains a string
		 that the source peer must include "as is" in a 
		 P-Request-Hash header when it sends the SIP request to
		 the destination peer.  The string value may, for
		 example, be of form hash@timestamp, where hash contains
		 a hash calculated by the broker based on the attributes
		 of the query and some local information and timestamp
		 is the time when the calculation was done.


AVP names used in reply attributes are assigned by the
		 broker.


This function can be used from REQUEST_ROUTE and
		FAILURE_ROUTE.


```c title="verify_destination() usage"
...
if (verify_destination()) {
   append_hf("P-Request-Hash: $avp(prh)\r\n");
}
...
```


#### verify_source() {#func_verify_source}


Function verify_source() queries from
		broker's AAA server if SIP request was received from
	a trusted peer.  AAA request contains the
	following attributes/values:


- User-Name - Request-URI host
- SIP-URI-User - Request-URI user
- SIP-From-Tag - From tag
- SIP-Call-Id - Call id
- SIP-Request-Hash - body of P-Request-Hash header
- Service-Type - verify_source_service_type


Function returns value 1 if SIP request was received
	from a trusted peer and -1 otherwise.  In case of positive
	result, AAA server may return a set of SIP-AVP reply
	attributes.  Value of each SIP-AVP is of form:


[#]name(:|#)value


Value of each SIP-AVP reply attribute is mapped to an
		 OpenSIPS 
		 AVP.  Prefix # in front of name or value indicates a
	string name or string value, respectively.


AVP names used in reply attributes are
		 assigned by the broker.


This function can be used from REQUEST_ROUTE and
		FAILURE_ROUTE.


```c title="verify_source() usage"
...
if (is_present_hf("P-Request-Hash")) {
   if (verify_source()) {
      xlog("L_INFO", "Request came from trusted peer\n")
   }
}
...
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 13 | 11 | 29 | 32 |
| 2. | Juha Heinanen ([@juha-h](https://github.com/juha-h)) | 13 | 4 | 909 | 5 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 10 | 8 | 27 | 39 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 9 | 7 | 13 | 9 |
| 5. | Irina-Maria Stanescu | 9 | 2 | 125 | 254 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 4 | 2 | 4 | 4 |
| 7. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 3 | 1 | 3 | 3 |
| 8. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Apr 2013 - Sep 2019 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jun 2008 - Apr 2019 |
| 4. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 5. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 6. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Jun 2018 |
| 7. | Irina-Maria Stanescu | Aug 2009 - Aug 2009 |
| 8. | Juha Heinanen ([@juha-h](https://github.com/juha-h)) | May 2008 - Jun 2008 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Irina-Maria Stanescu, Juha Heinanen ([@juha-h](https://github.com/juha-h)).


*Documentation Copyrights:*


Copyright © 2008 Juha Heinanen
