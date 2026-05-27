---
title: "event_kafka Module"
description: "This module is an implementation of an [Apache Kafka](https://kafka.apache.org/) producer. It serves as a transport backend for the Event Interface and also provides a stand-alone connector to be used from the OpenSIPS script in order to publish messages to Kafka brokers."
---

## Admin Guide


### Overview {#overview}


This module is an implementation of an
		[Apache Kafka](https://kafka.apache.org/) producer.
		It serves as a transport backend for the Event Interface and also provides a
		stand-alone connector to be used from the OpenSIPS script in order to
		publish messages to Kafka brokers.


### Kafka socket syntax {#kafka_socket_syntax}


*'kafka:' brokers '/' topic ['?' properties]*


Meaning of the socket fields:


- *brokers* - comma-separated list of the addresses (as
			host:port) of the Kafka brokers to connect to. These are the "bootstrap"
			servers used by the client to discover the Kafka cluster. This
			corresponds to the *bootstrap.servers* /
			*metadata.broker.list* configuration property.
- *topic* - Kafka topic used to publish messages to.
- *properties* - configuration properties to be
			transparently passed to the Kafka client library. The syntax is:
			*'g.'|'t.' property '=' value ['&' 'g.'|'t.' property '=' value] ...*
			The *g.* or *t.* prefix before
				each property name specifies whether it's a global or topic level
				property, as classified by the Kafka library. Documentation for the
				supported properties can be found
				[here](https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md).
			Note that some library properties have the *topic.*
				prefix as part of their name, but still fall under the global category.
			*key=callid* is an extra property that is not
				passed to the Kafka library and is interpreted by OpenSIPS itself.
				When enabling this property the record published to Kafka will also
				include the Call-ID of the current SIP message as key.


### Kafka events syntax


The event payload is formated as a JSON-RPC notification, with the event
		name as the *method* field and the event parameters as
		the *params* field.


The record published to Kafka will also include the Call-ID of the current
		SIP message as key, if the *key=callid* property is
		provided in the event socket.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *none*.


#### External Libraries or Applications


The following libraries or applications must be installed before 
		running OpenSIPS with this module loaded:


- *librdkafka-dev*


*librdkafka-dev* can be installed from the Confluent
			[APT](https://docs.confluent.io/current/installation/installing_cp/deb-ubuntu.html#get-the-software) or
			[YUM](https://docs.confluent.io/current/installation/installing_cp/rhel-centos.html#get-the-software)
			repositories.


### Exported Parameters {#exported_parameters}


#### broker_id (string) {#param_broker_id}


This parameter specifies the configuration for a Kafka broker
			(or cluster) that can be used to publish messages directly
			from the script, using the [kafka publish](#func_kafka_publish) function.


The format of the parameter is: *[ID]kafka_socket*,
			where *ID* is an identifier for this broker instance and
			*kafka_socket* is a specification similar to the
			[kafka socket syntax](#kafka_socket_syntax).


The *key=callid* property does not have an effect for
			brokers configured through this parameter.


This parameter can be set multiple times.


```c title="Set broker_id parameter"
...
modparam("event_kafka", "broker_id", "[k1]127.0.0.1:9092/topic1?g.linger.ms=100&t.acks=all")
...
```


### Exported Functions {#exported_functions}


#### kafka_publish(broker_id, message, [key], 
				[report_route]) {#func_kafka_publish}


Publishes a message to a Kafka broker (or cluster). As the actual
			send operation is done in an asynchronous manner, a report route
			may be provided in order to check the message delivery status.


Returns *1* if the message was succesfully queued
			for sending or *-1* otherwise.


This function can be used from any route.


The function has the following parameters:


- *broker_id* (string) - the ID of the Kafka broker
					(or cluster).
					Must be one of the IDs defined through the
					[broker id](#param_broker_id) modparam.
- *message* (string) - the payload of the Kafka
					message to publish.
- *key* (string, optional) - the key of the Kafka
					record to publish.
- *report_route* (string, static, optional) -
					name of a script route to be executed when the message delivery
					status is available. Information about the message publishing will
					be available in this route through the following AVP variables:
					
					*$avp(kafka_id)* - broker ID
					*$avp(kafka_status)* - delivery status,
						0 if succesfull, -1 othewise
					*$avp(kafka_key)* - message key
					*$avp(kafka_msg)* - message payload


```c title="kafka_publish() function usage"
	...
	$var(msg) = "my msg content";
	kafka_publish("k1", $var(kmsg), $ci, "kafka_report");
	...
	route[kafka_report] {
		xlog("Delivery status: $avp(kafka_status) for broker: $avp(kafka_id)\n");
	}
	...
	
```


### Examples


```c title="Kafka socket"
	kafka:127.0.0.1:9092/topic1?t.message.timeout.ms=1000&key=callid
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 26 | 8 | 1933 | 31 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 4 | 2 | 25 | 37 |
| 3. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 5 | 5 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 4 | 2 | 2 | 1 |
| 5. | Alexandra Titoc | 3 | 1 | 5 | 1 |
| 6. | Ken Rice | 3 | 1 | 1 | 1 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Ken Rice | Sep 2025 - Sep 2025 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Jul 2024 - Aug 2025 |
| 3. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jul 2025 - Jul 2025 |
| 4. | Alexandra Titoc | Sep 2024 - Sep 2024 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | May 2023 - Jun 2024 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | Aug 2020 - Jun 2023 |
| 7. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)).


*Documentation Copyrights:*


Copyright © 2020 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
