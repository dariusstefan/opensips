---
title: "perl Module"
description: "The time needed when writing a new OpenSIPS module unfortunately is quite high, while the options provided by the configuration file are limited to the features implemented in the modules."
---

## Admin Guide


### Overview {#overview}


The time needed when writing a new OpenSIPS module unfortunately is quite high, while the
		options provided by the configuration file are limited to the features implemented in the
		modules.


With this Perl module, you can easily implement your own OpenSIPS extensions in Perl. This allows
		for simple access to the full world of CPAN modules. SIP URI rewriting could be implemented
		based on regular expressions; accessing arbitrary data backends, e.g. LDAP or Berkeley DB files,
		is now extremely simple.


### Installing the module


This Perl module is loaded in opensips.cfg (just like all the other modules) with
		loadmodule("/path/to/perl.so");.


For the Perl module to compile, you need a reasonably recent version of perl (tested
		with 5.8.8) linked dynamically. It is strongly advised to use a threaded version.
		The default binary packages from your favorite Linux distribution should work fine.


Cross compilation is supported by the Makefile. You need to set the environment variables
		PERLLDOPTS, PERLCCOPTS and TYPEMAP to values similar to the output of


```c
PERLLDOPTS: perl -MExtUtils::Embed -e ldopts
PERLCCOPTS: perl -MExtUtils::Embed -e ccopts
TYPEMAP:    echo "`perl -MConfig -e 'print $Config{installprivlib}'`/ExtUtils/typemap"
```


The exact position of your (precompiled!) perl libraries depends on the setup of your
		environment.


### Using the module


The Perl module has two interfaces: The perl side, and the OpenSIPS side. Once a Perl
		function is defined and loaded via the module parameters (see below), it may be
		called in OpenSIPS's configuration at an arbitary point. E.g., you could write
		a function "ldap_alias" in Perl, and then execute


```c
...
if (perl_exec("ldap_alias")) {
	...
}	
...
```


just as you would have done with the current alias_db module.


