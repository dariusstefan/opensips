---
title: "exec Module"
description: "The Exec module enables the execution of external commands from the OpenSIPS script. Any valid shell commands are accepted. The final input string is evaluated and executed using the \"/bin/sh\" symlink/binary. OpenSIPS may additionally pass a lot more information about the request using en..."
---

## Admin Guide


### Overview {#overview}


The Exec module enables the execution of external commands from the
		OpenSIPS script. Any valid shell commands are accepted. The final input
		string is evaluated and executed using the "/bin/sh" symlink/binary.
		OpenSIPS may additionally pass a lot more information about the request
		using environment variables:


- SIP_HF_<hf_name> contains value of each header field in
			request. If a header field occurred multiple times, values are
			concatenated and comma-separated. <hf_name> is in capital
			letters. Ff a header-field name occurred in compact form,
			<hf_name> is canonical.
- SIP_TID is transaction identifier. All request retransmissions or
			CANCELs/ACKs associated with a previous INVITE result in the same
			value.
- SIP_DID is dialog identifier, which is the same as to-tag.
			Initially, it is empty.
- SIP_SRCIP is source IP address from which request came.
- SIP_ORURI is original request URI.
- SIP_RURI is *current* request URI (if
			unchanged, equal to original).
- SIP_USER is userpart of *current* request URI.
- SIP_OUSER is userpart of original request URI.


NOTE: Any environment variables which are given to the exec module
		functions must be specified using the '$$' delimiter (e.g., $$SIP_OUSER),
		otherwise they will be evaluated as OpenSIPS pseudo-variables,
		throwing scripting errors.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following  modules must be loaded before this module:


- *No dependencies on other OpenSIPS modules*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### setvars (integer) {#param_setvars}


Set to 1 to enable setting all above-mentioned environment variables
		for all executed commands.


**WARNING: Before enabling this parameter, make sure
		your "/bin/sh" is safe from the Shellshock bash vulnerability!!!**


*Default value is 0 (disabled).*


```c title="Set 'setvars' parameter"
...
modparam("exec", "setvars", 1)
...
```


#### time_to_kill (integer) {#param_time_to_kill}


If set, this parameter specifies the longest time (in seconds) that a
		program is allowed to execute. Once this duration is exceeded, the
		program is terminated (SIGTERM).


NOTE: due to internal limitations, a SIGTERM will actually be sent to
		**all** job pids once the "time_to_kill"
		expiration timeout hits. On a standard system, this should have no
		side-effects, as pids are monotonically increasing in a slow manner,
		and OpenSIPS should run under the "opensips" user, thus rendering it
		unable to terminate non-child processes. If this is not the case on
		your system, do not use the OpenSIPS "time_to_kill" feature -- rather
		implement it within your external app!


*Default value is 0 (disabled).*


```c title="Set 'time_to_kill' parameter"
...
modparam("exec", "time_to_kill", 20)
...
```


### Exported Functions {#exported_functions}


#### exec(command, [stdin], [stdout], [stderr], [envavp]) {#func_exec}


Executes an external command. The input is passed to the standard input of the new
		process, if specified, and the output is saved in the output variable.


The function waits for the external script until it provided all its output (not
		necessary to actually finish). If no output (standard output or standard error)
		is required by the function, it will not block at all - it will simply launch the
		external script and continue the script.


Meaning of the parameters is as follows:


- *command (string)* - command to be executed
- *stdin (string, optional)* - string to be
				passed to the standard input of the command
- *stdout (var, optional)* - optional
				output variable which will hold the standard output of the
				process
- *stderr (var, optional)* - optional
				output variable which will hold the standard error of the
				process
- *envavp (var, optional)* - optional AVP
				which holds the values for the
			environment variables to be passed for the command. The names of the environment
			variables will be "OSIPS_EXEC_#", where "#" starts from 0. For example, if we
			push two values (e.g. "b" and "a") into an AVP variable, which acts like a stack,
			OSIPS_EXEC_0 will hold "a", while OSIPS_EXEC_1 will hold "b".


NOTE: If expecting a multi-line formatted output, you should use $avp
		variables for the "stdout" and "stderr" parameters, to avoid only
		receiving the last lines of each stream.


WARNING: any OpenSIPS pseudo-vars which may contain special bourne shell (sh/bash)
		characters should be placed inside quotes, e.g.
		exec("update-stats.sh '$(ct{re.subst,/'//g})'");


WARNING: "stdin"/"stdout"/"stderr" parameters are not designed for large amounts of
		data, so one should be careful when using them. Because of the basic implementation,
		filled up pipes could cause a read deadlock.


