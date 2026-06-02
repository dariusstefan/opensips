---
title: "MSILO Module"
description: "This modules provides offline message storage for the Open SIP Server. It stores received messages for an offline user and sends them when the user becomes online."
---

## Admin Guide


### Overview {#overview}


This modules provides offline message storage for the Open SIP Server. It 
		stores received messages for an offline user and sends them when the 
		user becomes online.


For each message, the modules stores "Request-URI" 
		("R-URI") only if it is a complete address of record 
		("username@hostname"), URI from "To" 
		header, URI from "From" header, incoming time,
		expiration time, content type and body of the message. If 
		"R-URI" is not an address of record (it might be the 
		contact address for current SIP session) the URI
		from "To" header will be used as R-URI.


When the expiration time passed, the message is discarded from 
		database.  Expiration time is computed based on incoming time and 
		one of the module's parameters.


Every time when a user registers with OpenSIPS, the module is looking in 
		database for offline messages intended for that user. All of them will 
		be sent to contact address provided in REGISTER request.


It may happen the SIP user to be registered but his SIP User Agent 
		to have no support for MESSAGE request. In this case it should be used 
		the "failure_route" to store the undelivered requests.


Another functionality provided by the modules is to send messages at
		a certain time -- the reminder functionality. Using config logic, a
		received message can be stored and delivered at a time specified while
		storing with the 'snd_time_avp'.


### Dependencies {#dependencies}


#### OpenSIPS modules


The following modules must be loaded before this module:


- *database module* - mysql, dbtext or other 
				module that implements the "db" interface and 
				provides support for storing/receiving data to/from a 
				database system.
- *TM*--transaction module--is used to 
				send SIP requests.


#### External libraries or applications


The following libraries or applications must be installed before 
		running OpenSIPS with this module:


- *none*.


### Exported Parameters {#exported_parameters}


#### db_url (string) {#param_db_url}


Database URL.


*Default value is 
			"mysql://opensips:opensipsrw@localhost/opensips".*


```c title="Set the 'db_url' parameter"
...
modparam("msilo", "db_url", "mysql://user:passwd@host.com/dbname")
...
```


#### db_table (string) {#param_db_table}


The name of table where to store the messages.


*Default value is "silo".*


```c title="Set the 'db_table' parameter"
...
modparam("msilo", "db_table", "silo")
...
```


#### from_address (string) {#param_from_address}


The SIP address used to inform users that destination of their 
		message is not online and the message will be delivered next time 
		when that user goes online. If the parameter is not set, the module 
		will not send any notification. It can contain pseudo-variables.


*Default value is "NULL".*


```c title="Set the 'from_address' parameter"
...
modparam("msilo", "from_address", "sip:registrar@example.org")
modparam("msilo", "from_address", "sip:$rU@example.org")
...
```


#### contact_hdr (string) {#param_contact_hdr}


The value of the Contact header (including header name and ending
		\r\n) to be added in notification messages.
		It can contain pseudo-variables.


*Default value is "NULL".*


```c title="Set the 'contact_hdr' parameter"
...
modparam("msilo", "contact_hdr", "Contact: <sip:null@example.com>\r\n")
...
```


#### offline_message (string) {#param_offline_message}


The body of the notification message.
		It can contain pseudo-variables.


*Default value is "NULL".*


```c title="Set the 'offline_message' parameter"
...
modparam("msilo", "offline_message", "*** User $rU is offline!")
modparam("msilo", "offline_message", "<em>I am offline!</em>")
...
```


#### content_type_hdr (string) {#param_content_type_hdr}


The value of the Content-Type header (including header name and ending
		\r\n) to be added in notification messages. It must reflect what the
		'offline_message' contains.
		It can contain pseudo-variables.


*Default value is "NULL".*


```c title="Set the 'content_type_hdr' parameter"
...
modparam("msilo", "content_type_hdr", "Content-Type: text/plain\r\n")
modparam("msilo", "content_type_hdr", "Content-Type: text/html\r\n")
...
```


#### reminder (string) {#param_reminder}


The SIP address used to send reminder messages. If this value
		is not set, the reminder feature is disabled.


*Default value is "NULL".*


```c title="Set the 'reminder' parameter"
...
modparam("msilo", "reminder", "sip:registrar@example.org")
...
```


#### outbound_proxy (string) {#param_outbound_proxy}


The SIP address used as next hop when sending the message. Very
		useful when using OpenSIPS with a domain name not in DNS, or when
		using a separate OpenSIPS instance for msilo processing. If not set,
		the message will be sent to the address in destination URI.


