---
title: "Modules"
description: ""
---

## SIP related modules

### SIP signaling modules
* [**B2B_ENTITIES**](../modules/b2b_entities/README.md) - Back-to-Back User Agent Entities, @@green|stable@@
* [**B2B_LOGIC**](../modules/b2b_logic/README.md) - Back-to-Back User Agent Logic, @@green|stable@@
* [**CALL CENTER**](../modules/call_center/README.md) - Inbound call center system , @@green|stable@@
* [**DIALOG**](../modules/dialog/README.md) - Dialog support module , @@green|stable@@
* [**NAT_TRAVERSAL**](../modules/nat_traversal/README.md) - NAT traversal module , @@green|stable@@
* [**NATHELPER**](../modules/nathelper/README.md) - NAT traversal helper module , @@green|stable@@
* [**OPTIONS**](../modules/options/README.md) - OPTIONS server replier module , @@green|stable@@
* [**REGISTRAR**](../modules/registrar/README.md) - SIP Registrar implementation module , @@green|stable@@
* [**SIGNALING**](../modules/signaling/README.md) - SIP signaling module , @@green|stable@@
* [**UAC_REGISTRANT**](../modules/uac_registrant/README.md) - SIP Registrant implementation module , @@green|stable@@
* [**TM**](../modules/tm/README.md) - Transaction (stateful) module , @@green|stable@@
* [**SL**](../modules/sl/README.md) - Stateless replier module , @@green|stable@@
* [**MEDIA_EXCHANGE**](../modules/media_exchange/README.md) - Module to exchange SDP bodies between different SIP calls, @@green|stable@@
* [**CALLOPS**](../modules/callops/README.md) - Module to trigger different call operations on ongoing SIP calls, @@green|stable@@
* [**B2B_SDP_DEMUX**](../modules/b2b_sdp_demux/README.md) - Module to de-multiplex calls with multiple media streams, @@green|stable@@
* [**MSRP_UA**](../modules/msrp_ua/README.md) - MSRP User Agent module, @@green|stable@@

### SIP Routing modules
* [**CARRIERROUTE**](../modules/carrierroute/README.md) - routing extension suitable for carriers , @@green|stable@@
* [**CPL_C**](../modules/cpl_c/README.md) - CPL interpreter module , @@green|stable@@
* [**DISPATCHER**](../modules/dispatcher/README.md) - Dispatcher module , @@green|stable@@
* [**DROUTING**](../modules/drouting/README.md) - Dynamic Routing / LCR , @@green|stable@@
* [**QROUTING**](../modules/qrouting/README.md) - Quality-based Routing , @@green|stable@@
* [**EMERGENCY**](../modules/emergency/README.md) - Emergency module, @@green|stable@@
* [**ENUM**](../modules/enum/README.md) - ENUM lookup module , @@green|stable@@
* [**JABBER**](../modules/jabber/README.md) - JABBER IM and PRESENCE interconnection module , beta
* [**IMC**](../modules/imc/README.md) - Instant Messaging Conferencing module , @@green|stable@@
* [**LOAD_BALANCER**](../modules/load_balancer/README.md) - Load Balancer (for calls) module, @@green|stable@@
* [**MID_REGISTRAR**](../modules/mid_registrar/README.md) - SIP registration front-end with traffic throttling , @@green|stable@@
* [**MSILO**](../modules/msilo/README.md) - SIP message silo module , @@green|stable@@
* [**MSRP_GATEWAY**](../modules/msrp_gateway/README.md) - SIP MESSAGE / MSRP gateway module, @@green|stable@@
* [**RR**](../modules/rr/README.md) - Record-Route and Route module , @@green|stable@@
* [**SCRIPT_HELPER**](../modules/script_helper/README.md) - Embedded SIP routing logic and dialog management, @@green|stable@@
* [**OSP**](../modules/osp/README.md) - OSP peering module , @@green|stable@@

