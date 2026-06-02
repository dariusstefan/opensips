---
title: "proto_smpp module"
description: "This module offers interoperability between SIP and SMPP (Short Message Peer-to-Peer) protocols. It provides the means to build a messaging gateway/bridge between the two protocols, being able to convert messages from both directions."
---

## Admin Guide


### Overview {#overview}


This module offers interoperability between SIP and SMPP
			(Short Message Peer-to-Peer) protocols. It provides the
			means to build a messaging gateway/bridge between the two
			protocols, being able to convert messages from both directions.


- SIP to SMPP - messages coming from SIP can be converted to a
			SMPP PDU (Protocol Data Unit) message and sent further to a
			SMSC (Short Message Service Center).
- SMPP to SIP - the module can act as an ESME (External Short
			Messaging Entity), receiving messages from a SMSC and converting
			them to a SIP Message that is sent further to a SIP proxy.


The module is compatible with the
			[SMPP v3.4](http://opensmpp.org/specs/SMPP_v3_4_Issue1_2.pdf) specifications.


### SIP to SMPP bridging


In order to convert a SIP message to a SMPP all you need to do
			is to call the [send smpp message](#func_send_smpp_message) function,
			indicating the SMSc you want to send the message to. The module
			will build the PDU according to the parameters provisioned
			in the database.


### SMPP to SIP bridging


When bridging a message received over the SMPP interface,
			OpenSIPS builds a SIP Message and sends it to the outbound
			proxy identified by the [smpp outbound uri](#param_smpp_outbound_uri)
			module's parameter.


### SMSC binding


In order to be able to deliver messages to SMSc, an ESME needs to
			first bind to the SMSc. This is done at OpenSIPS startup by sending
			a SMPP *bind_transciever* command to connect
			to the SMSc, or an *outbind* command to inform
			an SMSc it can now bind to our gateway.


The description of all SMSc servers is provisioned in the database.
			For each server, one can cofigure the following information:


- *Name* - an unique name given to
			the SMSc that is used to reference this SMSc in the OpenSIPS script.
- *IP* - The IP the SMSc is listening
			on for new bindings/connections.
- *Port* - The TCP port that the SMSc
			is listening on for new bindings/connections.
- *System ID* - Also known as the
			User name that is used to authenticate to the SMSc.
- *Password* - A password used to
			authenticate to the SMSc.
- *System Type* - Usually
			"SMPP", this field is required by some SMPP providers.
- *Source Type of Number (TON)* - Specifies
			the format of the number used to send messages from. Some comon values are:
			
				*0* - Unknown
				*1* - International
				*2* - National
				*3* - Network Specific
				*4* - Subscriber Number
				*5* - Alphanumeric
				*6* - Abbreviated
			
			Default value is *0 - Unknown*.
- *Source Number Plan Indicator (NPI)* - Specifies
			the numbering scheme of the number used to send messages from. Some comon values are:
			
				*0* - Unknown
				*1* - ISDN/telephone numbering plan (E163/E164)
				*3* - Data numbering plan (X.121)
				*4* - Telex numbering plan (F.69)
				*6* - Land Mobile (E.212)
				*8* - National numbering plan
				*9* - Private numbering plan
				*10* - ERMES numbering plan (ETSI DE/PS 3 01-3)
				*13* - Internet (IP)
				*18* - WAP Client Id (to be defined by WAP Forum)
			
			Default value is *0 - Unknown*.
- *Destination Type of Number (TON)* - Specifies
			the format of the number used to send messages to. Can have the same values as
			*Source Type of Number (TON)* and default value is *0 -
			Unknown*.
- *Destination Number Plan Indicator (NPI)* -
			Specifies the numbering scheme of the number used to send messages to. Can have
			the same values as *Source Number Plan Indicator (NPI)*
			and default value is *0 - Unknown*.
- *Session Type* - Specifies what type of session
			should be used to connecto th the SMSc. Possible values are:
			
				*1* - Transciever
				*2* - Transmitter
				*3* - Receiver
				*4* - Outbind
			
			Default value is *1 - Transciever*.


When OpenSIPS starts up, it reads all SMSc specifications from the
			database and triggers a binding with them. *Note:*
			reloading the SMSc database is not yet supported, but it is a work in
			progress.


Each SMPP connection is periodically pinged (currently every 5 seconds)
			using *enquire_link* SMPP commands to keep the
			connection active.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *database* -- Any database module


#### Dependencies of external libraries


- *None*.


### OpenSIPS Exported parameters


All these parameters can be used from the opensips.cfg file,
		to configure the behavior of OpenSIPS-SMPP gateway.


#### db_url (string) {#param_db_url}


The database handler where the SMPP connection will be
			stored. This parameter is mandatory.


*Default value is *unset*.*


```c title="Set db_url parameter"
...
modparam("proto_smpp", "db_url", "dbdriver://username:password@dbhost/dbname")
...
```


#### smpp_port (integer) {#param_smpp_port}


Used to change the default value of the SMPP port used to
			listen for new connections.


*Default value is 2775.*


```c title="Set smpp_port variable"
...
modparam("proto_smpp", "smpp_port", 27775)
...
		
```


#### smpp_max_msg_chunks (integer) {#param_smpp_max_msg_chunks}


The maximum number of chunks in which a SMPP message is expected to
			arrive via TCP. If a received packet is more fragmented than this,
			the connection is dropped (either the connection is very
			overloaded and this leads to high fragmentation - or we are the
			victim of an ongoing attack where the attacker is sending very
			fragmented traffic in order to decrease server performance).


*Default value is 8.*


```c title="Set smpp_max_msg_chunks parameter"
...
modparam("proto_smpp", "smpp_max_msg_chunks", 32)
...
```


#### smpp_send_timeout (integer) {#param_smpp_send_timeout}


Time in milliseconds after a TCP connection will be closed if it is
		not available for blocking writing in this interval (and OpenSIPS wants
		to send something on it).


*Default value is 100 ms.*


```c title="Set smpp_send_timeout parameter"
...
modparam("proto_smpp", "smpp_send_timeout", 200)
...
```


#### outbound_uri (string) {#param_smpp_outbound_uri}


This parameter represents the URI of the outbound proxy used to send
		a message converted from SMPP to SIP.


*Default value is *None*.*


```c title="Set outbound_uri parameter"
...
modparam("proto_smpp", "outbound_uri", "sip:127.0.0.1:5060")
...
```


#### smpp_table (string) {#param_smpp_table}


The name of the database table containing definitions
			of the SMSc servers used to connect to.


*Default value is "smpp".*


```c title="Set smpp_table parameter"
...
modparam("proto_smpp", "smpp_table", "smsc")
...
```


#### name_col (string) {#param_smpp_name_col}


The name of the column that holds the SMSc identifier used by
			the *send_smpp_message()* function.


*Default value is "name".*


```c title="Set name_col parameter"
...
modparam("proto_smpp", "name_col", "smsc_name")
...
```


#### ip_col (string) {#param_smpp_ip_col}


The name of the column that holds the IP of the SMSc.


*Default value is "ip".*


```c title="Set ip_col parameter"
...
modparam("proto_smpp", "ip_col", "smsc_ip")
...
```


#### port_col (string) {#param_smpp_port_col}


The name of the column that holds the SMSc port.


*Default value is "port".*


```c title="Set port_col parameter"
...
modparam("proto_smpp", "port_col", "smsc_port")
...
```


#### system_id_col (string) {#param_smpp_system_id_col}


The name of the column that holds the SMSc System ID.


*Default value is "system_id".*


```c title="Set system_id_col parameter"
...
modparam("proto_smpp", "system_id_col", "smsc_system_id")
...
```


#### password_col (string) {#param_smpp_password_col}


The name of the password column used to authenticate the SMSc.


*Default value is "password".*


```c title="Set password_col parameter"
...
modparam("proto_smpp", "password_col", "smsc_password")
...
```


#### system_type_col (string) {#param_smpp_system_type_col}


The name of the System Type column used to bind the SMSc.


*Default value is "system_type".*


```c title="Set system_type_col parameter"
...
modparam("proto_smpp", "system_type_col", "smsc_system_type")
...
```


#### src_ton_col (string) {#param_smpp_src_ton_col}


The name of the column that holds the Source TON values.


*Default value is "src_ton".*


```c title="Set src_ton_col parameter"
...
modparam("proto_smpp", "src_ton_col", "smsc_src_ton")
...
```


#### src_npi_col (string) {#param_smpp_src_npi_col}


The name of the column that holds the Source NPI values.


*Default value is "src_npi".*


```c title="Set src_npi_col parameter"
...
modparam("proto_smpp", "src_npi_col", "smsc_src_npi")
...
```


#### dst_ton_col (string) {#param_smpp_dst_ton_col}


The name of the column that holds the Destination TON values.


*Default value is "dst_ton".*


```c title="Set dst_ton_col parameter"
...
modparam("proto_smpp", "dst_ton_col", "smsc_dst_ton")
...
```


#### dst_npi_col (string) {#param_smpp_dst_npi_col}


The name of the column that holds the Destination NPI values.


*Default value is "dst_npi".*


```c title="Set dst_npi_col parameter"
...
modparam("proto_smpp", "dst_npi_col", "smsc_dst_npi")
...
```


#### session_type_col (string) {#param_smpp_session_type_col}


The name of the column that holds the Session Type of the SMSc.


*Default value is "session_type".*


```c title="Set session_type_col parameter"
...
modparam("proto_smpp", "session_type_col", "smsc_session_type")
...
```


### Exported Functions {#exported_functions}


#### send_smpp_message(smsc_name, [from],[to],[body],[utf-16],[delivery_receipt]) {#func_send_smpp_message}


This function is used to convert a SIP message received in the
			OpenSIPS script to a SMPP PDU and send it to the
			*smsc_name (string)* received as parameter.
			The SMPP parameters used to construct the PDU are provisione
			in the database, and the command sent is either
			*submit_sm* or *deliver_sm*,
			depending on the type of the SMSc.


The function returns *-2* if the SMSc
			the message should be sent does not exist in the database,
			*-1* if there was an internal error,
			or positive value in case of success.


Meaning of the parameters is as follows:


- *sms_name (string)* - name of the SMS
					to be used for sending the SMPP traffic.
- *from (string, optional)* - the source number.
				If missing, the SIP message from username is used.
- *to (string, optional)* - the destination number.
				If missing, the SIP request URI username is used.
- *body (string, optional)* - the body of the SMS.
				If missing, the SIP message body is used.
- *UTF-16 (int, optional)* - set to
				*1* if the body of the message is in UTF-16.
				format. If missing or *0*, UTF-8 is used.
- *delivery_receipt (int, optional)* - Whether
				the SMSC should confirm delivery for this SMS or not


This function can be used from REQUEST_ROUTE, FAILURE_ROUTE
			or BRANCH_ROUTE.


```c title="send_smpp_message() usage"
...
    if (is_method("MESSAGE"))
			send_smpp_message("MY_SMSC");
...
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Victor Ciurel ([@victor-ciurel](https://github.com/victor-ciurel)) | 80 | 21 | 3760 | 1658 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 72 | 40 | 2047 | 800 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 9 | 7 | 49 | 37 |
| 4. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 8 | 5 | 234 | 37 |
| 5. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 6 | 4 | 12 | 13 |
| 6. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 5 | 3 | 7 | 3 |
| 7. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 4 | 2 | 10 | 13 |
| 8. | Zero King ([@l2dy](https://github.com/l2dy)) | 3 | 1 | 4 | 2 |
| 9. | Nick Altmann ([@nikbyte](https://github.com/nikbyte)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Nov 2023 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Jan 2019 - Jul 2023 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | May 2020 - Apr 2022 |
| 4. | Nick Altmann ([@nikbyte](https://github.com/nikbyte)) | May 2021 - May 2021 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Apr 2019 - Apr 2021 |
| 6. | Zero King ([@l2dy](https://github.com/l2dy)) | Mar 2020 - Mar 2020 |
| 7. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | May 2019 - Sep 2019 |
| 8. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | Apr 2019 - Apr 2019 |
| 9. | Victor Ciurel ([@victor-ciurel](https://github.com/victor-ciurel)) | Sep 2017 - Jan 2019 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)).
## License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