*Default value is "NULL".*


```c title="Set the 'outbound_proxy' parameter"
...
modparam("msilo", "outbound_proxy", "sip:opensips.org;transport=tcp")
...
```


#### expire_time (int) {#param_expire_time}


Expire time of stored messages - seconds. When this time passed, the message is
		silently discarded from database.


*Default value is "259200 (72 hours = 3 days)".*


```c title="Set the 'expire_time' parameter"
...
modparam("msilo", "expire_time", 36000)
...
```


#### check_time (int) {#param_check_time}


Timer interval to check if dumped messages are sent OK - seconds. The module keeps
		each request send by itself for a new online user and if the reply is 2xx then the
		message is deleted from database.


*Default value is "30".*


```c title="Set the 'check_time' parameter"
...
modparam("msilo", "check_time", 10)
...
```


#### send_time (int) {#param_send_time}


Timer interval in seconds to check if there are reminder messages.
		The module takes all reminder messages that must be sent at that moment 
		or before that moment.


If the value is 0, the reminder feature is disabled.


*Default value is "0".*


```c title="Set the 'send_time' parameter"
...
modparam("msilo", "send_time", 60)
...
```


#### clean_period (int) {#param_clean_period}


Number of "check_time" cycles when to check if
		there are expired messages in database.


*Default value is "5".*


```c title="Set the 'clean_period' parameter"
...
modparam("msilo", "clean_period", 3)
...
```


#### use_contact (int) {#param_use_contact}


Turns on/off the usage of the Contact address to send notification
		back to sender whose message is stored by MSILO.


*Default value is "1 (0 = off, 1 = on)".*


```c title="Set the 'use_contact' parameter"
...
modparam("msilo", "use_contact", 0)
...
```


#### sc_mid (string) {#param_sc_mid}


The name of the column in silo table, storing message id.


Default value is "mid".


```c title="Set the 'sc_mid' parameter"
...
modparam("msilo", "sc_mid", "other_mid")
...
```


#### sc_from (string) {#param_sc_from}


The name of the column in silo table, storing the source address.


Default value is "src_addr".


```c title="Set the 'sc_from' parameter"
...
modparam("msilo", "sc_from", "source_address")
...
```


#### sc_to (string) {#param_sc_to}


The name of the column in silo table, storing the destination address.


Default value is "dst_addr".


```c title="Set the 'sc_to' parameter"
...
modparam("msilo", "sc_to", "destination_address")
...
```


#### sc_uri_user (string) {#param_sc_uri_user}


The name of the column in silo table, storing the user name.


Default value is "username".


```c title="Set the 'sc_uri_user' parameter"
...
modparam("msilo", "sc_uri_user", "user")
...
```


#### sc_uri_host (string) {#param_sc_uri_host}


The name of the column in silo table, storing the domain.


Default value is "domain".


```c title="Set the 'sc_uri_host' parameter"
...
modparam("msilo", "sc_uri_host", "domain")
...
```


#### sc_body (string) {#param_sc_body}


The name of the column storing the message body in silo table.


Default value is "body".


```c title="Set the 'sc_body' parameter"
...
modparam("msilo", "sc_body", "message_body")
...
```


#### sc_ctype (string) {#param_sc_ctype}


The name of the column in silo table, storing content type.


Default value is "ctype".


```c title="Set the 'sc_ctype' parameter"
...
modparam("msilo", "sc_ctype", "content_type")
...
```


#### sc_exp_time (string) {#param_sc_exp_time}


The name of the column in silo table, storing the expire time of the message.


Default value is "exp_time".


```c title="Set the 'sc_exp_time' parameter"
...
modparam("msilo", "sc_exp_time", "expire_time")
...
```


#### sc_inc_time (string) {#param_sc_inc_time}


The name of the column in silo table, storing the incoming time of the message.


Default value is "inc_time".


```c title="Set the 'sc_inc_time' parameter"
...
modparam("msilo", "sc_inc_time", "incoming_time")
...
```


#### sc_snd_time (string) {#param_sc_snd_time}


The name of the column in silo table, storing the send time for the reminder.


Default value is "snd_time".


```c title="Set the 'sc_snd_time' parameter"
...
modparam("msilo", "sc_snd_time", "send_reminder_time")
...
```


#### snd_time_avp (str) {#param_snd_time_avp}


The name of an AVP which may contain the time when to sent
		the received message as reminder.The AVP is used ony by m_store().


