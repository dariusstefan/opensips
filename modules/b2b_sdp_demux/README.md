---
title: "B2B SDP De-Multiplexer Module"
description: "This module provides the logic to convert a multi-stream SDP call, to multiple calls, each containing a subset of streams from the initial call. The module only handles the SIP signalling part of the call, without interfering with the media of the call, which will flow end-to-end. The onl..."
---

## Admin Guide


### Overview {#overview}


This module provides the logic to convert a multi-stream
		SDP call, to multiple calls, each containing a subset of
		streams from the initial call. The module only handles the
		SIP signalling part of the call, without interfering with
		the media of the call, which will flow end-to-end. The only
		manipulation it does is at the SDP level to disable the
		media-streams that are not being used downstream.


The logic is implemented on top of the B2B module, and
		de-multiplexes a B2B server (the initial call with
		multiple streams) to multiple B2B clients (with their own
		streams subset). In-dialog requests that come from the
		initial caller will be forked towards each client, and their
		replies aggregated back to the caller. The other side
		in-dialog requests are forwarded to the caller as if only
		their stream had changed. When a call is terminated from 
		the client side, the module can have different behaviors,
		according to the [client bye mode](#param_client_bye_mode)
		parameter.


### Use Cases {#use-cases}


A common scenario where this module can become useful is
		when configuring OpenSIPS as a SIPREC SRS proxy. Using this
		module you can receive on one side SIPREC INVITEs, which
		usually have two or more SDP streams (one for each
		call/conference participants), and split/de-multiplex each
		stream in a new call downstream, usually towards a media
		server that is able to do call recording. This way the
		media server will have to handle calls that contain a
		single media stream.


Another use case is balancing multiple streams to different
		media servers. For example, if you are offering both audio
		and video services, you can split a two-stream call (with
		an audio and video stream) to two different calls, and send
		them to be processed by different servers. This way you may
		have separated audio-dedicated processing media servers, as
		well as video-dedicated one. Of course, this can be achieved
		if you can process the streams separately, for example for
		recording.


### Dependencies {#dependencies}


#### OpenSIPS Modules {#b2b_sdp_demux_modules_dependencies}


The following modules must be loaded before this module:


- *B2B_ENTITIES* - Back-2-Back module
				used for handing server and client side calls.


#### External Libraries or Applications {#b2b_sdp_demux_external_dependencies}


The following libraries or applications must be installed before
		running OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### client_bye_mode (string) {#param_client_bye_mode}


This parameter indicates how a BYE coming from the
			client side should be treated in the context of the
			upstream call.


Possible values are:
			
			
			*disable* - when a client
				terminates its call, the module will simply disable
				the media streams associated with its call, resulting
				in a re-INVITE upstream.
			
			
			*terminate* - when one client
				terminates its call, the module will terminate
				all other calls, including the upstream one.
			
			
			*disable-terminate* - same as
				disable, except that when the final stream is disabled,
				instead of a re-INVITE with all streams disabled,
				the module sends a BYE upstream.
		*Default value is "disable".*


```c title="Set client_bye_mode parameter"
...
modparam("b2b_sdp_demux", "client_bye_mode", "terminate")
...
```


### Exported Functions {#exported_functions}


#### b2b_sdp_demux(URI[, [headers][, streams]]) {#func_b2b_sdp_demux}


Engages the B2B SDP De-Multiplexing scenario for the
				calls it has been triggered on.


Parameters:


- *URI* (string) - the URI where
					to send the newly generated calls
- *headers* (AVP, optional) - an
					AVP containing multiple values, each index
					corresponding to one of the new calls generated
					by the function. The number of values in the
					AVP should be equal to the number of calls
					resulted, otherwise it may lead to an unexpected
					behavior. If missing, no extra headers will be
					added.
- *streams* (AVP, optional) - an
					AVP containing multiple values, each value
					indicating the media stream index that should be
					used for the current client. If multiple streams
					should be used for a single call, they should
					be specified comma-separated (i.e.
					*0,2*). The number of AVP
					values represent the number of calls generated
					downstream. If the parameter is missing,
					a call will be generated for each stream present in
					the initial call.


This function can be used only from request route.


```c title="Use b2b_sdp_demux() to handle an audio SIPREC call"
...
if (!has_totag() && is_method("INVITE")) {
	$avp(headers) = "X-Leg: caller\r\n");
	$avp(headers) = "X-Leg: callee\r\n");
	b2b_sdp_demux("sip:media@localhost", $avp(headers));
}
...
	
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 93 | 59 | 2949 | 500 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 5 | 5 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | May 2021 - Aug 2025 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Nov 2023 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)).
## License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
