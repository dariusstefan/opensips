---
title: "imc Module"
description: "This module offers support for instant message conference. It follows the architecture of IRC channels, you can send commands embedded in MESSAGE body, because there are no SIP UA clients which have GUI for IM conferencing."
---

## Admin Guide


### Overview {#overview}


This module offers support for instant message conference. It
		follows the architecture of IRC channels, you can send commands
		embedded in MESSAGE body, because there are no SIP UA clients
		which have GUI for IM conferencing.


You have to define an URI corresponding to im conferencing manager, where
	user can send commands to create a new conference room. Once the conference
	room is created, users can send commands directly to conferece's URI.


To ease the integration in the configuration file, the interpreter of
	the IMC commands are embeded in the module, from configuration poin of
	view, there is only one function which has to be executed for both
	messages and commands.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *mysql*.
- *tm*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### db_url (str) {#param_db_url}


The database url.


*The default value is "mysql://opensips:opensipsrw@localhost/opensips".*


```c title="Set db_url parameter"
...
modparam("imc", "db_url", "dbdriver://username:password@dbhost/dbname")
...
```


#### rooms_table (str) {#param_rooms_table}


The name of the table storing IMC rooms.


*The default value is "imc_rooms".*


```c title="Set rooms_table parameter"
...
modparam("imc", "rooms_table", "rooms")
...
```


#### members_table (str) {#param_members_table}


The name of the table storing IMC members.


*The default value is "imc_members".*


```c title="Set members_table parameter"
...
modparam("imc", "rooms_table", "members")
...
```


#### hash_size (integer) {#param_hash_size}


The power of 2 to get the size of the hash table used for storing
		members and rooms.


*The default value is 4 (resultimg in hash size 16).*


```c title="Set hash_size parameter"
...
modparam("imc", "hash_size", 8)
...
```


#### imc_cmd_start_char (str) {#param_imc_cmd_start_char}


The character which indicates that the body of the message is a command.


*The default value is "#".*


```c title="Set imc_cmd_start_char parameter"
...
modparam("imc", "imc_cmd_start_char", "#")
...
```


#### outbound_proxy (str) {#param_outbound_proxy}


The SIP address used as next hop when sending the message. Very
   useful when using OpenSIPS with a domain name not in DNS, or
   when using a separate OpenSIPS instance for imc processing. If
   not set, the message will be sent to the address in destination
   URI.


*Default value is NULL.*


```c title="Set outbound_proxy parameter"
...
modparam("imc", "outbound_proxy", "sip:opensips.org;transport=tcp")
...
```


### Exported Functions {#exported_functions}


#### imc_manager() {#func_imc_manager}


Handles Message method.It detects if the body of the message is a
		conference command.If so it executes it, otherwise it sends the
		message to all the members in the room.


This function can be used from REQUEST_ROUTE.


```c title="Usage of imc_manager() function"
...
# the rooms will be named chat-xyz to avoid overlapping
# with usernames
if(is_method("MESSAGE)
        && ($ru=~ "sip:chat-[0-9]+@" || ($ru=~ "sip:chat-manager@")
    imc_manager();
...
```


### Exported MI Functions {#exported_mi_functions}


#### imc:list_rooms {#mi_list_rooms}


Replaces obsolete MI command: *imc_list_rooms*.


Lists of the IM Conferencing rooms.


Name: *imc:list_rooms*


Parameters: none


MI FIFO Command Format:


```c
		opensips-cli -x mi imc:list_rooms
		
```


#### imc:list_members {#mi_list_members}


Replaces obsolete MI command: *imc_list_members*.


Listing of the members in IM Conferencing rooms.


Name: *imc:list_members*


Parameters:


- *room* : the room for which you want to list the members


MI FIFO Command Format:


```c
		opensips-cli -x mi imc:list_members sip:chat-000@opensips.org
		
```


### Exported Statistics


#### active_rooms {#stat_active_rooms}


Number of active IM Conferencing rooms.


### IMC Commands


