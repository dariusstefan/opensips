---
title: "Presence Module"
description: "The modules handles PUBLISH and SUBSCRIBE messages and generates NOTIFY messages in a general, event independent way. It allows registering events from other OpenSIPS modules. Events that can currently be added are: *presence*, *presence.winfo*, *dialog;sla* from presence_xml module *mes..."
---

## Admin Guide


### Overview {#overview}


The modules handles PUBLISH and SUBSCRIBE messages and generates
	NOTIFY messages in a general, event independent way. It allows registering 
	events from other OpenSIPS modules. Events that can currently be added are:


- *presence*, *presence.winfo*,
		*dialog;sla* from presence_xml module
- *message-summary* from presence_mwi module
- *call-info*, *line-seize* from
		presence_callinfo module
- *dialog* from presence_dialoginfo module
- *xcap-diff* from presence_xcapdiff module
- *as-feature-event* from presence_dfks module


The module uses database storage. 
	It has later been improved with memory caching operations to improve
	performance. The Subscribe dialog information are stored in memory and 
	are periodically updated in database, while for Publish only the presence
	or absence of stored info for a certain resource is maintained in memory
	to avoid unnecessary, costly db operations. 
	It is possible to configure a fallback to database mode(by setting module
	parameter "fallback2db"). In this mode, in case a searched record is not 
	found in cache, the search is continued	in database. This is useful for
	an architecture in which processing and memory load might be divided on 
	more machines using the same database.


The module can also work only with the functionality of a library,
	with no message processing and generation, but used only for the exported
	functions.
	This mode of operation is enabled if the db_url parameter is not set to any value.


The server follows the specifications in: RFC3265, RFC3856, RFC3857, 
	RFC3858.


### Presence clustering {#presence_clustering}