### SIP messaging operations
* [**COMPRESSION**](../modules/compression/README.md) - Message compression and compaction, @@green|stable@@
* [**DIVERSION**](../modules/diversion/README.md) - Diversion header insertion module , @@green|stable@@
* [**IDENTITY**](../modules/identity/README.md) - SIP Identity implementation, @@green|stable@@
* [**MAXFWD**](../modules/maxfwd/README.md) - Max-Forward processor module , @@green|stable@@
* [**MANGLER**](../modules/mangler/README.md) - SIP mangler module , @@green|stable@@
* [**PATH**](../modules/path/README.md) - Path support for SIP frontending , @@green|stable@@
* [**SIP_I**](../modules/sip_i/README.md) - ISUP manipulation module , @@green|stable@@
* [**SIPMSGOPS**](../modules/sipmsgops/README.md) - SIP operations module , @@green|stable@@
* [**STIR_SHAKEN**](../modules/stir_shaken/README.md) - STIR/SHAKEN support , @@green|stable@@
* [**TOPOLOGY_HIDING**](../modules/topology_hiding/README.md) - Provides Topology Hiding capabilities , @@green|stable@@
* [**UAC**](../modules/uac/README.md) - UAC functionalies (FROM mangling and UAC auth) , @@green|stable@@
* [**UAC_AUTH**](../modules/uac_auth/README.md) - UAC Authentication functionality, @@green|stable@@
* [**UAC_REDIRECT**](../modules/uac_redirect/README.md) - UAC redirection functionality , @@green|stable@@
* [**SST**](../modules/sst/README.md) - SIP Session Timer support , @@green|stable@@

### SIP Presence Modules
* [**PRESENCE**](../modules/presence/README.md) - Presence server module - common API , @@green|stable@@
* [**PRESENCE_CALLINFO**](../modules/presence_callinfo/README.md) - Extension to Presence server for Call-Info, @@green|stable@@
* [**PRESENCE_DIALOGINFO**](../modules/presence_dialoginfo/README.md) - Extension to Presence server for Dialog Info, @@green|stable@@
* [**PRESENCE_DFKS**](../modules/presence_dfks/README.md) - Extension to Presence server for Device Feature Key Synchronization, @@green|stable@@
* [**PRESENCE_MWI**](../modules/presence_mwi/README.md) - Extension to Presence server for Message Waiting Indication , @@green|stable@@
* [**PRESENCE_REGINFO**](../modules/presence_reginfo/README.md) - Extension to Presence server for "reg"-events according to RFC 3680 , @@green|stable@@
* [**PRESENCE_XCAPDIFF**](../modules/presence_xcapdiff/README.md) - Extension to Presence server for XCAP-DIFF event, @@green|stable@@
* [**PRESENCE_XML**](../modules/presence_xml/README.md) - Presence server module - presence & watcher info and XCAP , @@green|stable@@
* [**PUA**](../modules/pua/README.md) - Common API for presence user agent client , @@green|stable@@
* [**PUA_BLA**](../modules/pua_bla/README.md) - BLA extension for PUA , @@green|stable@@
* [**PUA_DIALOGINFO**](../modules/pua_dialoginfo/README.md) - Dialog-Info extension for PUA , @@green|stable@@
* [**PUA_MI**](../modules/pua_mi/README.md) - MI extension for PUA , @@green|stable@@
* [**PUA_REGINFO**](../modules/pua_reginfo/README.md) - Publisher for "reg"-events according to RFC 3680 , @@green|stable@@
* [**PUA_USRLOC**](../modules/pua_usrloc/README.md) - USRLOC extension for PUA , @@green|stable@@
* [**PUA_XMPP**](../modules/pua_xmpp/README.md) - XMPP extension for PUA (SIMPLE-XMPP presence gateway) , @@green|stable@@
* [**B2B_SCA**](../modules/b2b_sca/README.md) - Back-to-Back Shared Call Appearance, @@green|stable@@
* [**RLS**](../modules/rls/README.md) - Resource List Server implementation , @@green|stable@@
* [**XCAP**](../modules/xcap/README.md) - XCAP API provider , @@green|stable@@
* [**XCAP_CLIENT**](../modules/xcap_client/README.md) - XCAP client implementation , @@green|stable@@