If the parameter is not set, the module does not look for this AVP. If
		the value is set to a valid AVP name, then the module expects in the AVP
		to be a time value in format YYYYMMDDHHMMSS (e.g., 20060101201500).


*Default value is "null".*


```c title="Set the 'snd_time_avp' parameter"
...
modparam("msilo", "snd_time_avp", "$avp(snd_time)")
...
```


#### add_date (int) {#param_add_date}


Wheter to add as prefix the date when the message was stored.


*Default value is "1" (1==on/0==off).*


```c title="Set the 'add_date' parameter"
...
modparam("msilo", "add_date", 0)
...
```


#### max_messages (int) {#param_max_messages}


Maximum number of stored message for an AoR.  Value 0
		equals to no limit.


*Default value is 0.*


```c title="Set the 'max_messages' parameter"
...
modparam("msilo", "max_messages", 0)
...
```


### Exported Functions {#exported_functions}


#### m_store([owner]) {#func_m_store}


The method stores certain parts of the current SIP request (it 
		should be called when the request type is MESSAGE and the destination 
		user is offline or his UA does not support MESSAGE requests). If the 
		user is registered with a UA which does not support MESSAGE requests 
		you should not use mode="0" if you have
		changed the request uri with the contact address of user's UA.


Meaning of the parameters is as follows:


- *owner* (string, optional) - a SIP URI in whose
			inbox the message will be stored. If "owner" is missing,
			the SIP address is taken from R-URI.


This function can be used from REQUEST_ROUTE, FAILURE_ROUTE.


```c title="m_store usage"
...
m_store();
m_store($tu);
...
```


#### m_dump([owner], [maxmsg]) {#func_m_dump}


The method sends stored messages for the SIP user that is going to 
		register to his actual contact address. The method should be called 
		when a REGISTER request is received and the "Expire" 
		header has a value greater than zero.


Meaning of the parameters is as follows:


- *owner* (string, optional) - 
			a SIP URI whose inbox will be dumped. If "owner" is missing,
			the SIP address is taken from To URI.
- *maxmsg* (int, optional) - is a maximum number of messages
			to be dumped.


This function can be used from REQUEST_ROUTE, STARTUP_ROUTE,
		TIMER_ROUTE, EVENT_ROUTE


```c title="m_dump usage"
...
m_dump();
m_dump($fu);
m_dump($fu, 10);
...
```


### Exported Statistics


#### stored_messages {#stat_stored_messages}


The number of messages stored by msilo.


#### dumped_messages {#stat_dumped_messages}


The number of dumped messages.


#### failed_messages {#stat_failed_messages}


The number of failed dumped messages.


#### dumped_reminders {#stat_dumped_reminders}


The number of dumped reminder messages.


#### failed_reminders {#stat_failed_reminders}


The number of failed reminder messages.


### Installation and Running


#### OpenSIPS config file


Next picture displays a sample usage of msilo.


