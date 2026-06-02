---
title: "tls_wolfssl Module"
description: "This module implements TLS operations using the [wolfSSL](https://www.wolfssl.com/) libarary. It provides the primitives required by the *tls_mgm* module in order to expose a higher-level API used by TLS-based protocol modules like *proto_tls* or *proto_wss*."
---

## Admin Guide


### Overview {#overview}


This module implements TLS operations using the
		[wolfSSL](https://www.wolfssl.com/) libarary. It provides the primitives
		required by the *tls_mgm* module in order to expose a
		higher-level API used by TLS-based protocol modules like
		*proto_tls* or *proto_wss*.


The *wolfSSL* library is statically-linked and bundled
		with this module so no installation or external dependency is required.


### Dependencies {#dependencies}


#### Compilation


The following packages must be installed before compiling this module:


- *autoconf*.
- *automake*.
- *libtool*.


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *None*.


#### External Libraries or Applications


The following libraries or applications must be installed before
		running OpenSIPS with this module loaded:


- *None*.


## Frequently Asked Questions


**Q: Why do I get the following error when compiling the module?**


If you obtained the OpenSIPS sources by cloning the repository from Github,
		without using the *--recursive* option for the
		*git clone* command, you did not properly fetch the
		*wolfSSL* library code, which is included as a git submodule
		pointing to the official *wolfSSL* repository.

In order to fetch the *wolfSSL* library code you can run:
		
```c

		git submodule update --init
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 29 | 22 | 553 | 114 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 11 | 7 | 206 | 65 |
| 3. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 2 | 2 |
| 4. | James Stanley | 3 | 1 | 6 | 1 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 3 | 1 | 3 | 3 |
| 6. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 3 | 1 | 2 | 2 |
| 7. | Bence Szigeti | 3 | 1 | 1 | 1 |
| 8. | Alexey Vasilyev ([@vasilevalex](https://github.com/vasilevalex)) | 2 | 1 | 11 | 0 |
| 9. | vladpaiu | 2 | 1 | 8 | 0 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | vladpaiu | Jun 2025 - Jun 2025 |
| 2. | James Stanley | Feb 2024 - Feb 2024 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Oct 2023 - Oct 2023 |
| 4. | Bence Szigeti | Oct 2023 - Oct 2023 |
| 5. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2023 - Oct 2023 |
| 6. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | May 2023 - May 2023 |
| 7. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2021 - Mar 2023 |
| 8. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 9. | Alexey Vasilyev ([@vasilevalex](https://github.com/vasilevalex)) | Jan 2022 - Jan 2022 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)).
### License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
