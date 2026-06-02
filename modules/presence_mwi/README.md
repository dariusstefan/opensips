---
title: "Presence_MWI Module"
description: "The module does specific handling for notify-subscribe message-summary (message waiting indication) events as specified in RFC 3842. It is used with the general event handling module, presence. It constructs and adds message-summary event to it."
---

## Admin Guide


### Overview {#overview}


The module does specific handling for notify-subscribe
	      message-summary (message waiting indication) events
	      as specified in RFC 3842.
	      It is used with the general event handling module,
   	      presence. It constructs and adds message-summary event to
  	      it.


The module does not currently implement any authorization
	      rules.  It assumes that publish requests are only issued by
	      a voicemail application and subscribe requests only by
	      the owner of voicemail box.  Authorization can thus
	      be easily done by OpenSIPS configuration file before
	      calling handle_publish() and handle_subscribe()
	      functions.


The module implements a simple check of content type
	      application/simple-message-summary:  Content must start
	      with Messages-Waiting status line followed by zero or
	      more lines that consist of tabs and printable ASCII
	      characters.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *presence*.


#### External Libraries or Applications


None.


### Exported Parameters {#exported_parameters}


None.


### Exported Functions {#exported_functions}


None to be used in configuration file.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 15 | 13 | 30 | 30 |
| 2. | Juha Heinanen ([@juha-h](https://github.com/juha-h)) | 12 | 5 | 645 | 11 |
| 3. | Anca Vamanu | 10 | 7 | 18 | 88 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 9 | 7 | 25 | 30 |
| 5. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 9 | 7 | 13 | 11 |
| 6. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 9 | 7 | 10 | 8 |
| 7. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 3 | 2 | 2 | 0 |
| 8. | Sergio Gutierrez | 3 | 1 | 41 | 10 |
| 9. | Ancuta Onofrei | 3 | 1 | 10 | 13 |
| 10. | Konstantin Bokarius | 3 | 1 | 2 | 5 |


**All remaining contributors**: Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Edson Gellert Schubert, Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)).


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - Sep 2019 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jul 2007 - Apr 2019 |
| 3. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Jun 2018 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - May 2017 |
| 6. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Oct 2010 - Mar 2011 |
| 7. | Anca Vamanu | Jul 2007 - Sep 2010 |
| 8. | Sergio Gutierrez | Nov 2008 - Nov 2008 |
| 9. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | Oct 2007 - Mar 2008 |
| 10. | Konstantin Bokarius | Mar 2008 - Mar 2008 |


**All remaining contributors**: Edson Gellert Schubert, Juha Heinanen ([@juha-h](https://github.com/juha-h)), Ancuta Onofrei.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Juha Heinanen ([@juha-h](https://github.com/juha-h)).