This function can be used from REQUEST_ROUTE, FAILURE_ROUTE,
		LOCAL_ROUTE, STARTUP_ROUTE, TIMER_ROUTE, EVENT_ROUTE, ONREPLY_ROUTE.


```c title="exec usage"
...
$avp(env) = "a";
$avp(env) = "b";
exec("ls -l", , $var(out), $var(err), $avp(env));
xlog("The output is $var(out)\n");
xlog("Received the following error\n$var(err)");
...
$var(input) = "input";
exec("/home/../myscript.sh", "this is my $var(input) for exec\n", , , $avp(env));
...
```


### Exported Asyncronous Functions


#### exec(command, [stdin], [stdout], [stderr], [envavp]) {#afunc_exec}


Executes an external command. This function does exactly the same as
		[exec](#func_exec) (in terms of input, output and processing),
		but in an asynchronous way. The script execution is suspended until
		the external script provided all its output. OpenSIPS waits for the
		external script to close its output stream, not necessarily to
		terminate (so the script may still be running when OpenSIPS
		resumes the script execution on "seeing" EOF on the the output stream)


NOTE: this function ignore the "stderr" parameter for now - the
		asynchronous waiting is done only on the output stream !! This may
		be fixed in the following versions.


To read and understand more on the asynchronous functions, how to use
		them and what are their advantages, please refer to the OpenSIPS 
		online Manual.


```c title="async exec usage"
{
...
async(exec("ruri-changer.sh", $ru, $ru), resume);
}

route [resume] {
...
}
```


### Known Issues


When imposing an execution timeout using
		**[time to kill](#param_time_to_kill)**,
		make sure your "/bin/sh" is a shell which does not fork when executed,
		case in which the job itself will not be killed, but rather its parent shell,
		while the job is silently inherited by "init" and will continue to run.
		"/bin/dash" is one of these troublesome shell environments.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 44 | 29 | 651 | 534 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 40 | 23 | 344 | 827 |
| 3. | Jiri Kuthan ([@jiriatipteldotorg](https://github.com/jiriatipteldotorg)) | 28 | 11 | 1579 | 152 |
| 4. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 26 | 19 | 440 | 131 |
| 5. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 20 | 16 | 320 | 59 |
| 6. | Jan Janak ([@janakj](https://github.com/janakj)) | 16 | 10 | 463 | 111 |
| 7. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | 15 | 7 | 612 | 117 |
| 8. | Andrei Pelinescu-Onciul | 11 | 8 | 29 | 105 |
| 9. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 8 | 2 | 46 | 297 |
| 10. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 4 | 2 | 8 | 8 |


**All remaining contributors**: Walter Doekes ([@wdoekes](https://github.com/wdoekes)), Maksym Sobolyev ([@sobomax](https://github.com/sobomax)), Zero King ([@l2dy](https://github.com/l2dy)), Anca Vamanu, Dan Pascu ([@danpascu](https://github.com/danpascu)), Elena-Ramona Modroiu, Konstantin Bokarius, Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Andreas Granig, Julián Moreno Patiño, Octavian Cerna, Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Edson Gellert Schubert, Dror Wald.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Feb 2014 - May 2024 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jul 2004 - Apr 2020 |
| 4. | Zero King ([@l2dy](https://github.com/l2dy)) | Mar 2020 - Mar 2020 |
| 5. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Jun 2011 - Jan 2020 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 8. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | Oct 2014 - Feb 2017 |
| 9. | Octavian Cerna | Oct 2016 - Oct 2016 |
| 10. | Julián Moreno Patiño | Feb 2016 - Feb 2016 |


**All remaining contributors**: Walter Doekes ([@wdoekes](https://github.com/wdoekes)), Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Anca Vamanu, Dror Wald, Dan Pascu ([@danpascu](https://github.com/danpascu)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Henning Westerholt ([@henningw](https://github.com/henningw)), Elena-Ramona Modroiu, Andreas Granig, Jan Janak ([@janakj](https://github.com/janakj)), Andrei Pelinescu-Onciul, Jiri Kuthan ([@jiriatipteldotorg](https://github.com/jiriatipteldotorg)).


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Walter Doekes ([@wdoekes](https://github.com/wdoekes)), Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Anca Vamanu, Dror Wald, Dan Pascu ([@danpascu](https://github.com/danpascu)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Elena-Ramona Modroiu, Jan Janak ([@janakj](https://github.com/janakj)).


*Documentation Copyrights:*


Copyright © 2003 FhG FOKUS
