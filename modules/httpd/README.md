---
title: "httpd Module"
description: "This module provides an HTTP transport layer for OpenSIPS."
---

## Admin Guide


### Overview {#overview}


This module provides an HTTP transport layer for OpenSIPS.


Implementation of httpd module's http server is based on
		libmicrohttpd library.


### Overview {#tlssupport}


TLS for the http server is enabled by setting  the `tls_cert_file`
			and `tls_key_file` parameters. If this is enabled, support for plain
			http is disabled.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *No dependencies on other OpenSIPS modules*.


#### External Libraries or Applications {#dependencies_libs}


The following libraries or applications must be installed before 
		running OpenSIPS with this module loaded:


- *libmicrohttpd*, with EPOLL support. This
					typically means a version newer than **0.9.50**.


**WARNING!**  Please be aware about an
			EPOLL support regression in the *libmicrohttpd*
			library and packaging which affects the OpenSIPS httpd module, which
			was fixed according to the below timeline.  The effect of the
			regression is that the HTTP reply body is *sometimes*
			never written by the library, causing the client (e.g. opensips-cli)
			to hang indefinitely waiting for it:


- versions **0.9.51** - **0.9.52**
				have been tested and work correctly
- regression introduced in **0.9.53** (Apr 2017),
				lasting until **0.9.71** (May 2020)
- regression is fixed since **0.9.72** (Dec 2020)


### Exported Parameters {#exported_parameters}


#### ip(string) {#param_ip}


The IP address used by the HTTP server to listen for incoming 
		requests.


*The default value is "*"* (bind to all IPv6 and IPv4 interfaces).


```c title="Set ip parameter"
...
modparam("httpd", "ip", "127.0.0.1")
...
```


#### port(integer) {#param_port}


The port number used by the HTTP server to listen for incoming 
		requests.


*The default value is 8888.*
		Ports lower than 1024 are not accepted.


```c title="Set port parameter"
...
modparam("httpd", "port", 8000)
...
```


#### conn_timeout(integer) {#param_conn_timeout}


Auto-close TCP connections which are idle for more than the designated
		timeout, in seconds.  Set to zero to never close any connections.


Note: the connection auto-close routine only seems to be executed
		in an "on-demand" fashion, during an HTTPD network event (e.g. on a new
		connection), which although not ideal, it should be good enough in
		practical terms.


*The default timeout is 30 seconds.*


```c title="Set conn_timeout parameter"
...
modparam("httpd", "conn_timeout", 10)
...
```


#### buf_size (integer) {#param_buf_size}


It specifies the maximum length (in bytes) of the buffer
		used to write in the html response.


If the size of the buffer is set to zero, it will be automatically
		set to a quarter of the size of the pkg memory.


*The default value is 0.*


```c title="Set buf_size parameter"
...
modparam("httpd", "buf_size", 524288)
...
```


#### post_buf_size (integer) {#param_post_buf_size}


It specifies the length (in bytes) of the POST HTTP requests
		processing buffer.  For large POST request, the default value
		might require to be increased.


*The default value is 1024. The minumal value is 256.*


```c title="Set post_buf_size parameter"
...
modparam("httpd", "post_buf_size", 4096)
...
```


#### receive_buf_size (integer) {#param_receive_buf_size}


It specifies the maximum length (in bytes) of the received HTTP requests.  
		For receiving large POST request, the default value might require to be increased.


*The default value is 1024.*


```c title="Set receive_buf_size parameter"
...
modparam("httpd", "receive_buf_size", 4096)
...
```


#### tls_cert_file (string) {#param_tls_cert_file}


Public certificate file for httpd. It will be used as server-side certificate for incoming TLS connections.


*The default value is ""*


```c title="Set tls_cert_file parameter"
...
modparam("httpd", "tls_cert_file", "/etc/opensips/tls/server.pem")
...
```


#### tls_key_file (string) {#param_tls_key_file}


Private key of the above certificate. I must be kept in a safe place with tight permissions!


*The default value is ""*