---

## Scripting modules

### Script helper modules
* [**JSON**](../modules/json/README.md) - Manipulate JSON objects in OpenSIPS script, @@green|stable@@ 
* [**XML**](../modules/xml/README.md) - Manipulate XML documents in OpenSIPS script, @@green|stable@@
* [**CFGUTILS**](../modules/cfgutils/README.md) - Various utility functions, @@green|stable@@
* [**EXEC**](../modules/exec/README.md) - External exec module , @@green|stable@@
* [**TEXTOPS**](../modules/textops/README.md) - Text operations module, @@green|stable@@
* **AVPOPS** - renamed, see [SQLops module](../modules/sqlops/README.md)
* [**SQLOPS**](../modules/sqlops/README.md) - SQL DB operations module @@green|stable@@
* [**REGEX**](../modules/regex/README.md) - RegExp via PCRE library, @@green|stable@@
* [**MATHOPS**](../modules/mathops/README.md) - Floating point and rounding operations, @@green|stable@@
* [**BENCHMARK**](../modules/benchmark/README.md) - Script file benchmarking,  @@green|stable@@
* [**CARRIERROUTE**](../modules/carrierroute/README.md) - routing extension suitable for carriers , @@green|stable@@
* [**GFLAGS**](../modules/gflags/README.md) - Global shared flags module, @@green|stable@@
* [**PYTHON**](../modules/python/README.md) - Python scripting support, @@green|stable@@
* [**LUA**](../modules/lua/README.md) - Call LUA scripts from OpenSIPS cfg, @@green|stable@@
* [**PERL**](../modules/perl/README.md) - embed execution of Perl function , @@green|stable@@
* [**MMGEOIP**](../modules/mmgeoip/README.md) - MaxMind GeoIP module, @@green|stable@@
* [**UUID**](../modules/uuid/README.md) - UUID generation, @@green|stable@@
* [**MQUEUE**](../modules/mqueue/README.md) - Message queue system inter-process communication using the config file, beta

### Auth modules
* [**AUTH_AAA**](../modules/auth_aaa/README.md) - AAA-backend authentication module, @@green|stable@@
* [**AUTH**](../modules/auth/README.md) - Authentication Framework module, @@green|stable@@
* [**AUTH_DB**](../modules/auth_db/README.md) -Database-backend authentication module, @@green|stable@@
* [**AUTH_JWT**](../modules/auth_jwt/README.md) -Authentication over JSON Web Tokens, @@green|stable@@
* [**AUTH_AKA**](../modules/auth_aka/README.md) - Authentication using RFC 3310 AKA mechanism, beta
* [**AKA_AV_DIAMETER**](../modules/aka_av_diameter/README.md) - Fetches RFC 3310 AKA AVs using Cx/Dx Diameter interface, beta
* [**PERMISSIONS**](../modules/permissions/README.md) - Permissions control module , @@green|stable@@

### Accounting & Billing modules
* [**ACC**](../modules/acc/README.md) - Accounting module, @@green|stable@@
* [**CALL CONTROL**](../modules/call_control/README.md) - PrePaid application module , @@green|stable@@
* [**CGRATES**](../modules/cgrates/README.md) - Connector to  the CGRateS billing engine, @@green|stable@@

### Dialplan Modules
* [**ALIAS_DB**](../modules/alias_db/README.md) - Database SIP aliases module, @@green|stable@@
* [**DIALPLAN**](../modules/dialplan/README.md) - Dialplan management , @@green|stable@@
* [**DOMAIN**](../modules/domain/README.md) - Multi-domain support module , @@green|stable@@
* [**DOMAINPOLICY**](../modules/domainpolicy/README.md) - Policies to connect federations , @@green|stable@@
* [**GROUP**](../modules/group/README.md) - User-groups module with DB-backend , @@green|stable@@
* [**USERBLACKLIST**](../modules/userblacklist/README.md) - User black/white listing , @@green|stable@@
* [**SPEEDDIAL**](../modules/speeddial/README.md) - Per-user speed-dial controller module , @@green|stable@@
* [**PEERING**](../modules/peering/README.md) - Radius peering module , @@green|stable@@

