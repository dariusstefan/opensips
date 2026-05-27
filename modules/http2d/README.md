---
title: "HTTP2D MODULE"
description: "This module provides an RFC 7540/9113 HTTP/2 server implementation with \"h2\" ALPN support, based on the **nghttp2** library ([https://nghttp2.org/](https://nghttp2.org/))."
---

## Admin Guide


### Overview {#overview}


This module provides an RFC 7540/9113 HTTP/2 server implementation with "h2" ALPN support,
		based on the **nghttp2** library ([https://nghttp2.org/](https://nghttp2.org/)).


HTTP/2, introduced in 2015, is a binary protocol with added transactional layers (SESSION, FRAME),
		which allow identifying and managing multiple, concurrent transfers over the same TCP/TLS connection.
		Thus, the revised protocol primarily aims to reduce resource usage for both clients and servers, by
		reducing the amount of TCP and/or TLS handshakes performed when loading a given web page.


The OpenSIPS **http2d** server includes support for both "h2" (TLS secured)
		and "h2c" (cleartext) HTTP/2 connections.  The requests arrive at
		*opensips.cfg* level using the [http2 request](#event_http2_request) event,
		where script writers may process the data and respond accordingly.


### Dependencies {#dependencies}


#### OpenSIPS Modules


None.


#### External Libraries or Applications


The HTTP/2 server is provided by the **nghttp2** library,
		which runs on top of the **libevent** server framework.


Overall, the following libraries must be installed before running
		OpenSIPS with this module loaded:


- *libnghttp2*
- *libevent*, *libevent_openssl*
- *libssl*, *libcrypto*


### Exported Parameters {#exported_parameters}


#### ip (string) {#param_ip}


The listening IPv4 address.


Default value is *"127.0.0.1"*.


```opensips title="Setting the ip parameter"
modparam("http2d", "ip", "127.0.0.2")
```


#### port (integer) {#param_port}


The listening port.


Default value is *443*.


```opensips title="Setting the port parameter"
modparam("http2d", "port", 5000)
```


#### tls_cert_path (string) {#param_tls_cert_path}


File path to the TLS certificate, in PEM format.


Default value is *NULL* (not set).


```opensips title="Setting the tls_cert_path parameter"
modparam("http2d", "tls_cert_path", "/etc/pki/http2/cert.pem")
```


#### tls_cert_key (string) {#param_tls_cert_key}


File path to the TLS private key, in PEM format.


Default value is *NULL* (not set).


```opensips title="Setting the tls_cert_key parameter"
modparam("http2d", "tls_cert_key", "/etc/pki/http2/private/key.pem")
```


#### max_headers_size (integer) {#param_max_headers_size}


The maximum amount of bytes allowed for all header field names and values
		combined in a single HTTP/2 request processed by the server.  Once this
		threshold is reached, extra headers will no longer be provided at script
		level and will be reported as errors instead.


Default value is *8192* bytes.


```opensips title="Setting the max_headers_size parameter"
modparam("http2d", "max_headers_size", 16384)
```


#### response_timeout (integer) {#param_response_timeout}


The maximum amount of time, in milliseconds, that the library will
		allow the opensips.cfg processing to take for a given HTTP/2 request.


Once this timeout is reached, the module will auto-generate a
		408 (request timeout) reply.


Default value is *2000* ms.


```opensips title="Setting the response_timeout parameter"
modparam("http2d", "response_timeout", 5000)
```


### Exported Functions {#exported_functions}


#### http2_send_response(code, [headers_json], [data]) {#func_http2_send_response}


Sends a response for the HTTP/2 request being processed.  The *":status"*
		header field will be automatically included by the module as 1st header, so it must not be
		included in the *headers_json* array.


*Parameters*


- *code* (integer) - The HTTP/2 reply code
- *headers_json* (string, default: *NULL*)
				- Optional JSON Array containing {"header": "value"} elements, denoting HTTP/2
				headers and their values to be included in the response message.
- *data* (string, default: *NULL*)
				- Optional DATA payload to include in the response message.


*Return Codes*


- **1** - Success
- **-1** - Internal Error


This function can only be used from an *EVENT_ROUTE*.


```opensips title="http2_send_response() usage"
event_route [E_HTTP2_REQUEST] {
  xlog(":: Method:  $param(method)\n");
  xlog(":: Path:    $param(path)\n");
  xlog(":: Headers: $param(headers)\n");
  xlog(":: Data:    $param(data)\n");

  $json(hdrs) := $param(headers);
  xlog("content-type: $json(hdrs/content-type)\n");

  $var(rpl_headers) = "[
	{ \"content-type\": \"application/json\" },
	{ \"server\": \"OpenSIPS 3.5\" },
	{ \"x-current-time\": \"1711457142\" },
	{ \"x-call-cost\": \"0.355\" }
  ]";

  $var(data) = "{\"status\": \"success\"}";

  if (!http2_send_response(200, $var(rpl_headers), $var(data)))
    xlog("ERROR - failed to send HTTP/2 response\n");
}
```


### Exported Events {#exported_events}


#### E_HTTP2_REQUEST {#event_http2_request}


This event is raised whenever the *http2d*
		module is loaded and OpenSIPS receives an HTTP/2 request on the configured
		listening interface(s).


Parameters:


- *method (string)* - value of the ":method" HTTP/2 header
- *path (string)* - value of the ":path" HTTP/2 header
- *headers (string)* - JSON Array with all headers of the request,
						including pseudo-headers
- *data (string, default: NULL)* - If the request included a payload,
						this parameter will hold its contents


Note that this event is currently designed to be mainly consumed by an *event_route*,
		since that is the only way to gain access to the [http2 send response](#func_http2_send_response)
		function in order to build custom response messages.  On the other hand,
		if the application does not mind the answer being always a 200 with no payload,
		this event can be successfully consumed through any other EVI-compatible delivery channel ☺️


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 30 | 8 | 2085 | 215 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 3 | 1 | 1 | 2 |
| 3. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2025 - Aug 2025 |
| 2. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jul 2025 - Jul 2025 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2024 - May 2024 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)).


*Documentation Copyrights:*


Copyright © 2024 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