```c title="Set tls_key_file parameter"
...
modparam("httpd", "tls_key_file", "/etc/opensips/tls/server.key")
...
```


#### tls_ciphers (string) {#param_tls_ciphers}


You can specify the list of algorithms for authentication and encryption that you allow.
		To obtain a list of ciphers
		and then choose, use the gnutls-cli application:


- gnutls-cli -l


> **Warning:** Do not use the NULL algorithms (no encryption) ... never!!!


*The default value is  "SECURE256:+SECURE192:-VERS-ALL:+VERS-TLS1.2"*


```c title="Set tls_key_file parameter"
...
modparam("httpd", "tls_ciphers", "SECURE256:+SECURE192:-VERS-ALL:+VERS-TLS1.2")
...
```


### Exported MI Functions {#exported_mi_functions}


#### httpd_list_root_path {#mi_httpd_list_root_path}


Lists all the registered http root paths into the httpd module.
		When a request comes in, if the root parth is in the list,
		the request will be sent to the module that register it.


Name: *httpd_list_root_path*


Parameters: none


MI FIFO Command Format:


```c
opensips-cli -x mi httpd_list_root_path
		
```


### Exported Functions {#exported_functions}


No function exported to be used from configuration file.


### Known issues


Due to the fact that OpenSIPS is a multiprocess application,
		the microhttpd library is used in "external select" mode.
		This ensures that the library is not running in
		multithread mode and the library is entirely controled
		by OpenSIPS.  Due to this particular mode of operations,
		for now, the entire http response is built in a pre-allocated
		buffer (see buf_size parameter).


Future realeases of this module will address this issue.


Running the http daemon as non root on ports below 1024 is
		forbidden by default in linux (kernel>=2.6.24).
		To allow the port binding, one can use
		*setcap* to give
		extra privilleges to opensips binary:


```c
setcap 'cap_net_bind_service=+ep' /usr/local/sbin/opensips
		
```


## Developer Guide


### Available Functions


#### register_httpdcb (module, root_path, httpd_acces_handler_cb, httpd_flush_data_cb, httpd_init_proc_cb)


Register a new http root with it's associated callbacks into the httpd module.


Meaning of the parameters is as follows:


- *const char *mod*
			- name of the module that register an http root path to be handled;
- *str *root_path*
			- the registered root path;
- *httpd_acces_handler_cb f1*
			- handler to the callback method to be called on root path match;
- *httpd_flush_data_cb f2*
			- handler to the callback method to be called for sending extra data (at a later time);
- *httpd_init_proc_cb f3*
			- handler to the callback method to be called during httpd process init;


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 48 | 30 | 1667 | 147 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 27 | 23 | 131 | 71 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 23 | 19 | 172 | 82 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 14 | 12 | 103 | 54 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 10 | 7 | 52 | 89 |
| 6. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | 8 | 6 | 65 | 21 |
| 7. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 4 | 2 | 68 | 16 |
| 8. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 5 | 5 |
| 9. | Alexandra Titoc | 4 | 2 | 2 | 1 |
| 10. | Fabian Gast ([@fgast](https://github.com/fgast)) | 4 | 1 | 150 | 3 |


**All remaining contributors**: Stephane Alnet, Stas Kobzar, Dusan Klinec ([@ph4r05](https://github.com/ph4r05)), Ken Rice, Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)).


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Mar 2015 - May 2026 |
| 2. | Ken Rice | Sep 2025 - Sep 2025 |
| 3. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Dec 2024 - Dec 2024 |
| 4. | Alexandra Titoc | Sep 2024 - Sep 2024 |
| 5. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - May 2024 |
| 6. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 7. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jan 2013 - Aug 2021 |
| 8. | Fabian Gast ([@fgast](https://github.com/fgast)) | Aug 2020 - Aug 2020 |
| 9. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 10. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Jan 2012 - Jan 2019 |


**All remaining contributors**: Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)), Dusan Klinec ([@ph4r05](https://github.com/ph4r05)), Stas Kobzar, Stephane Alnet.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Fabian Gast ([@fgast](https://github.com/fgast)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)).
### License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