### Data caching
* [**DNS_CACHE**](../modules/dns_cache/README.md) - Module for caching DNS records that can be used with any Key-Value back-end , @@green|stable@@
* [**USRLOC**](../modules/usrloc/README.md) - User location implementation module , @@green|stable@@
* [**SQL_CACHER**](../modules/sql_cacher/README.md) - SQL Caching module, @@green|stable@@
* [**RATE_CACHER**](../modules/rate_cacher/README.md) - Cache, Query, Reload or Update rates via MI, @@green|stable@@

### Traffic shaping module
* [**PIKE**](../modules/pike/README.md) - Flood detector module , @@green|stable@@
* [**QOS**](../modules/qos/README.md) - QOS (RTP) module , @@green|stable@@
* [**RATELIMIT**](../modules/ratelimit/README.md) - SIP traffic shaping module , @@green|stable@@
* [**FRAUD_DETECTION**](../modules/fraud_detection/README.md) - Detects fraudulent calls, @@green|stable@@

---

## Database modules

### SQL modules
* [**DB_BERKELEY**](../modules/db_berkeley/README.md) - Berkeley DB driver for DB API , @@green|stable@@
* [**DB_CACHEDB**](../modules/db_cachedb/README.md) - SQL to CacheDB translator , @@green|stable@@
* [**DB_FLATSTORE**](../modules/db_flatstore/README.md) - Fast writing-only text-backend for database module , @@green|stable@@
* [**DB_HTTP**](../modules/db_http/README.md) - HTTP-backend for DB API , @@green|stable@@
* [**DB_MYSQL**](../modules/db_mysql/README.md) - MYSQL-backend for database API module , @@green|stable@@
* [**DB_ORACLE**](../modules/db_oracle/README.md) - ORACLE-backend for database API module , @@green|stable@@
* [**DB_PERLVDB**](../modules/db_perlvdb/README.md) - Perl Virtual Database engine , @@green|stable@@
* [**DB_POSTGRES**](../modules/db_postgres/README.md) - POSTGRES-backend for database API module , @@green|stable@@
* [**DB_SQLITE**](../modules/db_sqlite/README.md) - SQLITE3-backend for database API module , @@green|stable@@
* [**DB_TEXT**](../modules/db_text/README.md) - Text-backend for database API module , @@green|stable@@
* [**DB_UNIXODBC**](../modules/db_unixodbc/README.md) - unixODBC driver module , @@green|stable@@
* [**DB_VIRTUAL**](../modules/db_virtual/README.md) - Middle-layer DB mixer, @@green|stable@@

### noSQL modules
* [**CACHEDB_CASSANDRA**](../modules/cachedb_cassandra/README.md) - Cassandra Implementation of CacheDB, @@green|stable@@
* [**CACHEDB_COUCHBASE**](../modules/cachedb_couchbase/README.md) - CouchBase Implementation of CacheDB, @@green|stable@@
* [**CACHEDB_LOCAL**](../modules/cachedb_local/README.md) - Local Implementation of CacheDB, @@green|stable@@
* [**CACHEDB_MEMCACHED**](../modules/cachedb_memcached/README.md) - Memcached Implementation of CacheDB, @@green|stable@@
* [**CACHEDB_MONGODB**](../modules/cachedb_mongodb/README.md) - MongoDB Implementation of CacheDB, @@green|stable@@
* [**CACHEDB_REDIS**](../modules/cachedb_redis/README.md) - Redis Implementation of CacheDB, @@green|stable@@
* [**CACHEDB_SQL**](../modules/cachedb_sql/README.md) - SQL-based Implementation of CacheDB, @@green|stable@@

---

## External Integration modules

