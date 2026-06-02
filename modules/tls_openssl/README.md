---
title: "tls_openssl Module"
description: "This module implements TLS operations using the [openSSL](https://www.openssl.org/) libarary. It provides the primitives required by the *tls_mgm* module in order to expose a higher-level API used by TLS-based protocol modules like *proto_tls* or *proto_wss* etc."
---

## Admin Guide


### Overview {#overview}


This module implements TLS operations using the
		[openSSL](https://www.openssl.org/) libarary. It provides the primitives
		required by the *tls_mgm* module in order to expose a
		higher-level API used by TLS-based protocol modules like
		*proto_tls* or *proto_wss* etc.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *None*.


#### External Libraries or Applications


OpenSIPS TLS v1.0 support requires the following packages:


- *openssl* or
				*libssl* >= 0.9.6
- *openssl-dev* or
				*libssl-dev*


OpenSIPS TLS v1.1/1.2 support requires the following packages:


- *openssl* or
				*libssl* >= 1.0.1e
- *openssl-dev* or
				*libssl-dev*


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 25 | 5 | 2195 | 39 |
| 2. | James Stanley | 4 | 2 | 13 | 8 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 3 | 1 | 3 | 3 |
| 4. | Ross Henderson | 3 | 1 | 3 | 1 |
| 5. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 3 | 1 | 1 | 1 |
| 6. | Norman Brandinger ([@NormB](https://github.com/NormB)) | 2 | 1 | 32 | 0 |
| 7. | Your Name | 2 | 1 | 0 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Norman Brandinger ([@NormB](https://github.com/NormB)) | Mar 2026 - Mar 2026 |
| 2. | Ross Henderson | Feb 2026 - Feb 2026 |
| 3. | Your Name | Jul 2024 - Jul 2024 |
| 4. | James Stanley | Apr 2023 - Feb 2024 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | May 2023 - May 2023 |
| 6. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 7. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2021 - Oct 2021 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)).
