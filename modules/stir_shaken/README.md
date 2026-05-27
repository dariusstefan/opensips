---
title: "STIR/SHAKEN Module"
description: "This module adds support for implementing STIR/SHAKEN (RFC 8224, RFC 8588) Authentication and Verification services in OpenSIPS."
---

## Admin Guide


### Overview {#overview}


This module adds support for implementing STIR/SHAKEN (RFC 8224, RFC 8588)
	Authentication and Verification services in OpenSIPS.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *No dependencies on other OpenSIPS modules*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *openssl (libssl)*.


### Exported Parameters {#exported_parameters}


#### auth_date_freshness (integer) {#param_auth_date_freshness}


The maximum number of seconds that the value in the Date header field
		can be older than the current time.


This parameter is only relevant
		for the [stir shaken auth](#func_stir_shaken_auth) function.


The default value is *60*.


```opensips title="Set auth_date_freshness parameter"
...
modparam("stir_shaken", "auth_date_freshness", 300)
...
```


#### verify_date_freshness (integer) {#param_verify_date_freshness}


The maximum number of seconds that the value in the Date header field can be
		older than the current time. Also, if the *iat* value in
		the PASSporT is different than the Date value, but remains within the
		permitted interval, it will be used in the verification process (for the
		reconstructed PASSporT) instead of the Date value.


If the [require date hdr](#param_require_date_hdr) parameter is set to not
		required and the Date header is missing, the *iat* value
		will be used for this check instead.


This parameter is only relevant for the
		[stir shaken verify](#func_stir_shaken_verify) function.


The default value is *60*.


```opensips title="Set verify_date_freshness parameter"
...
modparam("stir_shaken", "verify_date_freshness", 300)
...
```


#### ca_list (string) {#param_ca_list}


Path to a file containing trusted CA certificates for the verifier.
		The certificates must be in PEM format, one after another.


```opensips title="Set ca_list parameter"
...
modparam("stir_shaken", "ca_list", "/stir_certs/ca_list.pem")
...
```


#### ca_dir (string) {#param_ca_dir}


Path to a directory containing trusted CA certificates for the verifier.
		The certificates in the directory must be in hashed form, as described
		in the [openssl documentation](https://www.openssl.org/docs/manmaster/man3/X509_LOOKUP_hash_dir.html) for the
		*Hashed Directory Method*.


```opensips title="Set ca_dir parameter"
...
modparam("stir_shaken", "ca_dir", "/stir_certs/cas")
...
```


#### crl_list (string) {#param_crl_list}


Path to a file containing certificate revocation lists (CRLs) for the verifier.


```opensips title="Set crl_list parameter"
...
modparam("identity", "crl_list", "/stir_certs/crl_list.pem")
...
```


#### crl_dir (string) {#param_crl_dir}


Path to a directory containing certificate revocation lists (CRLs) for
		the verifier. The CRLs in the directory must be in hashed form, as described
		in the [openssl documentation](https://www.openssl.org/docs/manmaster/man3/X509_LOOKUP_hash_dir.html) for the
		*Hashed Directory Method*.


```opensips title="Set crl_dir parameter"
...
modparam("stir_shaken", "crl_dir", "/stir_certs/crls")
...
```


#### e164_strict_mode (integer) {#param_e164_strict_mode}


Require a leading *"+"* to be present in
		the originating/destination SHAKEN identity, on top of mandating an E.164
		telephone number by default.  Additionally, require the URI to be either
		a *tel* URI or a *sip* /
		*sips* URI with the *user=phone*
		parameter.


The default value is *0* (disabled).


```opensips title="Set e164_strict_mode parameter"
...
modparam("stir_shaken", "e164_strict_mode", 1)
...
```


#### e164_max_length (integer) {#param_e164_max_length}


This parameter allows the 15-digit number length restriction of the E.164
		format to be bypassed.  Especially useful in scenarios where various
		telephony number prefixes are in use, causing some numbers to exceed
		the standard maximum length.


The default value is *15*.


```opensips title="Set e164_max_length parameter"
...
modparam("stir_shaken", "e164_max_length", 16)
...
```


#### require_date_hdr (integer) {#param_require_date_hdr}


Specifies whether the Date header is mandatory when doing verification
	    with the [stir shaken verify](#func_stir_shaken_verify) function.


A value of *1* means required and *0*
	    not required.


If the parameter is set to "not required" but the Date header is present in the
	    message, the header value will be used as normally to check the freshness (as
	    configured in the [verify date freshness](#param_verify_date_freshness)
	    parameter). If the Date header is indeed missing, the value of the
	    *iat* claim in the PASSporT will be used instead.


The default value is *1* (required).


```opensips title="Set require_date_hdr parameter"
...
modparam("stir_shaken", "require_date_hdr", 0)
...
```


### Exported Functions {#exported_functions}


#### stir_shaken_auth(attest, origid, cert, pkey, x5u, [orig], [dest], [out]) {#func_stir_shaken_auth}


This function performs the steps of an authentication service. Before
		calling this function though, you must ensure:


- authority - the server is authoritative for the identity in question;
- authentication - the originator is authorized to claim the given identity.


Meaning of the parameters is as follows:


- *attest (string)* - value of the 'attest' claim
			to be included in the PASSporT. The following values can be used:
			
				
				*A* or *full*
				
				
				*B* or *partial*
				
				
				*C* or *gateway*
- *origid (string)* - value of the 'origid' claim
			to be included in the PASSporT. Treated by the module as an opaque string.
- *cert (string)* - the X.509 certificate used to
			compute the signature, in PEM format.
- *pkey (string)* - the private key used to
			compute the signature, in PEM format.
- *x5u (string)* - value of the 'x5u' claim to be
			included in the PASSporT. Treated by the module as an opaque string.
- *orig (string, optional)* - telephone number to
			be used as the originating identity in the PASSporT. If missing, this value
			will be derived from the SIP message.
- *dest (string, optional)* - telephone number to
			be used as the destination identity in the PASSporT. If missing, this value
			will be derived from the SIP message.
- *out (string, no expand, optional)* - name of an
			output variable to store the Identity header or the following flags:
			
			
				*req* - the Identity header will be appended
				to the current request message;
			
			
				*rpl* - the Identity header will be appended
				to all replies that will be generated by OpenSIPS for this request.
If this parameter is missing, the Identity header will be appended
			to the current request message.
If an output variable is provided, it should be given as a quoted string,
			eg. *"$var(identity_hdr)"*.


The function returns the following values:


- 1: Success
- -1: Internal error
- -2: Identity header already exists
- -3: Failed to derive identity from SIP message because the
		    URI is not a telephone number
- -4: Date header value is older than local policy for freshness
- -5: The current time or Date header value does not fall within
		    the certificate validity


This function can be used from REQUEST_ROUTE.


```c title="stir_shaken_auth() usage"
...
stir_shaken_auth("A", "4437c7eb-8f7a-4f0e-a863-f53a0e60251a",
	$var(cert), $var(privKey), "https://certs.example.org/cert.pem");
...
```


#### stir_shaken_verify(cert, err_code, err_reason, [orig], [dest]) {#func_stir_shaken_verify}


This function performs the steps of an verification service.


Meaning of the parameters is as follows:


- *cert (string)* - the X.509 certificate used to
			verify the signature, in PEM format.
- *err_code (var)* - output variable that will
			store the SIP response code associated with an eventual error of the
			verification process.
- *err_reason (var)* - output variable that will
			store the SIP response reason phrase associated with an eventual error of the
			verification process.
- *orig (string, optional)* - telephone number to
			be used as the originating identity in the verification prcess. If missing,
			this value will be derived from the SIP message.
- *dest (string, optional)* - telephone number to
			be used as the destination identity in the verification process. If missing,
			this value will be derived from the SIP message.


The function returns the following values:


- 1: Success
- -1: Internal error
- -2: No Identity or Date header found
- -3: Failed to derive identity from SIP message because the
		    URI is not a telephone number
- -4: Invalid identity header
- -5: Unsupported 'ppt' or 'alg' Identity header parameter
- -6: Date header value is older than local policy for freshness
- -7: The Date header value does not fall within the certificate validity
- -8: Invalid certificate
- -9: Signature does not verify successfully


This function can be used from REQUEST_ROUTE.


```c title="stir_shaken_verify() usage"
...
$var(rc) = stir_shaken_verify($var(cert), $var(err_code), $var(err_reason));
if ($var(rc) < -1) {
	send_reply($var(err_sip_code), $var(err_sip_reason));
	exit;
}
...
```


#### stir_shaken_check() {#func_stir_shaken_check}


This function checks the Identity header in order to validate the
		STIR/SHAKEN information in terms of format. It detects issues such as:
		missing or badly formated PASSporT claims, unsupported extensions etc.


The function returns the following values:


- 1: Success
- -1: Internal error
- -2: No Identity header found
- -3: Invalid identity header
- -4: Unsupported 'ppt' or 'alg' Identity header parameter


This function can be used from REQUEST_ROUTE.


```opensips title="stir_shaken_check() usage"
...
if (stir_shaken_check()) {
	xlog("forwarding call to stir/shaken verification service\n");
	...
}
...
```


#### stir_shaken_check_cert() {#func_stir_shaken_check_cert}


This function checks if the current time falls within the given
		certificate's validity period.


The function returns the following values:


- 1: Success
- -1: Internal error
- -2: Certificate is not valid


This function can be used from REQUEST_ROUTE.


```c title="stir_shaken_check_cert() usage"
...
# update expired cached certificates
cache_fetch("local", $identity(x5u), $var(cert));
if (!stir_shaken_check_cert($var(cert))) {
	rest_get($identity(x5u), $var(cert));
	cache_store("local", $identity(x5u), $var(cert));
}
...
```


### Exported Pseudo-Variables {#exported_pseudo_variables}


#### $identity(field) {#pv_identity}


This is a read-only pseudo-variable that provides access to the
	parsed information from the Identity header, through the following
	subnames:


- *header* - the entire PASSporT header;
- *x5u* - the value of the 'x5u' PASSporT claim;
- *payload* - the entire PASSporT payload;
- *attest* - the value of the 'attest' PASSporT claim;
- *dest* - the value of the 'tn' member of the 'dest'
		PASSporT claim;
- *iat* - the value of the 'iat' PASSporT claim;
- *orig* - the value of the 'tn' member of the 'orig'
		PASSporT claim;
- *origid* - the value of the 'origid' PASSporT claim;


```opensips title="identity usage"
...
	# acquire the certificate to use for the verification process
	$var(rc) = rest_get($identity(x5u), $var(cert));
	if ($var(rc) < 0) {
		send_reply(436, "Bad Identity Info");
		exit;
	}
	...
	xlog("Verified caller:$identity(orig), attestation level: $identity(attest)\n");
...
	
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 58 | 26 | 3129 | 324 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 22 | 17 | 246 | 120 |
| 3. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 18 | 18 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 4 | 2 | 8 | 9 |
| 5. | kworm83 | 4 | 2 | 7 | 2 |
| 6. | Kevin | 3 | 1 | 2 | 2 |
| 7. | John Burke ([@john08burke](https://github.com/john08burke)) | 2 | 1 | 4 | 0 |
| 8. | Andriy Pylypenko ([@bambyster](https://github.com/bambyster)) | 2 | 1 | 1 | 0 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Nov 2019 - Sep 2023 |
| 2. | Kevin | Feb 2022 - Feb 2022 |
| 3. | kworm83 | Jan 2022 - Jan 2022 |
| 4. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | Oct 2019 - Aug 2021 |
| 5. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Jan 2021 - Jul 2021 |
| 6. | John Burke ([@john08burke](https://github.com/john08burke)) | Apr 2021 - Apr 2021 |
| 7. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Jan 2021 - Jan 2021 |
| 8. | Andriy Pylypenko ([@bambyster](https://github.com/bambyster)) | Aug 2020 - Aug 2020 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)).


*Documentation Copyrights:*


Copyright © 2019 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