To read and understand the presence clustering, its abilities and how to
	implement scenarios like High-Availability, Load Balancing or Federations,
	please refer to this article [https://blog.opensips.org/2018/03/27/clustering-presence-services-with-opensips-2-4/](https://blog.opensips.org/2018/03/27/clustering-presence-services-with-opensips-2-4/).


As data synchronization at startup is performed when using the
	*full-sharing* [cluster federation mode](#param_cluster_federation_mode),
	you should define at least one "seed" node in the cluster in this case.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *a database module*.
- *signaling*.
- *clusterer*, if the cluster_id 
				module parameter is set and clustering support activated.


#### External Libraries or Applications


- *libxml-dev*.


### Exported Parameters {#exported_parameters}


#### db_url(str) {#param_db_url}


The database url.


If set, the module is a fully operational
		presence server. Otherwise, it is used as a 'library', for 
		its exported functions.


*Default value is "NULL".*


```c title="Set db_url parameter"
...
modparam("presence", "db_url", 
	"mysql://opensips:opensipsrw@192.168.2.132/opensips")
...
```


#### fallback2db (int) {#param_fallback2db}


Setting this parameter enables a fallback to db mode of operation.
		In this mode, in case a searched record is not found in cache, 
		the search is continued	in database. Useful for an architecture in
		which processing and memory load might be divided on more machines
		using the same database.


```c title="Set fallback2db parameter"
...
modparam("presence", "fallback2db", 1)
...
```


#### cluster_id (int) {#param_cluster_id}


The ID of the cluster this presence server belongs to. This parameter
		is to be used only if clustering mode is needed. In order to
		understand th concept of a cluster ID, please see the 
		*clusterer* module.


This OpenSIPS cluster exposes the **"presence"**
capability in order to mark nodes as eligible for becoming data donors during an
arbitrary sync request. Consequently, the cluster must have *at least
one node* marked with the **"seed"** value
as the *clusterer.flags* column/property in order to be fully functional.
Consult the [clusterer - Capabilities](../clusterer#capabilities)
chapter for more details.


For more on presence clustering see the 
		[presence clustering](#presence_clustering) chapter.


*Default value is "None".*


```c title="Set cluster_id parameter"
...
modparam("presence", "cluster_id", 2)
...
```


#### cluster_federation_mode (str) {#param_cluster_federation_mode}


When enabling the federation mode, nodes inside the presence
		cluster will start broadcasting the data to other nodes via the
		clustering support.


*Possible values:*


- *disabled* - federation mode is disabled
- *on-demand-sharing* - the minimum needed information is
				kept on each node. Replicated information for non local
				subscribers is discarded and queries are broadcasted
				in the cluster for new subscribers.
- *full-sharing* - published state is kept on all presence
				nodes even when there aren't any local subscribers.


If you don't want to use a shared database (via
		[fallback2db](#param_fallback2db)), but still want a
		complete data set everywhere, you may choose mode *full-sharing*.
		This mode allows you to switch PUBLISH endpoints,
		even for already published Event States, thus allowing
		you to add and remove presence servers without losing
		state.


For more on presence clustering see the 
		[presence clustering](#presence_clustering) chapter.


*Default value is "disabled".*


```c title="Set cluster_federation_mode parameter"
...
modparam("presence", "cluster_federation_mode", "full-sharing")
...
```


#### cluster_pres_events (str) {#param_cluster_pres_events}


Comma Separated Value (CSV) list with the events to considered by the
		federated cluster - only presentities advertising one of these events
		will be broadcasted via the cluster.


For more on presence clustering see the 
		[presence clustering](#presence_clustering) chapter.


*Default value is "empty" (meaning all).*


```c title="Set cluster_pres_events parameter"
...
modparam("presence", "cluster_pres_events" ,"presence, dialog;sla, message-summary")
...
```


#### cluster_be_active_shtag (str) {#param_cluster_be_active_shtag}


The name of a cluster sharing tag to be used to indicate when this
		node (as part of the cluster) should be active or not. If the sharing
		tag is off (or as backup), the node will become inactive from 
		clustering perspective, meaning not sending and not accepting any
		presence related cluster traffic.


This ability of a node to become inactive may be used when creating a
		federated cluster where 2 nodes are acting as a local active-backup 
		setup (for local High Availability purposes).


This parameter has meaning only in clustering mode. If not defined, the
		node will be active all the time.


For more on presence clustering see the 
		[presence clustering](#presence_clustering) chapter.


*Default value is "empty" (not tag define).*


```c title="Set cluster_be_active_shtag parameter"
...
modparam("presence", "cluster_be_active_shtag" ,"local_ha")
...
```


#### expires_offset (int) {#param_expires_offset}


The extra time to store a subscription/publication.


*Default value is "0".*


```c title="Set expires_offset parameter"
...
modparam("presence", "expires_offset", 10)
...
```


#### max_expires_subscribe (int) {#param_max_expires_subscribe}


The the maximum admissible expires value for SUBSCRIBE
		messages.


*Default value is "3600".*


```c title="Set max_expires_subscribe parameter"
...
modparam("presence", "max_expires_subscribe", 3600)
...
```


#### max_expires_publish (int) {#param_max_expires_publish}


The the maximum admissible expires value for PUBLISH
		messages.


*Default value is "3600".*


```c title="Set max_expires_publish parameter"
...
modparam("presence", "max_expires_publish", 3600)
...
```


#### contact_user (str) {#param_contact_user}


This is the username that will be used in the Contact header for the 200 OK
		replies to SUBSCRIBE and in the following in-dialog NOTIFY requests.
		The IP address, port and transport for the Contact will be automatically
		determined based on the interface where the SUBSCRIBE was received.


If set to an empty string, no username will be added to the contact and
		the contact will be built just out of the IP, port and transport.


*Default value is "presence".*


```c title="Set contact_user parameter"
...
modparam("presence", "contact_user", "presence")
...
		
```


#### enable_sphere_check (int) {#param_enable_sphere_check}


This parameter is a flag that should be set if permission rules 
		include sphere checking. The sphere information is expected to be 
		present in the RPID body published by the presentity. The flag is 
		introduced as this check requires extra processing that should be 
		avoided if this feature is not supported by the clients.


*Default value is "0 ".*


```c title="Set enable_sphere_check parameter"
...
modparam("presence", "enable_sphere_check", 1)
...
	
```


#### waiting_subs_daysno (int) {#param_waiting_subs_daysno}


The number of days to keep the record of a subscription in server
			database if the subscription is in pending or waiting state 
			(no authorization policy was defined for it or the target user 
			did not register sice the subscription and was not informed about
			it).


*Default value is "3" days. Maximum accepted
			value is 30 days.*


```c title="Set waiting_subs_daysno parameter"
...
modparam("presence", "waiting_subs_daysno", 2)
...
	
```


#### mix_dialog_presence (int) {#param_mix_dialog_presence}


This module parameter enables a very nice feature in the presence 
		server - generating presence information from dialogs state. If this 
		parameter is set, the presence server will tell you if a buddy is in 
		a call even if his phone did not send a presence Publish with this 
		information. You will need to load the dialoginfo modules, 
		presence_dialoginfo, pua_dialoginfo, dialog and pua.


*Default value is "0".*


```c title="Set mix_dialog_presence parameter"
...
modparam("presence", "mix_dialog_presence", 1)
...
	
```


#### bla_presentity_spec (str) {#param_bla_presentity_spec}


By default the presentity uri for BLA subscribes (event=dialog;sla)
			is computed from contact username + from domain. In some cases 
			though, this way of computing the presentity might not be right 
			(for example if you have a SBC in front that masquerades the 
			contact). So we added this parameter that allows defining a custom 
			uri to be used as presentity uri for BLA subscribes. You should 
			set this parameter to the name of a pseudovariable and then set 
			this pseudovariable to the desired URI before calling the
			[handle subscribe](#func_handle_subscribe) function.


*Default value is "NULL".*


```c title="Set bla_presentity_spec parameter"
...
modparam("presence", "bla_presentity_spec", "$var(bla_pres)")
...
	
```


#### bla_fix_remote_target (int) {#param_bla_fix_remote_target}


Polycom has a bug in the bla implementation. It inserts the 
			remote IP contact in the Notify body and when a phone picks up a 
			call put on hold by another phone in the same BLA group, it sends 
			an Invite directly to the remote IP. OpenSIPS BLA server tries to 
			prevent this by replacing the IP contact with the
			domain, when this is possible.


In some cases(configurations) however this is not desirable, so 
			this parameter was introduced to disable this behaviour when 
			needed.


*Default value is "1".*


```c title="Set bla_fix_remote_target parameter"
...
modparam("presence", "bla_fix_remote_target", 0)
...
	
```


#### notify_offline_body (int) {#param_notify_offline_body}


If this parameter is set, when no published info is found for
			a user, the presence server will generate a dummy body with status
			'closed' and use it when sending Notify, instead of notifying with
			no body.


*Default value is "0".*


```c title="Set notify_offline_body parameter"
...
modparam("presence", "notify_offline_body", 1)
...
	
```


#### end_sub_on_timeout (int) {#param_end_sub_on_timeout}


If a presence subscription should be automatically terminated 
			(destroyed) when receiving a SIP timeout (408) for a sent
			NOTIFY requests.


*Default value is "1" (enabled).*


```c title="Set end_sub_on_timeout parameter"
...
modparam("presence", "end_sub_on_timeout", 0)
...
	
```


#### clean_period (int) {#param_clean_period}


The period at which to clean the expired subscription dialogs.


*Default value is "100". A zero or negative 
		value disables this activity.*


```c title="Set clean_period parameter"
...
modparam("presence", "clean_period", 100)
...
```


#### db_update_period (int) {#param_db_update_period}


The period at which to synchronize cached subscriber info with the
		database.


*Default value is "100". A zero or negative 
		value disables synchronization.*


```c title="Set db_update_period parameter"
...
modparam("presence", "db_update_period", 100)
...
```


#### presentity_table(str) {#param_presentity_table}


The name of the db table where Publish information are stored.


*Default value is "presentity".*


```c title="Set presentity_table parameter"
...
modparam("presence", "presentity_table", "presentity")
...
```


#### active_watchers_table(str) {#param_active_watchers_table}


The name of the db table where active subscription information are 
		stored.


*Default value is "active_watchers".*


```c title="Set active_watchers_table parameter"
...
modparam("presence", "active_watchers_table", "active_watchers")
...
```


#### watchers_table(str) {#param_watchers_table}


The name of the db table where subscription states are stored.


*Default value is "watchers".*


```c title="Set watchers_table parameter"
...
modparam("presence", "watchers_table", "watchers")
...
```


#### subs_htable_size (int) {#param_subs_htable_size}


The size of the hash table to store subscription dialogs.
	This parameter will be used as the power of 2 when computing table size.


*Default value is "9 (512)".*


```c title="Set subs_htable_size parameter"
...
modparam("presence", "subs_htable_size", 11)
...
	
```


#### pres_htable_size (int) {#param_pres_htable_size}


The size of the hash table to store publish records.
	This parameter will be used as the power of 2 when computing table size.


*Default value is "9 (512)".*


```c title="Set pres_htable_size parameter"
...
modparam("presence", "pres_htable_size", 11)
...
	
```


### Exported Functions {#exported_functions}


#### handle_publish([sender_uri]) {#func_handle_publish}


The function handles PUBLISH requests. It stores and updates 
		published information in database and calls functions to send 
		NOTIFY messages when changes in the published information occur.


It may takes one optional string argument, the 'sender_uri' SIP URI.
		The parameter was added 
		for enabling BLA implementation. If present, Notification of
		a change in published state is not sent to the respective uri
		even though a subscription exists.
		It should be taken from the Sender header. It was left at the
		decision of the administrator whether or not to transmit the 
		content of this header as parameter for handle_publish, to 
		prevent security problems.


This function can be used from REQUEST_ROUTE.


*Return code:*


- *1 - if success*.
- *-1 - if error*.


The module sends an appropriate stateless reply
			in all cases.


```c title="handle_publish usage"
...
	if(is_method("PUBLISH"))
	{
		if($hdr(Sender)!= NULL)
			handle_publish($hdr(Sender));
		else
			handle_publish();
	} 
...
```


#### handle_subscribe([force_active] [,sharing_tag]) {#func_handle_subscribe}


This function is to be used for handling SUBSCRIBE requests. It stores
		or updates the watcher/subscriber information in database. 
		Additionally, in response to initial SUBSCRIBE requests (creating a 
		new subscription session), the function also sends back the NOTIFY 
		(with the presence information) to the wathcer/subscriber.


The function may take the following parameters:


- *force_active* (int, optional) - optional parameter that 
				controls what is the default policy (of the presentity) on 
				accepting new subscriptions (accept or reject) - of course, 
				this parameter makes sense only when using a presence 
				configuration with privacy rules enabled (force_active 
				parameter in presence_xml module is not set).
There are scenarios where the presentity (the party you 
				subscribe to) can not upload an XCAP document with its
				privacy rules (to control which watchers are allowed to 
				subscribe to it). In such cases, from script level, you can
				force the presence server to consider the current subscription
				allowed (with Subscription-Status:active) by calling the 
				handle_subscribe() function with the integer parameter "1".
- *sharing_tag* (string, optional) - optional parameter telling
				the owner tag (for the subscription) in clusetering scenarios 
				where the subscription data is shared between multiple 
				servers - see the [presence clustering](#presence_clustering)
				chapter for more details.


```c
   Ex: 
	if($ru =~ "kphone@opensips.org")
		handle_subscribe(1);
		
```


This function can be used from REQUEST_ROUTE.


*Return code:*


- *1 - if success*.
- *-1 - if error*.


The module sends an appropriate stateless reply
			in all cases.


```c title="handle_subscribe usage"
...
if($rm=="SUBSCRIBE")
    handle_subscribe();
...
```


### Exported MI Functions {#exported_mi_functions}


#### refresh_watchers {#mi_refresh_watchers}


Triggers sending Notify messages to watchers if a change in watchers
		authorization or in published state occurred.


Name: *refresh_watchers*


Parameters:


- presentity_uri : the uri of the user who made the change
				and whose watchers should be informed
- event : the event package
- refresh type : it distinguishes between the two different types of events
									that can trigger a refresh: 
									
									
									a change in watchers authentication: refresh type= 0 ;
									
									
									a statical update in published state (either through direct 
									update in db table or by modifying the pidf manipulation document,
									if pidf_manipulation parameter is set): refresh type!= 0.


MI FIFO Command Format:


```c
opensips-cli -x mi refresh_watchers sip:11@192.168.2.132 presence 1
	
```


#### cleanup {#mi_cleanup}


Manually triggers the cleanup functions for watchers and presentity tables. Useful if you
		have set `clean_period` to zero or less.


Name: *cleanup*


Parameters: *none*


MI FIFO Command Format:


```c
opensips-cli -x mi cleanup
	  
```


#### pres_phtable_list {#mi_pres_phtable_list}


Lists all the presentity records.


Name: *pres_phtable_list*


Parameters: *none*


MI FIFO Command Format:


```c
opensips-cli -x mi pres_phtable_list
	  
```


#### subs_phtable_list {#mi_subs_phtable_list}


Lists all the subscription records, or the subscriptions for which the "To" and "From" URIs match the given parameters.


Name: *subs_phtable_list*


Parameters


- *from*(optional) - wildcard for "From" URI
- *to*(optional) - wildcard for "To" URI


MI FIFO Command Format:


```c
opensips-cli -x mi subs_phtable_list sip:222@domain2.com sip:user_1@example.com
	  
```


#### pres_expose {#mi_pres_expose}


Exposes in the script, by rasing an
		  *E_PRESENCE_EXPOSED* event, all the
		  presentities of a specific event that match a specified
		  filter.


Name: *pres_expose*


Parameters:


- *event* - the desired presence
			event.
- *filter*(optional) - a regular
			expression (REGEXP) used for filtering the presentities
			for that event. Only the presentities that match will
			be exposed. If not specified, all presentities for that
			event are exposed.


MI FIFO Command Format:


```c
opensips-cli -x mi pres_expose presence ^sip:10\.0\.5\.[0-9]*
	  
```


### Exported Events {#exported_events}


#### E_PRESENCE_PUBLISH {#event_E_PRESENCE_PUBLISH}


This event is raised when the presence module receives
			a PUBLISH message.


Parameters:


- *user* - the AOR of the user
- *domain* - the domain
- *event* - the type of the
					event published
- *expires* - the expire value
					of the publish
- *etag* - the entity tag
- *old_etag* - the entity tag to be refreshed
- *body* - the body of the
					PUBLISH request


#### E_PRESENCE_EXPOSED {#event_E_PRESENCE_EXPOSED}


This event is raised for each presentity exposeed
			by the *pres_expose*.


Parameters:


Same parameters as the
			*E_PRESENCE_PUBLISH* event.


### Installation


The module requires 3 table in OpenSIPS database: presentity,
	active_watchers and watchers tables. The SQL 
	syntax to create them can be found in presence-create.sql 
	script in the database directories in the opensips/scripts folder.
	You can also find the complete database documentation on the
	project webpage, [https://opensips.org/docs/db/db-schema-devel.html](https://opensips.org/docs/db/db-schema-devel.html).


## Developer Guide


The module provides the following functions that can be used
		in other OpenSIPS modules.


### bind_presence(presence_api_t* api)


This function binds the presence modules and fills the structure 
				with the exported functions that represent functions adding events
				in presence module and functions specific for Subscribe processing.


```c title="presence_api_t structure"
...
typedef struct presence_api {
	add_event_t add_event;
	contains_event_t contains_event;
	search_event_t search_event;
	get_event_list_t get_event_list;
	
	update_watchers_t update_watchers_status;
	
	/* subs hash table handling functions */
	new_shtable_t new_shtable;
	destroy_shtable_t destroy_shtable;
	insert_shtable_t insert_shtable;
	search_shtable_t search_shtable;
	delete_shtable_t delete_shtable;
	update_shtable_t update_shtable;
	/* function to duplicate a subs structure*/
	mem_copy_subs_t  mem_copy_subs;
	/* function used for update in database*/
	update_db_subs_t update_db_subs;
	/* function to extract dialog information from a
	SUBSCRIBE message */
	extract_sdialog_info_t extract_sdialog_info;
	/* function to request sphere defition for a presentity */
	pres_get_sphere_t get_sphere;
	pres_contains_presence_t contains_presence;
}presence_api_t;
...
```


### add_event


Field type:


```c
...
typedef int (*add_event_t)(pres_ev_t* event);
...
```


This function receives as a parameter a structure with event specific
			information and adds it to presence event list.


The structure received as a parameter:


```c
...
typedef struct pres_ev
{
	str name;
	event_t* evp;
	str content_type;
	int default_expires;
	int type;
	int etag_not_new;
	/*
	 *  0 - the standard mechanism (allocating new etag
			for each Publish)
	 *  1 - allocating an etag only
			for an initial Publish 
	*/
	int req_auth;
	get_rules_doc_t* get_rules_doc;
	apply_auth_t*  apply_auth_nbody;
	is_allowed_t*  get_auth_status;
	
	/* an agg_body_t function should be registered
	 * if the event permits having multiple published
	 * states and requires an aggregation of the information
	 * otherwise, this field should be NULL and the last
	 * published state is taken when constructing Notify msg
	 */
	agg_nbody_t* agg_nbody;
	publ_handling_t  * evs_publ_handl;
	subs_handling_t  * evs_subs_handl;
	free_body_t* free_body;
	
	/* sometimes it is necessary that a module make changes for a body for each 
	 * active watcher (e.g. setting the "version" parameter in an XML document.
	 * If a module registers the aux_body_processing callback, it gets called for
	 * each watcher. It either gets the body received by the PUBLISH, or the body
	 * generated by the agg_nbody function.
	 * The module can deceide if it makes a copy of the original body, which is then
	 * manipulated, or if it works directly in the original body. If the module makes a
	 * copy of the original body, it also has to register the aux_free_body() to 
	 * free this "per watcher" body.
	 */
	aux_body_processing_t* aux_body_processing;
	free_body_t* aux_free_body;

	struct pres_ev* wipeer;			
	struct pres_ev* next;
	
}pres_ev_t;
...
```


### get_rules_doc


Filed type:


```c
...
typedef int (get_rules_doc_t)(str* user, str* domain, str** rules_doc);
...
			
```


This function returns the authorization rules document that will be
		used in obtaining the status of the subscription and processing the
		notified body. A reference to the document should be put in the 
		auth_rules_doc of the subs_t structure given as a parameter to the
		functions described bellow.


### get_auth_status


This filed is a function to be called for a subscription request
			to return the state for that subscription according to
			authorization rules. In the auth_rules_doc field of the subs_t
			structure received as a parameter should contain the rules 
			document of the presentity in case, if it exists.


It is called only if the req_auth field is not 0.


Filed type:


```c
...
typedef int (is_allowed_t)(struct subscription* subs);
...
			
```


### apply_auth_nbody


This parameter should be a function to be called for an event 
			that requires authorization, when constructing final body. 
			The authorization document is taken from the auth_rules_doc
			field of the subs_t structure given as a parameter.
			It is called only if the req_auth field is not 0.


Filed type:


```c
...
typedef int (apply_auth_t)(str* , struct subscription*, str** );
...
			
```


### agg_nbody


If present, this field marks that the events requires aggregation
			of states. This function receives a body array and should return
			the final body.	If not present, it is considered that the event
			does not require aggregation and the most recent published
			information is used when constructing Notifies.


Filed type:


```c
...
typedef str* (agg_nbody_t)(str* pres_user, str* pres_domain, 
str** body_array, int n, int off_index);
..
			
```


### free_body


This field must be field in if subsequent processing is performed
			on the info from database before being inserted in Notify
			message body(if agg_nbody or apply_auth_nbody fields are
			filled in). It should match the allocation function used when
			processing the body.


Filed type:


```c
...
typedef void(free_body_t)(char* body);
..
			
```


### aux_body_processing


This field must be set if the module needs to manipulate the NOTIFY body 
			for each watcher. E.g. if the XML body includes a 'version' parameter which 
			will be increased for each NOTIFY, on a "per watcher" basis.
			The module can either allocate a new buffer for the new body an return it (aux_free_body
			function must be set too) or it manipualtes the original body directly and returns NULL.


Filed type:


```c
...
typedef str* (aux_body_processing_t)(struct subscription *subs, str* body);
..
			
```


### aux_free_body


This field must be set if the module registers the aux_body_processing function
			and allocates memory for the new modified body. Then, this function will be used
			to free the pointer returned by the aux_body_processing function.
			If the module does use the aux_body_processing, but does not allocate new memory, but
			manipulates directly the original body buffer, then the aux_body_processing
			must return NULL and this field should not be set.


Filed type:


```c
...
typedef void(free_body_t)(char* body);
..
			
```


### evs_publ_handl


This function is called when handling Publish requests. Most contain 
		body correctness check.


```c
...
typedef int (publ_handling_t)(struct sip_msg*);
..
			
```


### evs_subs_handl


It is not compulsory. Should contain event specific handling for
		Subscription requests.


Filed type:


```c
...
typedef int (subs_handling_t)(struct sip_msg*);
..
```


### contains_event


Field type:


```c
..
typedef pres_ev_t* (*contains_event_t)(str* name,
event_t* parsed_event);
...
```


The function parses the event name received as a parameter and searches
	the result in the list. It returns the found event or NULL, if not found. 
	If the second argument is an allocated event_t* structure it fills it
	with the result of the parsing.


### get_event_list


Field type:


```c
...
typedef int (*get_event_list_t) (str** ev_list);
...
```


This function returns a string representation of the events registered
	in presence module.( used for Allowed-Events header).


### update_watchers_status


Field type:


```c
...
typedef int (*update_watchers_t)(str pres_uri, pres_ev_t* ev,
str* rules_doc);
...
```


This function is an external command that can be used to announce a change
	in authorization rules for a presentity. It updates the stored status and
	sends a Notify to the watchers whose status has changes. (used by
	presence_xml module when notified through an MI command of a change in
	an xcap document).


### get_sphere


Field type:


```c
...
typedef char* (*pres_get_sphere_t)(str* pres_uri);
...
```


This function searches for a sphere definition in the published information
	if this has type RPID. If not found returns NULL. (the return value is
	allocated in private memory and should be freed)


### contains_presence


Field type:


```c
...
typedef int (*pres_contains_presence_t)(str* pres_uri);
...
```


This function searches is a presence uri has published any presence
	information. It return 1 if a record is found, -1 otherwise.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Anca Vamanu | 651 | 247 | 24146 | 12623 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 152 | 95 | 3223 | 1836 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 35 | 28 | 423 | 163 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 35 | 25 | 287 | 397 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 33 | 19 | 631 | 481 |
| 6. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 29 | 20 | 618 | 170 |
| 7. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 24 | 19 | 174 | 165 |
| 8. | Dan Pascu ([@danpascu](https://github.com/danpascu)) | 20 | 14 | 162 | 201 |
| 9. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 14 | 6 | 340 | 302 |
| 10. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | 11 | 7 | 150 | 105 |


**All remaining contributors**: Saúl Ibarra Corretgé ([@saghul](https://github.com/saghul)), Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Juha Heinanen ([@juha-h](https://github.com/juha-h)), Edson Gellert Schubert, Maksym Sobolyev ([@sobomax](https://github.com/sobomax)), Stanislaw Pitucha, Damien Sandras ([@dsandras](https://github.com/dsandras)), Kobi Eshun ([@ekobi](https://github.com/ekobi)), Dusan Klinec ([@ph4r05](https://github.com/ph4r05)), Angel Marin, Klaus Darilion, Carlos Oliva, Sergio Gutierrez, Kennard White, Elena-Ramona Modroiu, Jasper Hafkenscheid, Vasil Kolev, Benny Prijono, James Criscuolo, Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Vallimamod Abdullah, UnixDev, Denis Bilenko, Julián Moreno Patiño, John Riordan, Julien Blache.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Oct 2006 - Nov 2025 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Jan 2021 - Feb 2023 |
| 3. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Jul 2022 |
| 4. | Jasper Hafkenscheid | Jul 2022 - Jul 2022 |
| 5. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Sep 2011 - Jun 2021 |
| 6. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | Apr 2010 - Apr 2021 |
| 7. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Apr 2021 |
| 8. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Sep 2010 - Sep 2020 |
| 9. | Dan Pascu ([@danpascu](https://github.com/danpascu)) | Nov 2007 - Feb 2020 |
| 10. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |


**All remaining contributors**: James Criscuolo, Julián Moreno Patiño, Dusan Klinec ([@ph4r05](https://github.com/ph4r05)), Carlos Oliva, Damien Sandras ([@dsandras](https://github.com/dsandras)), Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Saúl Ibarra Corretgé ([@saghul](https://github.com/saghul)), Anca Vamanu, Vallimamod Abdullah, Kennard White, Stanislaw Pitucha, Angel Marin, John Riordan, Vasil Kolev, UnixDev, Sergio Gutierrez, Kobi Eshun ([@ekobi](https://github.com/ekobi)), Klaus Darilion, Denis Bilenko, Henning Westerholt ([@henningw](https://github.com/henningw)), Juha Heinanen ([@juha-h](https://github.com/juha-h)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Edson Gellert Schubert, Julien Blache, Benny Prijono, Elena-Ramona Modroiu.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Jasper Hafkenscheid, Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Walter Doekes ([@wdoekes](https://github.com/wdoekes)), Dan Pascu ([@danpascu](https://github.com/danpascu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Saúl Ibarra Corretgé ([@saghul](https://github.com/saghul)), Anca Vamanu, Kennard White, Angel Marin, Kobi Eshun ([@ekobi](https://github.com/ekobi)), Klaus Darilion, Henning Westerholt ([@henningw](https://github.com/henningw)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Edson Gellert Schubert, Juha Heinanen ([@juha-h](https://github.com/juha-h)), Elena-Ramona Modroiu.
