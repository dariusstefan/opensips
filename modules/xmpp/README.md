---
title: "xmpp Module"
description: "This modules is a gateway between OpenSIPS and a jabber server. It enables the exchange of instant messages between SIP clients and XMPP(jabber) clients."
---

## Admin Guide


### Overview


This modules is a gateway between OpenSIPS and a jabber server. It enables the exchange of instant messages between
		SIP clients and XMPP(jabber) clients.


The gateway has two modes to run:


- **the component-mode** - the gateway requires a standalone XMPP server amd the 'xmpp' module acts as
			a XMPP component
- **the server-mode** - the module acts itself as a XMPP server, no requirement for another XMPP server in the system. NOTE: this is limited implementation of a XMPP server, it does not support SRV or TLS so far. This mode is in beta stage for the moment.


In the component mode, you need a local XMPP server (recommended jabberd2 or ejabberd); the xmpp module will relay all your connections to a tcp connection to the local jabber server.


After you have a running XMPP server, what you need to do is set the following parameters in the OpenSIPS configuration file:


- xmpp_domain and xmpp_host, which are explained in the
				[exported parameters](#exported_parameters) section;
- socket= your ip;
- alias=opensips domain and 
	alias=gateway domain;
- you can also change the jabber server password, which must be the same as the xmpp_password parameter.


A use case, for the component-mode, would look like this:


- OpenSIPS is running on sip-server.opensips.org;
- the jabber server is running on xmpp.opensips.org;
- the component is running on xmpp-sip.opensips.org.


In the server mode, the xmpp module is a minimal jabber server, thus you do not need to install another jabber server, the gateway will connect to the jabber servers, where the users you want to chat with have an account.


If you want to change to server-mode, you have to change the
	"backend" parameter, as shown in the
	[exported parameters](#exported_parameters) section, from component to server.


A use case, for the server-mode, would look like this:


- OpenSIPS is running on sip-server.opensips.org;
- the "XMPP server" is running on xmpp-sip.opensips.org.


### Dependencies


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *requires 'tm' module*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *libexpat1-devel* - used for parsing/building XML.


### Exported Parameters


#### backend (string)


The mode you are using the module; it can be either component or server.


*Default value is "component".*


**Example: Set backend parameter**


```opensips
...
 modparam("xmpp", "backend", "server")
...
```


#### xmpp_domain (string)


The xmpp domain of the component or the server, depending on the mode we are in.


*Default value is "127.0.0.1".*


**Example: Set xmpp_domain parameter**


```opensips
...
 modparam("xmpp", "xmpp_domain", "xmpp.opensips.org")
...
```


#### xmpp_host (string)


The ip address or the name of the local jabber server, if the backend is set to "component"; or the address to bind to in the server mode.


*Default value is "127.0.0.1".*


**Example: Set xmpp_host parameter**


```opensips
...
 modparam("xmpp", "xmpp_host", "xmpp.opensips.org")
...
```


#### sip_domain (string)


This parameter must be set only if the xmpp module is used in component mode and the domain
		that is the host for the jabber server is the same as the domain of the sip server(when using
		the same domain name for the SIP service and for the XMPP service). In this case,
		if we were to add buddies in xmpp accounts with that domain, then all the messages that will
		reach the jabber server will be considered to be for local xmpp users. It is necessary therefore
		to make a translate the sip domain name into another domain when sending messages in xmpp.
		This parameter is exactly the name that should be used as the SIP domain name in XMPP.
		Usage example: If the sip and xmpp domain is opensips.org and this parameter is set to sip.opensips.org,
		than in all the requests sent in xmpp the sip users will have the domain translated to sip.opensips.org.
		Also, in XMPP account the SIP buddies must have this domain: sip.opensips.org, and it will be
		translated to the real one opensips.org when traversing the gateway.


*Default value is NULL.*


**Example: Set xmpp_host parameter**


```opensips
...
 modparam("xmpp", "sip_domain", "sip.opensips.org")
...
```


#### xmpp_port (integer)


In the component mode, this is the port of the jabber router we connect to. In the server mode, it is the transport address to bind to.


*Default value is "5347", if backend is set to "component" and "5269", if backend is set to "server".*


**Example: Set xmpp_port parameter**


```opensips
...
 modparam("xmpp", "xmpp_port", 5269)
...
```


#### xmpp_password (string)


The password of the local jabber server.


*Default value is "secret"; if changed here, it must also be changed in the c2s.xml, added by the jabber server. This is how the default configuration for the jabberd2 looks like:*


```
			<router>
	............... 
	;
    <pass>secret</pass>;           ;	
			
```


**Example: Set xmpp_password parameter**


```opensips
...
 modparam("xmpp", "xmpp_password", "secret")
...
```


#### outbound_proxy (string)


The SIP address used as next hop when sending the message. Very
		useful when using OpenSIPS with a domain name not in DNS, or
		when using a separate OpenSIPS instance for xmpp processing. If
		not set, the message will be sent to the address in destination
		URI.


*Default value is NULL.*


**Example: Set outbound_proxy parameter**


```opensips
...
 modparam("xmpp", "outbound_proxy", "sip:opensips.org;transport=tcp")
...
```


### Exported Functions


#### xmpp_send_message()


Converts SIP messages to XMPP(jabber) messages, in order to be relayed to a XMPP(jabber) client.


**Example: xmpp_send_message() usage**


```
...
xmpp_send_message();
...
```


### Configuration


Next is presented a sample configuration file one can use to implement a
		standalone SIP-to-XMPP gateway. You can run an instance of OpenSIPS on a
		separate machine or on different port with the following config, and have
		the main SIP server configured to forward all SIP requests for XMPP world
		to it.


```opensips
....
#
# simple quick-start config script for XMPP GW
#
# make sure in your main SIP server that you send
# only the adequate SIP MESSAGES to XMPP GW
#
#
# ----------- global configuration parameters ------------------------

log_level=3        # debug level (cmd line: -dddddddddd)
stderror_enabled=no
syslog_enabled=yes

/* Uncomment these line to enter debugging mode */
#debug_mode=yes

check_via=no	# (cmd. line: -v)
dns=no          # (cmd. line: -r)
rev_dns=no      # (cmd. line: -R)
udp_workers=4

socket=udp:10.10.10.10:5076
alias=sip-xmpp.opensips.org

# ------------------ module loading ----------------------------------

mpath="/usr/local/opensips/lib/opensips/modules/"
loadmodule "sl.so"
loadmodule "tm.so"
loadmodule "rr.so"
loadmodule "maxfwd.so"
loadmodule "textops.so"
loadmodule "mi_fifo.so"


# XMPP
loadmodule "xmpp.so"

modparam("xmpp", "xmpp_domain", "xmpp-sip.opensips.org")
modparam("xmpp", "xmpp_host", "xmpp.opensips.org")

#modparam("xmpp", "backend", "server")
modparam("xmpp", "backend", "component")

# ----------------- setting module-specific parameters ---------------

# -- mi_fifo params --

modparam("mi_fifo", "fifo_name", "/tmp/opensips_fifo_xmpp")

# -------------------------  request routing logic -------------------

# main routing logic

route{

	# initial sanity checks -- messages with
	# max_forwards==0, or excessively long requests
	if (!mf_process_maxfwd_header("10")) {
		sl_send_reply(483,"Too Many Hops");
		exit;
	};

	### absorb retransmissions ###
	if (!t_newtran()) {
		sl_reply_error();
		return;
	}
	if (is_method("MESSAGE")) {
		log("*** xmpp-handled MESSAGE message.\n");
		if (xmpp_send_message()) {
			t_reply(200, "Accepted");
		} else {
			t_reply(404, "Not found");
		}
		return;
	}
		
	log("*** xmpp: unhandled message type\n");
	t_reply(503, "Service unavailable");
	return;
}


....
```


## Contributors


### By Commit Statistics


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 69 | 16 | 5988 | 76 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 31 | 24 | 121 | 247 |
| 3. | Anca Vamanu | 20 | 9 | 438 | 398 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 14 | 11 | 56 | 98 |
| 5. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 10 | 8 | 57 | 45 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 6 | 4 | 9 | 5 |
| 7. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 5 | 3 | 13 | 13 |
| 8. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 6 | 7 |
| 9. | Sergio Gutierrez | 3 | 1 | 5 | 5 |
| 10. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 3 | 1 | 3 | 2 |


**All remaining contributors**: Konstantin Bokarius, John Riordan, Juha Heinanen ([@juha-h](https://github.com/juha-h)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Zero King ([@l2dy](https://github.com/l2dy)), Edson Gellert Schubert.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - May 2026 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Oct 2006 - Apr 2026 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - May 2024 |
| 4. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - May 2023 |
| 5. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 6. | Zero King ([@l2dy](https://github.com/l2dy)) | Mar 2020 - Mar 2020 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 8. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Feb 2012 - Feb 2012 |
| 9. | Anca Vamanu | Oct 2007 - Sep 2010 |
| 10. | John Riordan | May 2009 - May 2009 |


**All remaining contributors**: Sergio Gutierrez, Henning Westerholt ([@henningw](https://github.com/henningw)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Juha Heinanen ([@juha-h](https://github.com/juha-h)).


*(1) including any documentation-related commits, excluding merge commits*


## Documentation


### Contributors


**Last edited by:** Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Anca Vamanu, Henning Westerholt ([@henningw](https://github.com/henningw)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Juha Heinanen ([@juha-h](https://github.com/juha-h)).


*Documentation Copyrights:*


Copyright © 2006-2008 Voice Sistem SRL
