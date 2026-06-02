---
title: "textops Module"
description: "The module implements text based operations over the SIP message processed by OpenSIPS. SIP is a text based protocol and the module provides a large set of very useful functions to manipulate the message at text level, e.g., regular expression search and replace, Perl-like substitutions, ..."
---

## Admin Guide


### Overview {#overview}


The module implements text based operations over the SIP message
		processed by OpenSIPS. SIP is a text based protocol and the module
		provides a large set of very useful functions to manipulate the
		message at text level, e.g., regular expression search and replace,
		Perl-like substitutions, etc.


Note: all SIP-aware functions like *insert_hf*,
		*append_hf* or *codec*
		operations have been moved to the *sipmsgops*
		module.


#### Known Limitations


search ignores folded lines. For example, 
		search("(From|f):.*@foo.bar")
		doesn't match the following From header field:


```c
From: medabeda 
 <sip:medameda@foo.bar>;tag=1234
```


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *No dependencies on other OpenSIPS modules*.


#### External Libraries or Applications


The following libraries or applications must be installed before 
		running OpenSIPS with this module loaded:


- *None*.


### Exported Functions {#exported_functions}


#### search(re) {#func_search}


Searches for the re in the message.


Meaning of the parameters is as follows:


- *re* (string) - Regular expression.


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
		FAILURE_ROUTE, BRANCH_ROUTE.


```c title="search usage"
...
if ( search("[Ss][Ii][Pp]") ) { /*....*/ };
...
```


#### search_body(re) {#func_search_body}


Searches for the re in the body of the message.


Meaning of the parameters is as follows:


- *re* (string) - Regular expression.


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
		FAILURE_ROUTE, BRANCH_ROUTE.


```c title="search_body usage"
...
if ( search_body("[Ss][Ii][Pp]") ) { /*....*/ };
...
```


#### search_append(re, txt) {#func_search_append}


Searches for the first match of re and appends txt after it.


Meaning of the parameters is as follows:


- *re* (string) - Regular expression.
- *txt* (string) - String to be appended.


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
		FAILURE_ROUTE, BRANCH_ROUTE.


```c title="search_append usage"
...
search_append("[Oo]pen[Ss]er", " SIP Proxy");
...
```


#### search_append_body(re, txt) {#func_search_append_body}


Searches for the first match of re in the body of the message
		and appends txt after it.


Meaning of the parameters is as follows:


- *re* (string) - Regular expression.
- *txt* (string) - String to be appended.


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
		FAILURE_ROUTE, BRANCH_ROUTE.


```c title="search_append_body usage"
...
search_append_body("[Oo]pen[Ss]er", " SIP Proxy");
...
```


#### replace(re, txt) {#func_replace}


Replaces the first occurrence of re with txt.


Meaning of the parameters is as follows:


- *re* (string) - Regular expression.
- *txt* (string)


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
		FAILURE_ROUTE, BRANCH_ROUTE.


```c title="replace usage"
...
replace("opensips", "Open SIP Server");
...
```


#### replace_body(re, txt) {#func_replace_body}


Replaces the first occurrence of re in the body of the message
		with txt.


Meaning of the parameters is as follows:


- *re* (string) - Regular expression.
- *txt* (string)


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
		FAILURE_ROUTE, BRANCH_ROUTE.


```c title="replace_body usage"
...
replace_body("opensips", "Open SIP Server");
...
```


#### replace_all(re, txt) {#func_replace_all}


Replaces all occurrence of re with txt.


Meaning of the parameters is as follows:


- *re* - (string) Regular expression.
- *txt* (string)


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
		FAILURE_ROUTE, BRANCH_ROUTE.


```c title="replace_all usage"
...
replace_all("opensips", "Open SIP Server");
...
```


#### replace_body_all(re, txt) {#func_replace_body_all}


Replaces all occurrence of re in the body of the message
		with txt. Matching is done on a per-line basis.