### OpenSIPS API modules
* [**EVENT_DATAGRAM**](../modules/event_datagram/README.md) - Publish JSON-RPC notifications using UDP, @@green|stable@@
* [**EVENT_FLATSTORE**](../modules/event_flatstore/README.md) - Text/File backend for events, @@green|stable@@
* [**EVENT_KAFKA**](../modules/event_kafka/README.md) - Publish JSON-RPC notifications/generic messages to Apache Kafka , @@green|stable@@
* [**EVENT_STREAM**](../modules/event_stream/README.md) - Publish JSON-RPC notifications using TCP, @@green|stable@@
* [**EVENT_ROUTE**](../modules/event_route/README.md) - Route triggering based on events, @@green|stable@@
* [**EVENT_ROUTING**](../modules/event_routing/README.md) - Event-based routing, @@green|stable@@
* [**EVENT_RABBITMQ**](../modules/event_rabbitmq/README.md) - Publish JSON-RPC notifications using AMQP over TCP , @@green|stable@@
* [**EVENT_VIRTUAL**](../modules/event_virtual/README.md) - Aggregator of event backends (failover & balancing), @@green|stable@@
* [**EVENT_XMLRPC**](../modules/event_xmlrpc/README.md) - Event XMLRPC client module , @@green|stable@@
* [**MI_DATAGRAM**](../modules/mi_datagram/README.md) - DATAGRAM (unix and network) support for Management Interface , @@green|stable@@
* [**MI_FIFO**](../modules/mi_fifo/README.md) - FIFO support for Management Interface , @@green|stable@@
* [**MI_HTML**](../modules/mi_html/README.md) - Minimal web GUI for Management Interface , @@green|stable@@
* [**MI_HTTP**](../modules/mi_http/README.md) - HTTP support for Management Interface , @@green|stable@@
* [**MI_SCRIPT**](../modules/mi_script/README.md) - support for running Management Interface commands in script , @@green|stable@@
* [**MI_XMLRPC_NG**](../modules/mi_xmlrpc_ng/README.md) - XMLRPC support for Management Interface , @@green|stable@@
* [**HTTPD**](../modules/httpd/README.md) - Embedded HTTP server , @@green|stable@@
* [**PI_HTTP**](../modules/pi_http/README.md) - Provisioning Interface module , @@green|stable@@
* [**RABBITMQ**](../modules/rabbitmq/README.md) - Connector to a RabbitMQ message broker, @@green|stable@@
* [**RABBITMQ_CONSUMER**](../modules/rabbitmq_consumer/README.md) - Connect to RabbitMQ and receive events, @@green|stable@@
* [**STATISTICS**](../modules/statistics/README.md) - Script statistics support , @@green|stable@@
* [**STATUS_REPORT**](../modules/status_report/README.md) - Script Status/Report identifiers support , @@green|stable@@

### Media Relays
* [**MEDIAPROXY**](../modules/mediaproxy/README.md) - NAT traversal module , @@green|stable@@
* [**MSRP_RELAY**](../modules/msrp_relay/README.md) - Implementation of a Relay for the MSRP protocol , @@green|stable@@
* [**RTPENGINE**](../modules/rtpengine/README.md) - Connector to RTPengine external RTP relay , @@green|stable@@
* [**RTPPROXY**](../modules/rtpproxy/README.md) - Connector to RTPproxy external RTP relay, @@green|stable@@
* [**RTP_RELAY**](../modules/rtp_relay/README.md) - Interface for different RTP relay engines, @@green|stable@@