```c title="OpenSIPS config script - sample msilo usage"
...
#
# MSILO usage example
#
#


# running in debug mode (log level 4, log to stderr, stay in foreground)
debug_mode=yes

check_via=no      # (cmd. line: -v)
dns=off           # (cmd. line: -r)
rev_dns=off       # (cmd. line: -R)
port=5060

socket=10.0.0.2   # listen address

# ------------------ module loading ----------------------------------
mpath="/usr/local/lib/opensips/modules/"

loadmodule "textops.so"

loadmodule "sl.so"
loadmodule "mysql.so"
loadmodule "maxfwd.so"
loadmodule "msilo.so"
loadmodule "tm.so"
loadmodule "registrar.so"
loadmodule "usrloc.so"

# ----------------- setting module-specific parameters ---------------

# -- registrar params --

modparam("registrar", "default_expires", 120)

# -- registrar params --

modparam("usrloc", "db_mode", 0)

# -- msilo params --

modparam("msilo","db_url","mysql://opensips:opensipsrw@localhost/opensips")
modparam("msilo","from_address","sip:registrar@opensips.org")
modparam("msilo","contact_hdr","Contact: registrar@192.168.1.2:5060;msilo=yes\r\n")
modparam("msilo","content_type_hdr","Content-Type: text/plain\r\n")
modparam("msilo","offline_message","*** User $rU is offline!")

# -- tm params --

modparam("tm", "fr_timer", 10 )
modparam("tm", "fr_inv_timer", 15 )
modparam("tm", "wt_timer", 10 )


route{
    if ( !mf_process_maxfwd_header(10) )
    {
        sl_send_reply(483, "Too Many Hops");
        exit;
    };


    if (is_myself("$rd")) {
    {
        # for testing purposes, simply okay all REGISTERs
        if ($rm=="REGISTER")
        {
            save("location");
            log("REGISTER received -> dumping messages with MSILO\n");

            # MSILO - dumping user's offline messages
            if (m_dump())
            {
                log("MSILO: offline messages dumped - if they were\n");
            }else{
                log("MSILO: no offline messages dumped\n");
            };
            exit;
        };

        # domestic SIP destinations are handled using our USRLOC DB
        
        if(!lookup("location")) 
        {
            if (! t_newtran())
            {
                sl_reply_error();
                exit;
            };
            # we do not care about anything else but MESSAGEs
            if (!$rm=="MESSAGE")
            {
                if (!t_reply(404, "Not found")) 
                {
                    sl_reply_error();
                };
                exit;
            };
            log("MESSAGE received -> storing using MSILO\n");
            # MSILO - storing as offline message
            if (m_store("$ru"))
            {
                log("MSILO: offline message stored\n");
                if (!t_reply(202, "Accepted")) 
                {
                    sl_reply_error();
                };
            }else{
                log("MSILO: offline message NOT stored\n");
                if (!t_reply(503, "Service Unavailable")) 
                {
                    sl_reply_error();
                };
            };
            exit;
        };
        # if the downstream UA does not support MESSAGE requests
        # go to failure_route[1]
        t_on_failure("1");
        t_relay();
        exit;
    };

    # forward anything else
    t_relay();
}

failure_route[1] {
    # forwarding failed -- check if the request was a MESSAGE 
    if (!$rm=="MESSAGE")
    {
        exit;
    };
    
    log(1,"MSILO:the downstream UA doesn't support MESSAGEs\n");
    # we have changed the R-URI with the contact address, ignore it now
    if (m_store("$ou"))
    {
        log("MSILO: offline message stored\n");
        t_reply(202, "Accepted"); 
    }else{
        log("MSILO: offline message NOT stored\n");
        t_reply(503, "Service Unavailable");
    };
}



...
		
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 125 | 66 | 4163 | 1410 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 45 | 38 | 191 | 277 |
| 3. | Andrei Pelinescu-Onciul | 18 | 10 | 115 | 382 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 17 | 14 | 73 | 89 |
| 5. | Jan Janak ([@janakj](https://github.com/janakj)) | 16 | 11 | 126 | 168 |
| 6. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 12 | 10 | 33 | 28 |
| 7. | Jiri Kuthan ([@jiriatipteldotorg](https://github.com/jiriatipteldotorg)) | 9 | 6 | 180 | 33 |
| 8. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 9 | 6 | 114 | 110 |
| 9. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 9 | 5 | 69 | 123 |
| 10. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 7 | 4 | 90 | 80 |


**All remaining contributors**: Juha Heinanen ([@juha-h](https://github.com/juha-h)), Andrea Giordana, Ancuta Onofrei, Elena-Ramona Modroiu, Maksym Sobolyev ([@sobomax](https://github.com/sobomax)), Aron Rosenberg, John Riordan, Alexandra Titoc, Konstantin Bokarius, Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)), Julián Moreno Patiño, Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Sergio Gutierrez, UnixDev, Zero King ([@l2dy](https://github.com/l2dy)), Edson Gellert Schubert, Stanislaw Pitucha.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Alexandra Titoc | Sep 2024 - Sep 2024 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - May 2024 |
| 3. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 4. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Apr 2022 - Apr 2022 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Sep 2002 - Oct 2021 |
| 6. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Jun 2011 - Jan 2021 |
| 7. | Zero King ([@l2dy](https://github.com/l2dy)) | Mar 2020 - Mar 2020 |
| 8. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 9. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 10. | Julián Moreno Patiño | Feb 2016 - Feb 2016 |


**All remaining contributors**: Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Stanislaw Pitucha, John Riordan, UnixDev, Sergio Gutierrez, Henning Westerholt ([@henningw](https://github.com/henningw)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Ancuta Onofrei, Aron Rosenberg, Elena-Ramona Modroiu, Juha Heinanen ([@juha-h](https://github.com/juha-h)), Andrea Giordana, Andrei Pelinescu-Onciul, Jan Janak ([@janakj](https://github.com/janakj)), Jiri Kuthan ([@jiriatipteldotorg](https://github.com/jiriatipteldotorg)).


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Elena-Ramona Modroiu, Juha Heinanen ([@juha-h](https://github.com/juha-h)), Andrea Giordana, Jan Janak ([@janakj](https://github.com/janakj)).