The functions you can use are listed in the
		[exported functions](#exported_functions) section below.


On the Perl side, there are a number of functions that let you read and modify the
		current SIP message, such as the RURI or the message flags. An introduction
		to the Perl interface and the full reference documentation can be found below.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- The "sl" module is needed for sending replies uppon fatal errors. All other modules
				can be accessed from the Perl module, though.


#### External Libraries or Applications


The following libraries or applications must be installed before 
		running OpenSIPS with this module loaded:


- *Perl 5.8.x or later*


Additionally, a number of perl modules should be installed. The OpenSIPS::LDAPUtils package
		relies on Net::LDAP to be installed. One of the sample scripts needs IPC::Shareable


This module has been developed and tested with Perl 5.8.8, but should work with any
		5.8.x release. Compilation is possible with 5.6.x, but its behavior is unsupported.
		Earlier versions do not work.


On current Debian systems, at least the following packages should be installed:


- perl
- perl-base
- perl-modules
- libperl5.8
- libperl-dev
- libnet-ldap-perl
- libipc-shareable-perl


It was reported that other Debian-style distributions (such as Ubuntu) need the
		same packages.


On SuSE systems, at least the following packages should be installed:


- perl
- perl-ldap
- IPC::Shareable perl module from CPAN


Although SuSE delivers a lot of perl modules, others may have to be fetched
		from CPAN. Consider using the program "cpan2rpm" - which, in turn,
		is available on CPAN. It creates RPM files from CPAN.


### Exported Parameters {#exported_parameters}


#### filename (string) {#param_filename}


This is the file name of your script. This may be set once only, but it may include an arbitary
			number of functions and "use" as many Perl module as necessary.


*May not be empty!*


```c title="Set filename parameter"
...
modparam("perl", "filename", "/home/john/opensips/myperl.pl")
...
```


#### modpath (string) {#param_modpath}


The path to the Perl modules included (OpenSIPS.pm et.al). It is not absolutely
			crucial to set this path,
			as you *may* install the Modules in Perl's standard path, or update
			the "%INC" variable from within your script. Using this module parameter
			is the standard behavior, though.


```c title="Set modpath parameter"
...
modparam("perl", "modpath", "/usr/local/lib/opensips/perl/")
...
```


### Exported Functions {#exported_functions}


#### perl_exec_simple(func, [param]) {#func_perl_exec_simple}


Calls a perl function *without* passing it the current SIP message.
			May be used for very simple simple requests that do not have to fiddle with the message
			themselves, but rather return information values about the environment.


The first parameter is the function to be called.
			An arbitrary string may optionally be passed as a parameter.


The function returns *1* if the perl function was successfully called
			or *-1* if an internal error occured. Note that it does not propagate
			the return value of the perl function.


This function can be used from REQUEST_ROUTE, FAILURE_ROUTE, ONREPLY_ROUTE and BRANCH_ROUTE.


```c title="perl_exec_simple() usage"
...
if ($rm=="INVITE") {
	perl_exec_simple("dosomething", "on invite messages");
};
...
```


#### perl_exec(func, [param]) {#func_perl_exec}


Calls a perl function *with* passing it the current SIP message.
			The SIP message is reflected by a Perl module that gives you access to the information
			in the current SIP message (OpenSIPS::Message).


The first parameter is the function to be called.
			An arbitrary string may be passed as a parameter.


The function returns back to the OpenSIPS script the value returned by the perl function.
			Note that if this value is *0* the script execution
			will be stoped, similarly to calling *exit*.


This function can be used from REQUEST_ROUTE, FAILURE_ROUTE, ONREPLY_ROUTE and BRANCH_ROUTE.


```c title="perl_exec() usage"
...
if (perl_exec("ldapalias")) {
	...
};
...
```


## OpenSIPS Perl API


### OpenSIPS {#ID-d83a4c83b0cd455aef9602128b87c323}


This module provides access to a limited number of OpenSIPS core
      functions. As the most interesting functions deal with SIP messages,
      they are located in the OpenSIPS::Message class below.


#### log(level,message) {#ID-3ae476990b7b1fe73d972e68eb52f93c}


Logs the message with OpenSIPS's logging facility. The logging level
	is one of the following:


```c
* L_ALERT
* L_CRIT
* L_ERR
* L_WARN
* L_NOTICE
* L_INFO
* L_DBG
```


Please note that this method is *NOT* automatically exported, as it collides
	with the perl function log (which calculates the logarithm). Either
	explicitly import the function (via `use OpenSIPS qw ( log );`), or call
	it with its full name:


```c
OpenSIPS::log(L_INFO, "foobar");
```


### OpenSIPS::Message {#ID-949656abaac653354242d1d0e9b418be}


This package provides access functions for an OpenSIPS `sip_msg` structure and its
      sub-components. Through its means it is possible to fully configure
      alternative routing decisions.


#### getType() {#ID-4462b43922a45955436db6a77eff1274}


Returns one of the constants SIP_REQUEST, SIP_REPLY, SIP_INVALID
	stating the type of the current message.


#### getStatus() {#ID-48eb6f3897940320d3dca81eee99e60f}


Returns the status code of the current Reply message. This function
	is invalid in Request context!


#### getReason() {#ID-06ad673da0ada52b0d53a16dc5edf3c7}


Returns the reason of the current Reply message. This function is
	invalid in Request context!


#### getVersion() {#ID-defa3c4593f2232a246f800880c3db8d}


Returns the version string of the current SIP message.


#### getRURI() {#ID-40655f47473dfd0b09ab79f021379661}


This function returns the recipient URI of the present SIP message:


`my $ruri =
	$m->getRURI();`


getRURI returns a string. See ["getParsedRURI()"](#ID-f20c57aaa92a757d7152aa0479ee1fc0)
	below how to receive a parsed structure.


This function is valid in request messages only.


#### getMethod() {#ID-09d760cb43aa35ee3338a63959e1ad52}


Returns the current method, such as `INVITE`, `REGISTER`, `ACK` and so on.


`my $method =
	$m->getMethod();`


This function is valid in request messages only.


#### getFullHeader() {#ID-29a7bfa2b533d46b900690c15e89dac0}


Returns the full message header as present in the current message.
	You might use this header to further work with it with your
	favorite MIME package.


`my $hdr =
	$m->getFullHeader();`


#### getBody() {#ID-d4227612556f15885224f8d7c9baa63d}


Returns the message body.


#### getMessage() {#ID-5c55b75dca8e197bf8d4e9195f3fc78b}


Returns the whole message including headers and body.


#### getHeader(name) {#ID-2cd2368ec9da09ded819e54da7ed7e56}


Returns the body of the first message header with this name.


`print
	$m->getHeader("To");`


**`"John"
	<sip:john@doe.example>`**


#### getHeaderNames() {#ID-e6f4b718fa39a84855f41d5048abfd17}


Returns an array of all header names. Duplicates possible!


#### moduleFunction(func,string1,string2) {#ID-7b0efd72c8ec89bc43dad940590b40c6}


Search for an arbitrary function in module exports and call it with
	the parameters self, string1, string2.


`string1` and/or `string2` may be omitted.


As this function provides access to the functions that are exported
	to the OpenSIPS configuration file, it is autoloaded for unknown
	functions. Instead of writing


```c
$m->moduleFunction("sl_send_reply", "500", "Internal Error");
$m->moduleFunction("xlog", "L_INFO", "foo");
```


you may as well write


```c
$m->sl_send_reply("500", "Internal Error");
$m->xlog("L_INFO", "foo");
```


WARNING


In OpenSIPS 1.2, only a limited subset of module functions is
	available. This restriction will be removed in a later version.


Here is a list of functions that are expected to be working (not
	claiming completeness):


```c
* alias_db_lookup
* consume_credentials
* is_rpid_user_e164
* append_rpid_hf
* bind_auth
* avp_print
* cpl_process_register
* cpl_process_register_norpl
* load_dlg
* ds_next_dst
* ds_next_domain
* ds_mark_dst
* ds_mark_dst
* is_from_local
* is_uri_host_local
* dp_can_connect
* dp_apply_policy
* enum_query (without parameters)
* enum_fquery (without parameters)
* is_from_user_enum (without parameters)
* i_enum_query (without parameters)
* imc_manager
* jab_* (all functions from the jabber module)
* sdp_mangle_ip
* sdp_mangle_port
* encode_contact
* decode_contact
* decode_contact_header
* fix_contact
* use_media_proxy
* end_media_session
* m_store
* m_dump
* fix_nated_contact
* unforce_rtp_proxy
* force_rtp_proxy
* fix_nated_register
* add_rcv_param
* options_reply
* checkospheader
* validateospheader
* requestosprouting
* checkosproute
* prepareosproute
* prepareallosproutes
* checkcallingtranslation
* reportospusage
* mangle_pidf
* mangle_message_cpim
* add_path (without parameters)
* add_path_received (without parameters)
* prefix2domain
* allow_routing (without parameters)
* allow_trusted
* pike_check_req
* handle_publish
* handle_subscribe
* stored_pres_info
* bind_pua
* send_publish
* send_subscribe
* pua_set_publish
* loose_route
* record_route
* load_rr
* sip_trace
* sl_reply_error
* sd_lookup
* sstCheckMin
* append_time
* has_body (without parameters)
* is_peer_verified
* t_newtran
* t_release
* t_relay (without parameters)
* t_flush_flags
* t_check_trans
* t_was_cancelled
* uac_restore_from
* uac_auth
* has_totag
* tel2sip
* check_to
* check_from
* radius_does_uri_exist
* ul_* (All functions exported by the usrloc module for user access)
* xmpp_send_message
```


#### log(level,message) (deprecated type) {#ID-9fb36151aa2a86585e66dc19227b7d92}


Logs the message with OpenSIPS's logging facility. The logging level
	is one of the following:


```c
* L_ALERT
* L_CRIT
* L_ERR
* L_WARN
* L_NOTICE
* L_INFO
* L_DBG
```


The logging function should be accessed via the OpenSIPS module
	variant. This one, located in OpenSIPS::Message, is deprecated.


#### rewrite_ruri(newruri) {#ID-b6b5403d6a2b667316e7ce7ddd0937d0}


Sets a new destination (recipient) URI. Useful for rerouting the
	current message/call.


```c
if ($m->getRURI() =~ m/\@somedomain.net/) {
  $m->rewrite_ruri("sip:dispatcher\@organization.net");
}
```


#### setFlag(flag) {#ID-43bdd45f2267be3165993d728ae6ac58}


Sets a message flag. The constants as known from the C API may be
	used, when Constants.pm is included.


#### resetFlag(flag) {#ID-d3f836fca6d5be320332751cb544a50b}


Resets a message flag.


#### isFlagSet(flag) {#ID-9df30424b7e93814c7928cf5899a12cc}


Returns whether a message flag is set or not.


#### pseudoVar(string) {#ID-cfb8c8675a6620394b21ab786379acfa}


Returns a new string where all pseudo variables are substituted by
	their values. Can be used to receive the values of single
	variables, too.


**Please remember that you need to escape the
	'$' sign in perl strings!**


#### append_branch(branch,qval) {#ID-098e67bc760ef445343eb9e8f93ece8b}


Append a branch to current message.


#### serialize_branches(clean_before, keep_order) {#ID-bff5c83f8c28e72abf1d8a5204310202}


Serialize branches.


#### next_branches() {#ID-db3ce723c8e1880caf5fc0cb9ed7aa4d}


Next branches.


#### getParsedRURI() {#ID-f20c57aaa92a757d7152aa0479ee1fc0}


Returns the current destination URI as an OpenSIPS::URI object.


### OpenSIPS::URI {#ID-ea7b3e8a4dc6ddf7af0e02f7cec8eef1}


This package provides functions for access to sip_uri structures.


#### user() {#ID-f0b0fe573c97823c7db7e6d2f763eecd}


Returns the user part of this URI.


#### host() {#ID-13f823f4d5d264262256ce6c13ebda7f}


Returns the host part of this URI.


#### passwd() {#ID-f16f4d3590aeadbb89be6381c63d31ba}


Returns the passwd part of this URI.


#### port() {#ID-0e78ab3771467ade60bb813acff96e1f}


Returns the port part of this URI.


#### params() {#ID-f09a07f5c225a4cdc0c8cf1b562feca5}


Returns the params part of this URI.


#### headers() {#ID-6ac4960ecbb77b9b208182e39c3fced6}


Returns the headers part of this URI.


#### transport() {#ID-edbeee75601887c5522ec606864fcf06}


Returns the transport part of this URI.


#### ttl() {#ID-9b062529593a90fa98b8fdaf147d9625}


Returns the ttl part of this URI.


#### user_param() {#ID-af281c7d5fa29dfa3d059c93e2ece82f}


Returns the user_param part of this URI.


#### maddr() {#ID-cc3ad600892aefceee9bb7f7d083b9eb}


Returns the maddr part of this URI.


#### method() {#ID-bcde93cc9055b03c23ac61f4f7aba922}


Returns the method part of this URI.


#### lr() {#ID-ffda2446aa820304039c53eaca5c1383}


Returns the lr part of this URI.


#### r2() {#ID-e07722a580a9e670d428835b7efacfaf}


Returns the r2 part of this URI.


#### transport_val() {#ID-20d6cdc41465025baceba8056215ac58}


Returns the transport_val part of this URI.


#### ttl_val() {#ID-6643223dbdb425407fe9801fd4d2fa5b}


Returns the ttl_val part of this URI.


#### user_param_val() {#ID-aa87e27dd0cce4de62a0f32a7ecb7a33}


Returns the user_param_val part of this URI.


#### maddr_val() {#ID-85b278e13c1c6169d612921b32a42c3a}


Returns the maddr_val part of this URI.


#### method_val() {#ID-64d75002d2db35d7a66847e0c92f37a9}


Returns the method_val part of this URI.


#### lr_val() {#ID-cdb1f6d4201cd9b6ee19e6f58236a900}


Returns the lr_val part of this URI.


#### r2_val() {#ID-af30e585b8decaf3e377375210e977b6}


Returns the r2_val part of this URI.


### OpenSIPS::AVP {#ID-f092a1ce520e2e575c41702289adf85e}


This package provides access functions for OpenSIPS's AVPs. These
      variables can be created, evaluated, modified and removed through
      this package.


Please note that these functions do NOT support the notation used in
      the configuration file, but directly work on strings or numbers. See
      documentation of add method below.


#### add(name,val) {#ID-d4453222c49e08dc556c25a6586a00bc}


Add an AVP.


Add an OpenSIPS AVP to its environment. name and val may both be
	integers or strings; this function will try to guess what is
	correct. Please note that


```c
OpenSIPS::AVP::add("10", "10")
```


is something different than


```c
OpenSIPS::AVP::add(10, 10)
```


due to this evaluation: The first will create _string_ AVPs with
	the name 10, while the latter will create a numerical AVP.


You can modify/overwrite AVPs with this function.


#### get(name) {#ID-ad3f402b1c78426a3a1de4d0c1d9ad6c}


get an OpenSIPS AVP:


```c
my $numavp = OpenSIPS::AVP::get(5);
my $stravp = OpenSIPS::AVP::get("foo");
```


#### destroy(name) {#ID-15db533161502fc4591ec9acdd990ef4}


Destroy an AVP.


```c
OpenSIPS::AVP::destroy(5);
OpenSIPS::AVP::destroy("foo");
```


### OpenSIPS::Utils::PhoneNumbers {#ID-b2dcbd51ac3a3e6208edd5e4f6a5ef63}


OpenSIPS::Utils::PhoneNumbers - Functions for canonical forms of phone
      numbers.


```c
use OpenSIPS::Utils::PhoneNumbers;

my $phonenumbers = new OpenSIPS::Utils::PhoneNumbers(
     publicAccessPrefix => "0",
     internationalPrefix => "+",
     longDistancePrefix => "0",
     areaCode => "761",
     pbxCode => "456842",
     countryCode => "49"
   );

$canonical = $phonenumbers->canonicalForm("07612034567");
$number    = $phonenumbers->dialNumber("+497612034567");
```


A telphone number starting with a plus sign and containing all dial
      prefixes is in canonical form. This is usally not the number to dial
      at any location, so the dialing number depends on the context of the
      user/system.


The idea to canonicalize numbers were taken from hylafax.


Example: +497614514829 is the canonical form of my phone number, 829
      is the number to dial at Pyramid, 4514829 is the dialing number from
      Freiburg are and so on.


To canonicalize any number, we strip off any dial prefix we find and
      then add the prefixes for the location. So, when the user enters the
      number 04514829 in context pyramid, we remove the publicAccessPrefix
      (at Pyramid this is 0) and the  pbxPrefix (4514 here). The result is
      829. Then we add all the general dial prefixes - 49 (country) 761
      (area) 4514 (pbx) and 829, the number itself => +497614514829


To get the dialing number from a canonical phone number, we substract
      all general prefixes until we have something


As said before, the interpretation of a phone number depends on the
      context of the location. For the functions in this package, the
      context is created through the `new` operator.


The following fields should be set:


```c
'longDistancePrefix' 
'areaCode'
'pbxCode' 
'internationalPrefix'
'publicAccessPrefix'
'countryCode'
```


This module exports the following functions when `use`ed:


#### new(publicAccessPrefix,internationalPrefix,longDistancePrefix,countryCode,areaCode,pbxCode) {#ID-bd49dfdfc2284e87abd95fbce2d63cbd}


The new operator returns an object of this type and sets its
	locational context according to the passed parameters. See
	
	OpenSIPS::Utils::PhoneNumbers
	 above.


#### canonicalForm( number [, context] ) {#ID-aa52786fee82fe6e26ac766b30a1a4fa}


Convert a phone number (given as first argument) into its canonical
	form. When no context is passed in as the second argument, the
	default context from the systems configuration file is used.


#### dialNumber( number [, context] ) {#ID-31479dee3ae037c27cd7a84aa7358bd2}


Convert a canonical phone number (given in the first argument) into
	a number to to dial.  WHen no context is given in the second
	argument, a default context from the systems configuration is used.


### OpenSIPS::LDAPUtils::LDAPConf {#ID-05a2ba8f92ec3fe3d26bcb69d169a1b1}


OpenSIPS::LDAPUtils::LDAPConf - Read openldap config from standard
      config files.


```c
use OpenSIPS::LDAPUtils::LDAPConf;
my $conf = new OpenSIPS::LDAPUtils::LDAPConf();
```


This module may be used to retrieve the global LDAP configuration as
      used by other LDAP software, such as `nsswitch.ldap` and `pam-ldap`. The configuration is
      usualy stored in `/etc/openldap/ldap.conf`


When used from an account with sufficient privilegs (e.g. root), the
      ldap manager passwort is also retrieved.


#### Constructor new() {#ID-77bbb24cd07a34db564171c3072c9f8c}


Returns a new, initialized `OpenSIPS::LDAPUtils::LDAPConf`
	object.


#### Method base() {#ID-09b3942c6aa316dd4659cc1d84cfab21}


Returns the servers base-dn to use when doing queries.


#### Method host() {#ID-cabd9882c784cf96cf68ca988c1dae0f}


Returns the ldap host to contact.


#### Method port() {#ID-467ec702bbcdb63e337bbff9e3a5bf01}


Returns the ldap servers port.


#### Method uri() {#ID-1a047746515766cf720b5cc0f154c783}


Returns an uri to contact the ldap server. When there is no
	ldap_uri in the configuration file, an `ldap:` uri is constucted from host
	and port.


#### Method rootbindpw() {#ID-35ebbd3612d026b4176d2eb8eae428b0}


Returns the ldap "root" password.


Note that the `rootbindpw`
	is only available when the current account has sufficient privilegs
	to access `/etc/openldap/ldap.secret`.


#### Method rootbinddn() {#ID-5e0c4c1de34f9247439d8d10d6734b9e}


Returns the DN to use for "root"-access to the ldap server.


#### Method binddn() {#ID-cdb5e3ce4599d0613db43f1f387f7ea6}


Returns the DN to use for authentication to the ldap server. When
	no bind dn has been specified in the configuration file, returns
	the `rootbinddn`.


#### Method bindpw() {#ID-3c51d4eeaae6feb7498a2f562b176df3}


Returns the password to use for authentication to the ldap server.
	When no bind password has been specified, returns the `rootbindpw` if any.


### OpenSIPS::LDAPUtils::LDAPConnection {#ID-10d643239760bcd4a2286051808d077e}


OpenSIPS::LDAPUtils::LDAPConnection - Perl module to perform simple
      LDAP queries.


OO-Style interface:


```c
use OpenSIPS::LDAPUtils::LDAPConnection;
my $ldap = new OpenSIPS::LDAPUtils::LDAPConnection;
my @rows = $ldap-search("uid=andi","ou=people,ou=coreworks,ou=de");
```


Procedural interface:


```c
use OpenSIPS::LDAPUtils::LDAPConnection;
my @rows = $ldap->search(
      new OpenSIPS::LDAPUtils::LDAPConfig(), "uid=andi","ou=people,ou=coreworks,ou=de");
```


This perl module offers a somewhat simplified interface to the
      `Net::LDAP` functionality.
      It is intended for cases where just a few attributes should be
      retrieved without the overhead of the full featured `Net::LDAP`.


#### Constructor new( [config, [authenticated]] ) {#ID-58e29437b48125a88033370afe8fa32b}


Set up a new LDAP connection.


The first argument, when given, should be a hash reference pointing
	to to the connection parameters, possibly an `OpenSIPS::LDAPUtils::LDAPConfig`
	object. This argument may be `undef` in which case a new
	(default) `OpenSIPS::LDAPUtils::LDAPConfig`
	object is used.


When the optional second argument is a true value, the connection
	will be authenticated. Otherwise an anonymous bind is done.


On success, a new `LDAPConnection` object is
	returned, otherwise the result is `undef`.


#### Function/Method search( conf, filter, base, [requested_attributes ...]) {#ID-129bd2634f1c36a401321f2fa34be026}


perform an ldap search, return the dn of the first matching
	directory entry, unless a specific attribute has been requested, in
	wich case the values(s) fot this attribute are returned.


When the first argument (conf) is a `OpenSIPS::LDAPUtils::LDAPConnection`,
	it will be used to perform the queries. You can pass the first
	argument implicitly by using the "method" syntax.


Otherwise the `conf`
	argument should be a reference to a hash containing the connection
	setup parameters as contained in a `OpenSIPS::LDAPUtils::LDAPConf`
	object. In this mode, the `OpenSIPS::LDAPUtils::LDAPConnection`
	from previous queries will be reused.


##### Arguments: {#ID-fb371aca693935a2ea3467c59473c78f}


**conf**


configuration object, used to find host,port,suffix and
		  use_ldap_checks


**filter**


ldap search filter, eg '(mail=some@domain)'


**base**


search base for this query. If undef use default suffix,
		  concat base with default suffix if the last char is a ','


**requested_attributes**


retrieve the given attributes instead of the dn from the
		  ldap directory.


##### Result: {#ID-5c0875c73d1d44335ab71b50d8930584}


Without any specific `requested_attributes`, return
	  the dn of all matching entries in the LDAP directory.


When some `requested_attributes` are given,
	  return an array with those attibutes. When multiple entries match
	  the query, the attribute lists are concatenated.


### OpenSIPS::VDB {#ID-94fdb1e4103544a567ac38ee23c8a4d1}


This package is an (abstract) base class for all virtual databases.
      Derived packages can be configured to be used by OpenSIPS as a
      database.


The base class itself should NOT be used in this context, as it does
      not provide any functionality.


### OpenSIPS::Constants {#ID-d97df315e2044f430319fc4cd9937aa5}


This package provides a number of constants taken from enums and
      defines of OpenSIPS header files. Unfortunately, there is no mechanism
      for updating the constants automatically, so check the values if you
      are in doubt.


### OpenSIPS::VDB::Adapter::Speeddial {#ID-9658bbad14831f685cca13a5c9bb4335}


This adapter can be used with the speeddial module.


### OpenSIPS::VDB::Adapter::Alias {#ID-5cdb14839de18c32782e3d768e170da7}


This package is intended for usage with the alias_db module. The
      query VTab has to take two arguments and return an array of two
      arguments (user name/domain).


#### query(conds,retkeys,order) {#ID-748da8d11c20576948ef68d62753a06a}


Queries the vtab with the given arguments for request conditions,
	keys to return and sort order column name.


### OpenSIPS::VDB::Adapter::AccountingSIPtrace {#ID-8165ac0b808b36ff72ee4032e0f62881}


This package is an Adapter for the acc and tracer modules,
      featuring only an insert operation.


### OpenSIPS::VDB::Adapter::Describe {#ID-048913e15394a9675cfe02af5c340528}


This package is intended for debug usage. It will print information
      about requested functions and operations of a client module.


Use this module to request schema information when creating new
      adapters.


### OpenSIPS::VDB::Adapter::Auth {#ID-682dd320dd536e7fb0de4a24e9d1e46e}


This adapter is intended for usage with the auth_db module. The VTab
      should take a username as an argument and return a (plain text!)
      password.


### OpenSIPS::VDB::ReqCond {#ID-f245c032bf4b4837415c871537a63e3b}


This package represents a request condition for database access,
      consisting of a column name, an operator (=, <, >, ...), a data
      type and a value.


This package inherits from OpenSIPS::VDB::Pair and thus includes its
      methods.


#### new(key,op,type,name) {#ID-c50f7cee51ceffdb7253101a36d6b7b3}


Constructs a new Column object.


#### op() {#ID-cb5f02a3144f3af620484048b11472af}


Returns or sets the current operator.


### OpenSIPS::VDB::Pair {#ID-4be9a5fa03b11c2c2dad3594fc3b585d}


This package represents database key/value pairs, consisting of a
      key, a value type, and the value.


This package inherits from OpenSIPS::VDB::Value and thus has the same
      methods.


#### new(key,type,name) {#ID-e6cc5b1a997ea8e29d2a00b986784dc0}


Constructs a new Column object.


#### key() {#ID-9c70856322a870405521ad299a6785d6}


Returns or sets the current key.


### OpenSIPS::VDB::VTab {#ID-41cd8a9b5b94c05139d2a7f62d5a967b}


This package handles virtual tables and is used by the OpenSIPS::VDB
      class to store information about valid tables. The package is not
      inteded for end user access.


#### new() {#ID-a42ddb87d67983028cf8d1b7d6f46baa}


```c
Constructs a new VTab object
```


#### call(op,[args]) {#ID-c00e2c0b9abc7cd56be59f2b051a538c}


Invokes an operation on the table (insert, update, ...) with the
	given arguments.


### OpenSIPS::VDB::Value {#ID-dd87abbf6c21bba656e60f50bf1db81b}


This package represents a database value. Additional to the data
      itself, information about its type is stored.


#### stringification {#ID-f8210a055e2dc48b7cd31acfbe483ef9}


When accessing a OpenSIPS::VDB::Value object as a string, it simply
	returns its data regardless of its type. =cut


use strict;


package OpenSIPS::VDB::Value;


use overload '""' => \&stringify;


sub stringify { shift->{data} }


use OpenSIPS; use OpenSIPS::Constants;


our @ISA = qw ( OpenSIPS::Utils::Debug );


#### new(type,data) {#ID-246aa300bb8cb062e3126e6eddd094c4}


Constructs a new Value object. Its data type and the data are
	passed as parameters.


#### type() {#ID-1b24f490feaba7e8ffde5919a34a3ea5}


Returns or sets the current data type. Please consider using the
	constants from OpenSIPS::Constants


#### data() {#ID-8ed1b290ce747e1e6fc97d5c253d67eb}


Returns or sets the current data.


### OpenSIPS::VDB::Column {#ID-01b04d0ae14c862d7543534782702100}


This package represents database column definition, consisting of a
      column name and its data type.


#### Stringification {#ID-c1f63ab2d499e0ffb0eff65d3d1bcaa3}


When accessing a OpenSIPS::VDB::Column object as a string, it simply
	returns its column name regardless of its type. =cut


package OpenSIPS::VDB::Column;


use overload '""' => \&stringify;


sub stringify { shift->{name} }


use OpenSIPS; use OpenSIPS::Constants;


our @ISA = qw ( OpenSIPS::Utils::Debug );


#### new(type,name) {#ID-9be0a542b4be1d36c09e57a0a7785b8b}


Constructs a new Column object. Its type and the name are passed as
	parameters.


#### type( ) {#ID-9e27faa58b7b4659e2d511fd4ed58ccd}


Returns or sets the current type. Please consider using the
	constants from OpenSIPS::Constants


#### name() {#ID-e3219d620b6d1b4f5656ee466ef3cf22}


Returns or sets the current column name.


#### OpenSIPS::VDB::Result {#ID-07e232dc068e186782a8d0c0bbae91d3}


This class represents a VDB result set. It contains a column
	definition, plus an array of rows. Rows themselves are simply
	references to arrays of scalars.


#### new(coldefs,[row, row, ...]) {#ID-54e80aeec6aaaa66afb9548d25388dea}


The constructor creates a new Result object. Its first parameter is
	a reference to an array of OpenSIPS::VDB::Column objects. Additional
	parameters may be passed to provide initial rows, which are
	references to arrays of scalars.


#### coldefs() {#ID-c77fe41ba24f3f04c12254c9f001bb27}


```c
Returns or sets the column definition of the object.
```


#### rows() {#ID-a20f0baefcdf46a3e923bb02203b3792}


```c
Returns or sets the rows of the object.
```


## Perl samples


### sample directory


There are a number of example scripts in the "samples/". They are
		documented well. Read them, it will explain a lot to you :)


If you want to use any of these scripts directly in your implementation, you
		can use Perl's "require" mechanism to import them (just remember
		that you need to use quotes when require'ing .pl files).


#### Script descriptions


The included sample scripts are described below:


##### branches.pl


The minimal function in branches.pl demonstrates that you can access the "append_branch"
				function from within perl, just as you would have done from your normal configuration file.
				You'll find documentation on the concepts of branching in the OpenSIPS documentation.


##### firstline.pl


Message's first_line structure may be evaluated. Message can be either of
				SIP_REQUEST or SIP_REPLY. Depending on that, different information can be received.
				This script demonstrates these functions.


##### flags.pl


The perl module provides access to OpenSIPS's flagging mechanism. The flag names available
				for OpenSIPS modules are made available through the OpenSIPS::Constants package, so you can
				flag messages as "green", "magenta" etc.


The first function, setflag, demonstrates how the "green" flag is set. In the second function,
				readflag, the "green" and "magenta" flags are evaluated.


##### functions.pl


This sample script demonstrates different things related to calling functions from within perl,
				and the different types of functions you can offer for OpenSIPS access.


"exportedfuncs" simply demonstrates that you can use the moduleFunction method
				to call functions offered by other modules. The results are equivalent to calling these
				functions from your config file. In the demonstrated case, telephone calls with a destination
				number beginning with 555... are rejected with an internal server error. Other destination
				addresses are passed to the alias_db module.


Please note that the moduleFunction method is not fully available in OpenSIPS 1.2. See the method's
				documentation for details.


"paramfunc" shows that you can pass arbitrary strings to perl functions. Do with
				them whatever you want :)