### External integration (non-SIP protocols)
* [**AAA_DIAMETER**](../modules/aaa_diameter/README.md) - Diameter backend for the AAA API, @@green|stable@@
* [**AAA_RADIUS**](../modules/aaa_radius/README.md) - RADIUS backend for the AAA API, @@green|stable@@
* [**FREESWITCH**](../modules/freeswitch/README.md) - FreeSWITCH ESL connection manager, @@green|stable@@
* [**FREESWITCH_SCRIPTING**](../modules/freeswitch_scripting/README.md) - FreeSWITCH events & commands at OpenSIPS script level, @@green|stable@@
* [**H350**](../modules/h350/README.md) - H350 implementation , @@green|stable@@
* [**HTTP2D**](../modules/http2d/README.md) - Programmable HTTP/2 Server, beta
* [**JSONRPC**](../modules/jsonrpc/README.md) - Execute JSON-RPC commands, @@green|stable@@
* [**LAUNCH_DARKLY**](../modules/launch_darkly/README.md) - Launch Darkly integration, beta
* [**LDAP**](../modules/ldap/README.md) - LDAP connector , @@green|stable@@
* [**PROMETHEUS**](../modules/prometheus/README.md) - export statistics to a [Prometheus](http://prometheus.io/) server, @@green|stable@@
* [**REST_CLIENT**](../modules/rest_client/README.md) - Implementation of an HTTP client , @@green|stable@@
* [**SEAS**](../modules/seas/README.md) - Sip Express Application Server (interface module) , @@green|stable@@
* [**SIPCAPTURE**](../modules/sipcapture/README.md) - SipCapture module , @@green|stable@@
* [**SIPREC**](../modules/siprec/README.md) - SIP Recording module , @@green|stable@@
* [**TRACER**](../modules/tracer/README.md) - Collects SIP, logs, DNS or REST queries and ships them to various backends , @@green|stable@@
* [**SNGTC**](../modules/sngtc/README.md) - Voice Transcoding in OpenSIPS using Sangoma hardware , @@green|stable@@
* [**SNMPStats**](../modules/snmpstats/README.md) - SNMP interface for statistics module , @@green|stable@@
* [**STUN**](../modules/stun/README.md) - Built-in STUN server , @@green|stable@@ - 
* [**XMPP**](../modules/xmpp/README.md) - SIP-to-XMPP Gateway (SIP to Jabber/Google Talk) , @@green|stable@@

---

## OpenSIPS protocols and infrastructure
* [**CLUSTERER**](../modules/clusterer/README.md) - Define and configure an OpenSIPS cluster, @@green|stable@@
* [**TLS_MGM**](../modules/tls_mgm/README.md) - TLS management module , @@green|stable@@
* [**TLS_OPENSSL**](../modules/tls_openssl/README.md) - TLS operations implemented using the openSSL library , @@green|stable@@
* [**TLS_WOLFSSL**](../modules/tls_wolfssl/README.md) - TLS operations implemented using the wolfSSL library , @@green|stable@@
* [**TCP_MGM**](../modules/tcp_mgm/README.md) - TCP connections management module , @@green|stable@@
* [**PROTO_BIN**](../modules/proto_bin/README.md) - Binary INterface protocol module - implements inter-OPENSIPS communication , @@green|stable@@
* [**PROTO_BINS**](../modules/proto_bins/README.md) - Binary INterface over TLS protocol module - implements Secure inter-OPENSIPS communication , @@green|stable@@
* [**PROTO_HEP**](../modules/proto_hep/README.md) - HEP protocol module - implements HEP transport for SIP , @@green|stable@@
* [**PROTO_IPSEC**](../modules/proto_ipsec/README.md) - implements IMS IPSec protocol according to TS 33.203 specs, beta
* [**PROTO_SCTP**](../modules/proto_sctp/README.md) - SCTP protocol module - implements SCTP transport for SIP , @@green|stable@@
* [**PROTO_TCP**](../modules/proto_tcp/README.md) - TCP protocol module - implements TCP-plain transport for SIP , @@green|stable@@
* [**PROTO_TLS**](../modules/proto_tls/README.md) - TLS protocol module - implements TLS transport for SIP , @@green|stable@@
* [**PROTO_UDP**](../modules/proto_udp/README.md) - UDP protocol module - implements UDP-plain transport for SIP , @@green|stable@@
* [**PROTO_WS**](../modules/proto_ws/README.md) - WebSocket protocol module - implements WS transport for SIP , @@green|stable@@
* [**PROTO_WSS**](../modules/proto_wss/README.md) - WebSocket Secure protocol module - implements WSS transport for SIP , @@green|stable@@
* [**PROTO_SMPP**](../modules/proto_smpp/README.md) - SMPP (Short Message Peer-to-Peer) protocol module - implements transport for SMPP messages, @@green|stable@@
