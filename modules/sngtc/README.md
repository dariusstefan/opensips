---
title: "sngtc Module"
description: "The **Sangoma transcoding module** offers the possibility of performing voice transcoding with the [D-series transcoding cards manufactured by Sangoma](https://wiki.sangoma.com/display/MTC/Media+Transcoding). The module makes use of the Sangoma Transcoding API in order to manage transcoding..."
---

## Admin Guide


### Overview {#overview}


The **Sangoma transcoding module** offers the
	possibility of performing voice transcoding with the
	[D-series
		transcoding cards manufactured by Sangoma](https://wiki.sangoma.com/display/MTC/Media+Transcoding). The module makes use
	of the Sangoma Transcoding API in order to
	manage transcoding sessions on the dedicated equipment. For the cards
	in the network to be detected, the Sangoma SOAP server must be up and
	running (*sngtc_server* daemon).


### How it works


The module performs several modifications in the SDP body of SIP INVITE,
	200 OK and ACK messages. In all transcoding scenarios, the UAC performs early
	SDP negotiation, while the UAS does late negotiation. This way, OpenSIPS
	becomes responsible for intersecting the codec offer and answer, together with
	the management of transcoding sessions on the Sangoma cards.


This scenario brings about a couple of
	**restrictions**:


- UACs MUST only perform early SDP negotiation
- UASs MUST support late SDP negotiation (rfc 3261 requirement)


Since the *sngtc_node* library performs several memory
	allocations with each newly created transcoding session, the module uses a
	dedicated process, responsible for the management of the above-mentioned sessions. The
	*sangoma_worker* process communicates with the OpenSIPS
	UDP receivers through a series of pipes.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following  modules must be loaded before this module:


- *dialog*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *sngtc_node library - [download from Sangoma](https://wiki.freepbx.org/display/MTC/Media+Transcoding+Download),
					unpack, make, make install (required in order to compile this module)*.
- *sngtc_server up and running (required in order for
					this module to properly work)*.


### Exported Functions {#exported_functions}


#### sngtc_offer() {#func_sngtc_offer}


The function strips off the SDP offer from a SIP INVITE, thus
		asking for another SDP offer from the opposite endpoint (late negotiation).
		
		The following **error codes** may be returned:


- *-1* - SDP parsing error
- *-3* - internal error / no more memory


The function can be used from REQUEST_ROUTE, ONREPLY_ROUTE.


```c title="sngtc_offer usage"
...
	if (is_method("INVITE")) {
		t_newtran();
		create_dialog();
		sngtc_offer();
	}
...
```


#### sngtc_callee_answer([listen_if_A], [listen_if_B])
		 {#func_sngtc_callee_answer}


Handles the SDP offer from 200 OK responses, intersects both offers with
		the capabilities of the transcoding card and creates a new transcoding
		session on the card **only if** necessary. It then rewrites the 200 OK SDP so that it 
		contains the information resulted from the codec intersection.


**Parameters** explained:


Since the D-series transcoding cards are connected through either a
		PCI slot or simply an Ethernet connector, they cannot be assigned
		global IPs. Consequently, the module will write the local, private IP of the
		card in the SDP answers sent to each of the endpoints. Since this will not
		work with non-local UAs, the optional parameters force the RTP listen
		interface for each UA. This way, the script writer can enforce a global IP
		for the incoming RTP (which can be port forwarded to a transcoding card).


- *listen_if_A* (string) - the interface where the UAC (the caller) will send RTP after the call is established (IP from the 'c=' SDP line(s))
- *listen_if_B* (string) - the interface where the UAS (the callee) will send RTP after the call is established (IP from the 'c=' SDP line(s))


The following **error codes** may be returned:


- *-1* - SDP parsing error
- *-2* - failed to create transcoding session
- *-3* - internal error / no more memory


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE.


```c title="sngtc_callee_answer usage"
...
onreply_route[1] {
	if ($rs == 200)
		sngtc_callee_answer("11.12.13.14", "11.12.13.14");
}
...
```


#### sngtc_caller_answer() {#func_sngtc_caller_answer}


Attaches an SDP body to the caller's ACK request, so that it matches
		the late SDP negotiation done by the UAS.


The following **error codes** may be returned:


- *-3* - internal error / no more memory


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE.


```c title="sngtc_caller_answer usage"
...
	if (has_totag()) {
		if (loose_route()) {
			...
			if (is_method("ACK"))
				sngtc_caller_answer();
		}
		...
	}
...
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 32 | 14 | 2076 | 59 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 9 | 7 | 15 | 12 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 9 | 6 | 29 | 64 |
| 4. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 7 | 5 | 57 | 44 |
| 5. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 5 | 5 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Mar 2023 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Oct 2014 - Mar 2020 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - Feb 2020 |
| 5. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Aug 2013 - Jun 2018 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)).
### License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
