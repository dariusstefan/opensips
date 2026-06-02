---
title: "Presence_XCAPDiff Module"
description: "The presence_xcapdiff is an OpenSIPS module that adds support for the \"xcap-diff\" event to presence and pua. At the moment, the module just registers the event but doesn't do any event-specific processing. The module will automatically determine if the presence and/or pua ..."
---

## Admin Guide


### Overview {#overview}


The presence_xcapdiff is an OpenSIPS module that adds support for the
      "xcap-diff" event to presence and pua. At the moment, the module
      just registers the event but doesn't do any event-specific processing.
      The module will automatically determine if the presence and/or pua
      modules are present and if so it will register the xcap-diff event
      with them. This allows the module to automatically offer presence
      or pua related functionality simply based on the presence of the
      aforementioned modules in the OpenSIPS configuration, without any
      need for manual configuration.


Registering the event with pua, allows the XCAP server to publish
      the xcap-event when some modification of a document happens.
      Registering the event with presence allows clients to subscribe
      to the event.


The module is intended to be used with the OpenXCAP server (www.openxcap.org),
      although it doesn't contain any OpenXCAP-specific code and should be usable
      with any XCAP server.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *presence* module - to enable clients to
              subscribe to the xcap-diff event package.
- *pua* module - to be able to publish the
              xcap-diff event when some modification of a document happens.
- *pua_mi* module - to enable pua to publish
              the xcap-diff event using the MI interface. This is needed if
              this module is intended to be used in conjunction with OpenXCAP.


#### External Libraries or Applications


The following libraries or applications must be installed before
        running OpenSIPS with this module loaded:


- *None*.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 9 | 7 | 7 | 7 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 8 | 6 | 26 | 31 |
| 3. | Denis Bilenko | 7 | 3 | 300 | 4 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 6 | 4 | 4 | 2 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 5 | 3 | 4 | 7 |
| 6. | Anca Vamanu | 4 | 2 | 2 | 11 |
| 7. | Dan Pascu ([@danpascu](https://github.com/danpascu)) | 4 | 1 | 76 | 120 |
| 8. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 3 | 1 | 2 | 2 |
| 9. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - Sep 2019 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jul 2009 - Apr 2019 |
| 4. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 5. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 6. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Jul 2014 - Jun 2018 |
| 7. | Anca Vamanu | Sep 2010 - Dec 2010 |
| 8. | Dan Pascu ([@danpascu](https://github.com/danpascu)) | Sep 2008 - Sep 2008 |
| 9. | Denis Bilenko | Sep 2008 - Sep 2008 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Dan Pascu ([@danpascu](https://github.com/danpascu)), Denis Bilenko.