A command is identified by the starting character. A command must be
		written in one line. By default, the starting character is '#'. You
		can change it via "imc_cmd_start_char" parameter.


Next picture presents the list of commands and their parameters.


```c title="List of commands"
...

1.create
  -creates a conference room
  -takes 2 parameters:
     1) the name of the room
     2)optional- "private" -if present the created room is private
	   and new members can be added only though invitations
  -the user is added as the first member and owner of the room
  -eg:  #create chat-000 private

2.join
  -makes the user member of a room
  -takes one optional parameter - the address of the room -if not
    present it will be considered to be the address in the To
    header of the message
  -if the room does not exist the command is treated as create
  -eg:join sip:chat-000@opensips.org,
      or just, #join, sent to sip:chat-000@opensips.org

3.invite
  -invites a user to become a member of a room
  -takes 2 parameters:
     1)the complete address of the user
     2)the address of the room -if not present it will be considered
	   to be the address in the To header of the message
  -only certain users have the right to invite other user: the owner
    and the administrators
  -eg: #invite sip:john@opensips.org sip:chat-000@opensips.org
    or  #invite john@opensips.org sent to sip:chat-000@opensips.org

4.accept
  -accepting an invitation
  -takes one optional parameter - the address of the room - if not
    present it will be considered to be the address in the To header
    of the message
  -eg: #accept sip:john@opensips.org

5.deny
  -rejects an invitation
  -the parameter is the same as for accept

6.remove
  -deletes a member from a room
  -takes 2 parameters:
    1)the complete address of the member
    2)the address of the room -if not present it will be considered
	  to be the address in the To header of the message
  -only certain members have the right to remove other members
  -eg: #remove sip:john@opensips.org, sent to sip:chat-000@opensips.org

7.exit
  -leaving a room
  -takes one optional parameter - the address of the room - if not
    present it will be considered to be the address in the To header
    of the message
  -if the user is the owner of the room, the room will be destroyed

8.destroy
  -removing a room
  -the parameter is the same as for exit
  -only the owner of a room has the right to destroy it

9.list
  -list members in a room

...
```


### Installation


Before running OpenSIPS with IMC, you have to setup the database 
		tables where the module will store the data. For that, if the 
		tables were not created by the installation script or you choose
		to install everything by yourself you can use the imc-create.sql
		SQL script in the database directories in the 
		opensips/scripts folder as template. 
		You can also find the complete database documentation on the
		project webpage, [https://opensips.org/docs/db/db-schema-devel.html](https://opensips.org/docs/db/db-schema-devel.html).


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 43 | 30 | 313 | 553 |
| 2. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 43 | 12 | 1604 | 1007 |
| 3. | Anca Vamanu | 32 | 5 | 3014 | 16 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 27 | 23 | 140 | 84 |
| 5. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 15 | 12 | 39 | 66 |
| 6. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 10 | 7 | 76 | 105 |
| 7. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 9 | 6 | 75 | 80 |
| 8. | Elena-Ramona Modroiu | 4 | 2 | 68 | 5 |
| 9. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 5 | 6 |
| 10. | Alexandra Titoc | 4 | 2 | 4 | 2 |


**All remaining contributors**: John Riordan, Sergio Gutierrez, Konstantin Bokarius, Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Edson Gellert Schubert, Walter Doekes ([@wdoekes](https://github.com/wdoekes)).


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Sep 2011 - May 2026 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Oct 2006 - Apr 2026 |
| 3. | Alexandra Titoc | Sep 2024 - Sep 2024 |
| 4. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 5. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Apr 2021 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 8. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | May 2014 - May 2014 |
| 9. | John Riordan | May 2009 - May 2009 |
| 10. | Sergio Gutierrez | Nov 2008 - Nov 2008 |


**All remaining contributors**: Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Henning Westerholt ([@henningw](https://github.com/henningw)), Anca Vamanu, Elena-Ramona Modroiu.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Anca Vamanu, Henning Westerholt ([@henningw](https://github.com/henningw)), Elena-Ramona Modroiu.
