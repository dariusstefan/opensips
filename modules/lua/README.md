---
title: "lua Module"
description: "The time needed when writing a new OpenSIPS module unfortunately is quite high, while the options provided by the configuration file are limited to the features implemented in the modules."
---

## Admin Guide


### Overview {#overview}


The time needed when writing a new OpenSIPS module
   unfortunately is quite high, while the options provided by the
   configuration file are limited to the features implemented in
   the modules.


With this Lua module, you can easily implement your own
   OpenSIPS extensions in Lua.


### Installing the module


This Lua module is loaded in opensips.cfg (just like all the
    other modules) with loadmodule("/path/to/lua.so");.


For the Lua module to compile, you need a recent version of
    Lua (tested with 5.1) linked dynamically. The default version
    of your favorite Linux distribution should work fine.


### Using the module


With the Lua module, you can access to lua function on the
    OpenSIPS side. You need to define a file to load and call
    a function from it. Write a function "mongo_alias" and then
    write in your opensips.cfg


```
...
if (lua_exec("mongo_alias")) {
	...
}
...
```


On the Lua side, you have access to opensips functions and
    variables (AVP, pseudoVar, ...). Read the documentation below
    for further informations.


### Dependencies {#dependencies}


#### OpenSIPS Modules


None ;-)


#### External Libraries or Applications


The following libraries or applications must be installed
      before running OpenSIPS with this module loaded:


- Lua 5.1.x or later
- memcached


This module has been developed and tested with Lua 5.1.?, but
      should work with any 5.1.x release. Earlier versions do not work.


On current Debian systems, at least the following packages
      should be installed:


- lua5.1
- liblua5.1-0-dev
- libmemcached-dev
- libmysqlclient-dev


It was reported that other Debian-style distributions (such as Ubuntu) need the same packages.


On OpenBSD systems, at least the following packages should be
      installed:


- Lua


### Exported Parameters {#exported_parameters}


#### luafilename (string)


This is the file name of your script. This may be set once
      only, but it may include an arbitary number of functions and
      "use" as many Lua module as necessary.


The default value is "/etc/opensips/opensips.lua"


**Example: Set luafilename parameter**


```opensips
...
modparam("lua", "luafilename", "/etc/opensips/opensips.lua")
...
        
```


#### lua_auto_reload (int)


Define this value to 1 if you want to reload automatically
      the lua script.
      Disabled by default.


#### warn_missing_free_fixup (int)


When you call a function via moduleFunc() you could have a memleak.
      Enable this warns you when you're doing it.
      Enabled by default.


#### lua_allocator (string)


Change the default memory allocator for the lua module.
      Possible values are :


- opensips (default)
- malloc


### Exported Functions {#exported_functions}


#### lua_exec(func, [param])


Calls a Lua function with passing it the current SIP message.
      This function can be used from REQUEST_ROUTE, FAILURE_ROUTE,
      ONREPLY_ROUTE and BRANCH_ROUTE.


Parameters:


- *func* (string) - Lua function name
- *param* (string, optional) - Parameter to be passed to the Lua function.


**Example: lua_exec() usage**


```
...
if (lua_exec("mongo_alias")) {
	...
}
...
```


#### lua_meminfo()


Logs informations about memory.


### Exported MI Functions {#exported_mi_functions}


#### watch {#mi_watch}


Name: *watch*


Parameters: *none*


- *action* (optional) - 'add' or 'delete'
- *extension* (optional) - required if
        *action* is provided


MI FIFO Command Format:


```
  opensips-cli -x mi watch
  
```


## OpenSIPS Lua API


### Available functions


This module provides access to a limited number of OpenSIPS
    core functions.


#### xdbg(message)


An alias for xlog(DBG, message)


#### xlog([level],message)


Logs the message with OpenSIPS's logging facility. The logging
      level is one of the following:


- ALERT
- CRIT
- ERR
- WARN
- NOTICE
- INFO
- DBG


#### WarnMissingFreeFixup


Dynamically change the variable warn_missing_free_fixup.


#### getpid


Returns the current pid.


#### getmem


Returns a table with the size of allocated memory and the fragmentation.


#### getmeminfo


Returns a table with memory infos.


#### gethostname


Returns the value of the current hostname.


#### getType(msg)


Returns "SIP_REQUEST" or "SIP_REPLY".


#### isMyself(host, port)


Test if the host and optionally the port represent one of the addresses
      that OpenSIPS listens on.


#### grepSockInfo(host, port)


Similar to isMyself(), but without taking a look into the aliases.


#### getURI_User(msg)


Returns the user of the To URI.


#### getExpires(msg)


Returns the expires header of the current message.


#### getHeader(msg, header)


Returns the value of the specified header.


#### getContact(msg)


Returns a table with the contact header.


#### getRoute(msg)


Returns a table with the Route header.


#### moduleFunc(msg, function, args1, args2, ...)


You can pass arguments to this function.


#### getStatus(msg)


Returns the current status if the SIP message is a SIP_REPLY.


#### getMethod(msg)


Returns the current method.


#### getSrcIp(msg)


Returns the IP address of the source.


#### getDstIp(msg)


Returns the IP address of the destination.


#### AVP_get(name)


Returns an AVP variable.


#### AVP_set(name, value)


Defines an AVP variable.


#### AVP_destroy(name)


Destroys an AVP variable.


#### pseudoVar(msg, variable)


Returns a pseudoVar.


#### pseudoVarSet(msg, variable, value)


Sets the value of a pseudoVar.


#### scriptVarGet(variable)


Returns a script variable.


#### scriptVarSet(variable, value)


Sets the value of a script variable.


#### add_lump_rpl(msg, header)


Add header to the reply.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Arnaud Chong + Eric Gouyer | 37 | 1 | 4335 | 0 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 34 | 27 | 274 | 230 |
| 3. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 24 | 16 | 320 | 274 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 13 | 10 | 15 | 63 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 8 | 6 | 9 | 3 |
| 6. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 5 | 3 | 7 | 9 |
| 7. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 6 | 3 |
| 8. | Julián Moreno Patiño | 3 | 1 | 1 | 1 |
| 9. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Jun 2023 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Feb 2012 - Feb 2023 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | May 2014 - Jan 2020 |
| 5. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Mar 2019 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 7. | Julián Moreno Patiño | Feb 2016 - Feb 2016 |
| 8. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Feb 2012 - Jun 2012 |
| 9. | Arnaud Chong + Eric Gouyer | Dec 2011 - Dec 2011 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Arnaud Chong + Eric Gouyer.


*Documentation Copyrights:*


Copyright © 2006-2011 Arnaud Chong, Eric Gouyer