Meaning of the parameters is as follows:


- *re* (string) - Regular expression.
- *txt* (string)


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
		FAILURE_ROUTE, BRANCH_ROUTE.


```c title="replace_body_all usage"
...
replace_body_all("opensips", "Open SIP Server");
...
```


#### replace_body_atonce(re, txt) {#func_replace_body_atonce}


Replaces all occurrence of re in the body of the message
		with txt. Matching is done over the whole body.


Meaning of the parameters is as follows:


- *re* (string) - Regular expression.
- *txt* (string)


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
		FAILURE_ROUTE, BRANCH_ROUTE.


```c title="replace_body_atonce usage"
...
# strip the whole body from the message:
if(has_body() && replace_body_atonce("^.+$", ""))
        remove_hf("Content-Type"); 
...
```


#### subst('/re/repl/flags') {#func_subst}


Replaces re with repl (sed or perl like).


Meaning of the parameters is as follows:


- *'/re/repl/flags'* (string) - sed like regular 
				expression. flags can be a combination of i (case insensitive),
				g (global) or s (match newline don't treat it as end of line).
're' - is regular expression
'repl' - is replacement string - may contain pseudo-variables
'flags' - substitution flags (i - ignore case, g - global)


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
		FAILURE_ROUTE, BRANCH_ROUTE.


```c title="subst usage"
...
# replace the uri in to: with the message uri (just an example)
if ( subst('/^To:(.*)sip:[^@]*@[a-zA-Z0-9.]+(.*)$/t:\1\u\2/ig') ) {};

# replace the uri in to: with the value of avp sip_address (just an example)
if ( subst('/^To:(.*)sip:[^@]*@[a-zA-Z0-9.]+(.*)$/t:\1$avp(sip_address)\2/ig') ) {};

...
```


#### subst_uri('/re/repl/flags') {#func_subst_uri}


Runs the re substitution on the message uri (like subst but works
		 only on the uri)


Meaning of the parameters is as follows:


- *'/re/repl/flags'* (string) - sed like regular 
				expression. flags can be a combination of i (case insensitive),
				g (global) or s (match newline don't treat it as end of line).
're' - is regular expression
'repl' - is replacement string - may contain pseudo-variables
'flags' - substitution flags (i - ignore case, g - global)


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
		FAILURE_ROUTE, BRANCH_ROUTE.


```c title="subst_uri usage"
...
# adds 3463 prefix to numeric uris, and save the original uri (\0 match)
# as a parameter: orig_uri (just an example)
if (subst_uri('/^sip:([0-9]+)@(.*)$/sip:3463\1@\2;orig_uri=\0/i')){$

# adds the avp 'uri_prefix' as prefix to numeric uris, and save the original
# uri (\0 match) as a parameter: orig_uri (just an example)
if (subst_uri('/^sip:([0-9]+)@(.*)$/sip:$avp(uri_prefix)\1@\2;orig_uri=\0/i')){$

...
```


#### subst_user('/re/repl/flags') {#func_subst_user}


Runs the re substitution on the message uri (like subst_uri but works
		 only on the user portion of the uri)


Meaning of the parameters is as follows:


- *'/re/repl/flags'* (string) - sed like regular
				expression. flags can be a combination of i (case insensitive),
				g (global) or s (match newline don't treat it as end of line).
're' - is regular expression
'repl' - is replacement string - may contain pseudo-variables
'flags' - substitution flags (i - ignore case, g - global)


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
		FAILURE_ROUTE, BRANCH_ROUTE.


```c title="subst usage"
...
# adds 3463 prefix to uris ending with 3642 (just an example)
if (subst_user('/3642$/36423463/')){$

...
# adds avp 'user_prefix' as prefix to username in r-uri ending with 3642
if (subst_user('/(.*)3642$/$avp(user_prefix)\13642/')){$

...
```


#### subst_body('/re/repl/flags') {#func_subst_body}


Replaces re with repl (sed or perl like) in the body of the message.


Meaning of the parameters is as follows:


- *'/re/repl/flags'* (string) - sed like regular
				expression. flags can be a combination of i (case insensitive),
				g (global) or s (match newline don't treat it as end of line).
're' - is regular expression
'repl' - is replacement string - may contain pseudo-variables
'flags' - substitution flags (i - ignore case, g - global)


This function can be used from REQUEST_ROUTE, ONREPLY_ROUTE, 
		FAILURE_ROUTE, BRANCH_ROUTE.


```c title="subst_body usage"
...
if (subst_body("/^o=([^ ]*) /o=$fU /"))
	xlog("successfully prepared an "o" line update!\n");

...
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 55 | 43 | 440 | 475 |
| 2. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 40 | 28 | 938 | 201 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 39 | 6 | 16 | 1952 |
| 4. | Andrei Dragus | 32 | 15 | 1540 | 196 |
| 5. | Andrei Pelinescu-Onciul | 28 | 21 | 446 | 134 |
| 6. | Jiri Kuthan ([@jiriatipteldotorg](https://github.com/jiriatipteldotorg)) | 18 | 14 | 293 | 45 |
| 7. | Jan Janak ([@janakj](https://github.com/janakj)) | 12 | 6 | 496 | 27 |
| 8. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 11 | 9 | 32 | 58 |
| 9. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 10 | 5 | 129 | 147 |
| 10. | Juha Heinanen ([@juha-h](https://github.com/juha-h)) | 8 | 5 | 210 | 8 |


**All remaining contributors**: Elena-Ramona Modroiu, Henning Westerholt ([@henningw](https://github.com/henningw)), Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)), Anca Vamanu, Marc Haisenko, Andreas Heise, Klaus Darilion, Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Andreas Granig, Hugues Mitonneau, Konstantin Bokarius, Saúl Ibarra Corretgé ([@saghul](https://github.com/saghul)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Edson Gellert Schubert, Maksym Sobolyev ([@sobomax](https://github.com/sobomax)), Christophe Sollet ([@csollet](https://github.com/csollet)).


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Feb 2012 - Sep 2019 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Oct 2013 - Sep 2019 |
| 3. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Jul 2019 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Feb 2002 - Apr 2019 |
| 5. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 6. | Anca Vamanu | Oct 2008 - May 2011 |
| 7. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Dec 2010 - Jan 2011 |
| 8. | Christophe Sollet ([@csollet](https://github.com/csollet)) | Dec 2010 - Dec 2010 |
| 9. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Oct 2010 - Oct 2010 |
| 10. | Andrei Dragus | Jul 2009 - Aug 2010 |


**All remaining contributors**: Saúl Ibarra Corretgé ([@saghul](https://github.com/saghul)), Hugues Mitonneau, Andreas Granig, Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Henning Westerholt ([@henningw](https://github.com/henningw)), Juha Heinanen ([@juha-h](https://github.com/juha-h)), Andreas Heise, Klaus Darilion, Marc Haisenko, Elena-Ramona Modroiu, Andrei Pelinescu-Onciul, Jan Janak ([@janakj](https://github.com/janakj)), Maksym Sobolyev ([@sobomax](https://github.com/sobomax)), Jiri Kuthan ([@jiriatipteldotorg](https://github.com/jiriatipteldotorg)).


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)), Andrei Dragus, Anca Vamanu, Andreas Granig, Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Juha Heinanen ([@juha-h](https://github.com/juha-h)), Klaus Darilion, Marc Haisenko, Elena-Ramona Modroiu, Jan Janak ([@janakj](https://github.com/janakj)), Maksym Sobolyev ([@sobomax](https://github.com/sobomax)), Jiri Kuthan ([@jiriatipteldotorg](https://github.com/jiriatipteldotorg)), Andrei Pelinescu-Onciul.