"autotest" demonstrates that unknown functions in OpenSIPS::Message objects are
				automatically transformed into calls to module functions.


The "diefunc"s show that dying perl scripts - by "manual" dying, or because of script
				errors - are handled by the OpenSIPS package. The error message is logged through OpenSIPS's logging
				mechanism. Please note that this only works correctly if you do NOT overwrite the default die handler.
				Oh, yes, that works for warnings, too.


##### headers.pl


Header extraction is among the most crucial functionalities while processing SIP messages. This
				sample script demonstrates access to header names and values within two sample functions.


"headernames" extracts all header names and logs their names.


"someheaders" logs the contents of the two headers, "To" and
				"WWW-Contact". As you can see, headers that occur more than once are retrieved
				as an array, which may be accessed by Perl's array accessing methods.


##### logging.pl


For debugging purposes, you probably want to write messages to the syslog. The "logdemo"
				shows three ways to access the OpenSIPS log function: it is available through the OpenSIPS class as well
				as through the OpenSIPS::Message class.


Remember that you can use exported functions from other modules. You may thus as well use the
				"xlog" module and it's xlog function.


The L_INFO, L_DBG, L_ERR, L_CRIT... constants are available through the OpenSIPS::Constants package.


##### messagedump.pl


This script demonstrates how to access the whole message header of the current message. Please note that
				modifications on the message made by earlier function calls in your configuration script may NOT be
				reflected in this dump.


