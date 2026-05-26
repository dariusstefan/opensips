---
title: "opentelemetry Module"
description: "The *opentelemetry* module provides OpenTelemetry tracing for OpenSIPS route execution. It creates a root span per processed SIP message and a child span for each route entry."
---

## Admin Guide


### Overview {#overview}


The *opentelemetry* module provides OpenTelemetry
	tracing for OpenSIPS route execution. It creates a root span per
	processed SIP message and a child span for each route entry.


The root SIP message span follows a local semantic convention inspired
		by the OpenTelemetry HTTP span conventions: it uses a method-plus-target
		span name, server/client/internal span kinds based on the OpenSIPS route
		type, and generic network, client, server and URL attributes wherever
		they fit the SIP model.


Spans include common SIP attributes (request method, Call-ID, CSeq,
		response status) and connection metadata. While a span is active,
		OpenSIPS logs can be attached as OpenTelemetry events for easier
		correlation.


Trace data is exported via the OTLP/HTTP exporter from the
		OpenTelemetry C++ SDK.


The local SIP span convention emitted by this module is documented in
		`modules/opentelemetry/semantic-convention/sip-spans.md`.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *None*.


#### External Libraries or Applications


The following libraries or applications must be installed before
		running OpenSIPS with this module loaded:


- *OpenTelemetry C++ SDK* (opentelemetry-cpp),
				with the OTLP/HTTP exporter enabled.


### Exported Parameters {#exported_parameters}


#### enable (integer) {#param_enable}


Enables or disables OpenTelemetry tracing at startup. It can also be
		changed at runtime using the `opentelemetry:enable`
		MI command.


The module is built only when the OpenTelemetry C++ SDK is available
			at build time.


*Default value is "0 (disabled)".*


**Example: Set enable parameter**


```opensips
...
modparam("opentelemetry", "enable", 1)
...
```


#### proc_profiling (integer) {#param_proc_profiling}


If enabled, the module will also profile/trace the OpenSIPS processes,
		not only the script.


*Default value is "0 (disabled)".*


**Example: Set proc_profiling parameter**


```opensips
...
modparam("opentelemetry", "proc_profiling", 1)
...
```


#### log_level (integer) {#param_log_level}


Log level threshold used by the OpenTelemetry log consumer when
		attaching log events to the active span.


*Default value is "L_DBG".*


**Example: Set log_level parameter**


```opensips
...
modparam("opentelemetry", "log_level", 3)
...
```


#### use_batch (integer) {#param_use_batch}


Selects the OpenTelemetry span processor. When enabled, the module uses
		the batch span processor; otherwise it uses the simple span processor.


*Default value is "1 (enabled)".*


**Example: Set use_batch parameter**


```opensips
...
modparam("opentelemetry", "use_batch", 0)
...
```


#### service_name (string) {#param_service_name}


Sets the OpenTelemetry "service.name" resource attribute.


*Default value is "opensips".*


**Example: Set service_name parameter**


```opensips
...
modparam("opentelemetry", "service_name", "edge-proxy")
...
```


#### exporter_endpoint (string) {#param_exporter_endpoint}


Overrides the OTLP/HTTP exporter endpoint. If empty, the OpenTelemetry
		SDK default is used.


*Default value is "empty".*


**Example: Set exporter_endpoint parameter**


```opensips
...
modparam("opentelemetry", "exporter_endpoint", "http://127.0.0.1:4318/v1/traces")
...
```


### Exported MI Functions {#exported_mi_functions}


#### opentelemetry:enable {#mi_enable}


Replaces obsolete MI command: *otel_enable*.


Enables or disables OpenTelemetry tracing at runtime.


Name: *opentelemetry:enable*


Parameters:


- *opentelemetry:enable* - set to "1" to enable
				tracing or "0" to disable it.


MI FIFO Command Format:


```
		## enable tracing
		opensips-cli -x mi opentelemetry:enable enable=1
		## disable tracing
		opensips-cli -x mi opentelemetry:enable enable=0
		
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 21 | 8 | 1144 | 144 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 6 | 4 | 17 | 14 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 5 | 2 | 73 | 66 |
| 4. | Your Name | 3 | 1 | 230 | 0 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Mar 2026 - May 2026 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Mar 2026 - Apr 2026 |
| 3. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Dec 2025 - Apr 2026 |
| 4. | Your Name | Jan 2026 - Jan 2026 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Your Name.


*Documentation Copyrights:*


Copyright © 2026 OpenSIPS Project
