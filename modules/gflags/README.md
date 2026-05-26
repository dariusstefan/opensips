---
title: "gflags Module"
description: "gflags module (global flags) keeps a bitmap of flags in shared memory and may be used to change behaviour of server based on value of the flags. Example: ``` if (is_gflag(1)) { t_relay(\"udp:10.0.0.1:5060\"); } else { t_relay(\"udp:10.0.0.2:5060\"); } ```"
---

## Admin Guide


### Overview


gflags module (global flags) keeps a bitmap of flags in shared memory
	and may be used to change behaviour of server based on value of the flags.
	Example:


```opensips
	if (is_gflag(1)) {
		t_relay("udp:10.0.0.1:5060");
	} else {
		t_relay("udp:10.0.0.2:5060");
	}
	
```


The benefit of this module is the value of the switch flags
	can be manipulated by external applications such as web interface
	or command line tools. The size of bitmap is 32.


The module exports external commands that can be used to change
	the global flags via Management Interface. The MI commands are:
	"set_gflag", "reset_gflag" and
	"is_gflag".


### Dependencies


The module depends on the following modules (in the other words the
		listed modules must be loaded before this module):


- *none*


### Exported Parameters


#### initial (integer)


The initial value of global flags bitmap.


Default value is "0".


**Example: initial parameter usage**


```opensips
modparam("gflags", "initial", 15)
		
```


### Exported Functions


#### set_gflag(flag)


Set the bit at the position "flag" in global flags.


The "flag" (int) parameter can have a value in the range of 0..31.


This function may be used from any route.


**Example: set_gflag() usage**


```
...
set_gflag(4);
...
```


#### reset_gflag(flag)


Reset the bit at the position "flag" in global flags.


The "flag" (int) parameter can have a value in the range of 0..31.


This function may be used from any route.


**Example: reset_gflag() usage**


```
...
reset_gflag(4);
...
```


#### is_gflag(flag)


Check if bit at the position "flag" in global flags is
		set.


The "flag" (int) parameter can have a value in the range of 0..31.


This function may be used from any route.


**Example: is_gflag() usage**


```
...
if(is_gflag(4))
{
	log("global flag 4 is set\n");
} else {
	log("global flag 4 is not set\n");
};
...
```


### Exported MI Functions


Functions that check or change some flags accepts one parameter 
			which is the flag bitmap/mask specifing the corresponding flags.
			It is not possible to specify directly the flag position that 
			should be changed as in the functions available in the routing 
			script.


#### set_gflag


Set the value of some flags (specified by bitmask) to 1.


The parameter value must be a bitmask in decimal or hexa format.
			The bitmaks has a 32 bit size.


**Example: set_gflag usage**


```
...
$ opensips-cli -x mi set_gflag 1
$ opensips-cli -x mi set_gflag 0x3
...
```


#### reset_gflag


Reset the value of some flags to 0.


The parameter value must be a bitmask in decimal or hexa format.
			The bitmaks has a 32 bit size.


**Example: reset_gflag usage**


```
...
$ opensips-cli -x mi reset_gflag 1
$ opensips-cli -x mi reset_gflag 0x3
...
```


#### is_gflag


Returns true if the all the flags from the bitmask are set.


The parameter value must be a bitmask in decimal or hexa format.
			The bitmaks has a 32 bit size.


The function returns TRUE if all the flags from the set are set
			and FALSE if at least one is not set.


**Example: is_gflag usage**


```
...
$ opensips-cli -x mi set_gflag 1024
$ opensips-cli -x mi is_gflag 1024
TRUE
$ opensips-cli -x mi is_gflag 1025
TRUE
$ opensips-cli -x mi is_gflag 1023
FALSE
$ opensips-cli -x mi set_gflag 0x10
$ opensips-cli -x mi is_gflag 1023
TRUE
$ opensips-cli -x mi is_gflag 1007
FALSE
$ opensips-cli -x mi is_gflag 16
TRUE
...
```


#### get_gflags


Return the bitmap with all flags. The function gets no 
			parameters and returns the bitmap in hexa and decimal format.


**Example: get_gflags usage**


```
...
$ opensips-cli -x mi get_gflags
0x3039
12345
...
```


## Contributors


### By Commit Statistics


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 41 | 31 | 423 | 326 |
| 2. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 14 | 12 | 42 | 22 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 13 | 11 | 27 | 59 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 10 | 8 | 26 | 24 |
| 5. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 8 | 6 | 42 | 31 |
| 6. | Jiri Kuthan ([@jiriatipteldotorg](https://github.com/jiriatipteldotorg)) | 8 | 4 | 278 | 4 |
| 7. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 7 | 4 | 88 | 94 |
| 8. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 3 | 4 |
| 9. | Richard Revels | 3 | 1 | 24 | 11 |
| 10. | Anca Vamanu | 3 | 1 | 6 | 3 |


**All remaining contributors**: Konstantin Bokarius, Andrei Pelinescu-Onciul, Dan Pascu ([@danpascu](https://github.com/danpascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Edson Gellert Schubert, Klaus Darilion, Ancuta Onofrei.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - May 2024 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Oct 2005 - Oct 2022 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Sep 2011 - Sep 2019 |
| 5. | Dan Pascu ([@danpascu](https://github.com/danpascu)) | May 2019 - May 2019 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 8. | Anca Vamanu | Sep 2009 - Sep 2009 |
| 9. | Richard Revels | Aug 2008 - Aug 2008 |
| 10. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | Oct 2005 - Jun 2008 |


**All remaining contributors**: Konstantin Bokarius, Edson Gellert Schubert, Henning Westerholt ([@henningw](https://github.com/henningw)), Ancuta Onofrei, Klaus Darilion, Andrei Pelinescu-Onciul, Jiri Kuthan ([@jiriatipteldotorg](https://github.com/jiriatipteldotorg)).


*(1) including any documentation-related commits, excluding merge commits*


## Documentation


### Contributors


**Last edited by:** Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Richard Revels, Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Henning Westerholt ([@henningw](https://github.com/henningw)), Klaus Darilion.


*Documentation Copyrights:*


Copyright © 2004 FhG FOKUS