##### persistence.pl


When processing SIP messages, you may want to use persistent data across multiple calls to your
				Perl functions. Your first option is to use global variables in your script. Unfortunately,
				these globals are not visible from the mulitple instances of OpenSIPS. You may want to use a
				mechanism such as the IPC::Shareable shared memory access package to correct this.


##### phonenumbers.pl


The OpenSIPS::Utils::PhoneNumbers package provides two methods for the transformation of local to
				canonical telephone numbers, and vice versa. This script demonstrates it's use.


##### pseudovars.pl


This script demonstrates the Perl module's "pseudoVar" method. It may be used to
				retrieve the values of current pseudo variables.


You might notice that there is no particular function for setting pseudo variables; you may use
				the exported functions from the sqlops module, though.


## Frequently Asked Questions


**Q: Are there known bugs in the Perl module?**


The Perl module does have a few shortcomings that may be regarded as bugs.


**Q: Where can I find more about OpenSIPS?**


Take a look at [https://opensips.org/](https://opensips.org/).


**Q: Where can I post a question about this module?**


First at all check if your question was already answered on one of
			our mailing lists:

E-mails regarding any stable OpenSIPS release should be sent to 
			users@lists.opensips.org and e-mails regarding development versions
			should be sent to devel@lists.opensips.org.

If you want to keep the mail private, send it to 
			users@lists.opensips.org.


**Q: How can I report a bug?**


Please follow the guidelines provided at:
			[https://github.com/OpenSIPS/opensips/issues](https://github.com/OpenSIPS/opensips/issues).


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bastian Friedrich | 116 | 38 | 8597 | 284 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 51 | 32 | 715 | 688 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 25 | 22 | 100 | 61 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 22 | 18 | 62 | 133 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 20 | 10 | 264 | 408 |
| 6. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 19 | 14 | 164 | 125 |
| 7. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 5 | 3 | 10 | 42 |
| 8. | Julien Blache | 4 | 1 | 80 | 64 |
| 9. | Edson Gellert Schubert | 4 | 1 | 0 | 141 |
| 10. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | 3 | 1 | 100 | 7 |


**All remaining contributors**: Ancuta Onofrei, Konstantin Bokarius, Boris Ratner, Julián Moreno Patiño, Klaus Darilion, Fabian Gast ([@fgast](https://github.com/fgast)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Aaron Meriwether, Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)), Dan Pascu ([@danpascu](https://github.com/danpascu)).


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Jun 2011 - Mar 2026 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Dec 2006 - May 2025 |
| 3. | Aaron Meriwether | May 2024 - May 2024 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - May 2024 |
| 5. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Oct 2022 - Feb 2023 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Jan 2020 |
| 7. | Fabian Gast ([@fgast](https://github.com/fgast)) | Jan 2020 - Jan 2020 |
| 8. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 9. | Julián Moreno Patiño | Feb 2016 - Feb 2016 |
| 10. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | Oct 2015 - Oct 2015 |


**All remaining contributors**: Boris Ratner, Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)), Dan Pascu ([@danpascu](https://github.com/danpascu)), Klaus Darilion, Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Bastian Friedrich, Ancuta Onofrei, Julien Blache.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)), Klaus Darilion, Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Bastian Friedrich.
## License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
