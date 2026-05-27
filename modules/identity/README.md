---
title: "Identity Module"
description: "This module adds support for SIP Identity (see RFC 4474)."
---

## Admin Guide


### Overview {#overview}


This module adds support for SIP Identity (see RFC 4474).


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *No dependencies on other OpenSIPS modules*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *openssl (libssl)*.


### Exported Parameters {#exported_parameters}


#### privKey (string) {#param_privKey}


Filename of private RSA-key of authentication service. This file must be in PEM format.


```opensips title="Set privKey parameter"
...
modparam("identity", "privKey", "/etc/openser/privkey.pem")
...
```


#### authCert (string) {#param_authCert}


Filename of certificate which belongs to `privKey`. This file must be in PEM format.


```opensips title="Set authCert parameter"
...
modparam("identity", "authCert", "/etc/openser/cert.pem")
...
```


#### certUri (string) {#param_certUri}


URI from which the certificate of the authentication service can be acquired. This string will be placed in the Identity-Info header.


```opensips title="Set certUri parameter"
...
modparam("identity", "certUri", "http://www.myserver.com/cert.pem")
...
```


#### verCert (string) {#param_verCert}


Path containing certificates for the verifier. Certificates must be in PEM format. The URI in the Identity-Info header field is used to find the corresponding certificate for the request. For this purpose the verifier replaces every character which is not alphanumeric, no "_" and no "." with a "-". A "." at the beginning of the URI is forbidden. If the URI is "http://www.test.com/cert.pem" the verifier will look for the file "http---www.test.com-cert.pem", for example.
		It is also possible to store a whole certificate chain in a file. In this case certificates must be in right order, end certificate first.


```opensips title="Set verCert parameter"
...
modparam("identity", "verCert", "/etc/openser/verCert/")
...
```


#### caList (string) {#param_caList}


File containing all trusted (root) certificates for the verifier. Certificates must be in PEM format.


```opensips title="Set caList parameter"
...
modparam("identity", "caList", "/etc/openser/caList.pem")
...
```


#### crlList (string) {#param_crlList}


File containing certificate revocation lists (crls) for the verifier. Setting this parameter is only necessary if `useCrls` is set to "1".


```opensips title="Set crlList parameter"
...
modparam("identity", "crlList", "/etc/openser/crls.pem")
...
```


#### useCrls (integer) {#param_useCrls}


Switch to decide whether to use revocation lists ("1") or not ("0").


*Default value is "0".*


```opensips title="Set privKey parameter"
...
modparam("identity", "useCrls", 1)
...
```


### Exported Functions {#exported_functions}


#### authservice() {#func_authservice}


This function performs the steps of an authentication service. Before you call this function, you have to ensure
		that


- the server is responsible for this request (from URI matches local SIP domain)
- the sender of the request is authorized to claim the identity given in the From header field.


- -3: Date header field does not match validity period of cert. Identity header has not been added.
- -2: message out of time (e.g. message to old), Identity header has not been added.
- -1: An error occurred.
- 1: everything OK, Identity header has been added.


```opensips title="authservice() usage"
...
# CANCEL and ACK cannot be challenged
if (($rm=="CANCEL") || ($rm"ACK"))
{
    route(1); # forward
    exit;
}

# some clients (e.g. Kphone) do not answer, when a BYE is challenged
if ($rm=="BYE")
{
    route(1); # forward
    exit;
}

### Authentication Service ###

# check whether I am authoritative
if($fd!="mysipdomain.de")
{
    route(1); # forward
    exit;
}

if(!proxy_authorize("mysipdomain.de","subscriber"))
{
    proxy_challenge("mysipdomain.de",0);
    exit;
}

if ($au!=$fU)
{
    sl_send_reply(403, "Use From=ID");
    exit;
}
consume_credentials();
        
authservice();
switch($retcode)
{
    case -3:
        xlog("L_DBG" ,"authservice: Date header field does not match validity period of cert\n");
        break;
    case -2:
        xlog("L_DBG" ,"authservice: msg out of time (max. +- 10 minutes allowed)\n");
        break;
    case -1:
        xlog("L_DBG" ,"authservice: ERROR, returnvalue: -1\n");
        break;
    case 1:
        xlog("L_DBG" ,"authservice: everything OK\n");
        break;
    default:
        xlog("L_DBG" ,"unknown returnvalue of authservice\n");
        
}

route(1); #forward with ($retcode=1) or without ($retcode!=1) Identity header
...
```


#### verifier() {#func_verifier}


This function performs the steps of an verifier. The returned code tells you the result of the verification:


- -438: Signature does not correspond to the message. 438-response should be send.
- -437: Certificate cannot be validated. 437-response should be send.
- -436: Certificate is not available. 436-response should be send.
- -428: Message does not have an Identity header. 428-response should be send.
- -3: Error verifying Date header field.
- -2: Authentication service is not authoritative.
- -1: An unknown error occurred.
- 1: verification OK


```opensips title="verifier() usage"
...
# we have to define the same exceptions as we did for the authentication service
if (($rm=="CANCEL") || ($rm"ACK")) 
{ 
    route(1); # forward
    exit;
}
    
if ($rm=="BYE")
{
    route(1); # forward
    exit;
}
   
verifier();
switch($retcode)
{
    case -438:
        xlog("L_DBG" ,"verifier: returnvalue: -438\n");
        sl_send_reply(438, "Invalid Identity Header");
        exit;
        break;
    case -437:
        xlog("L_DBG" ,"verifier: returnvalue: -437\n");
        sl_send_reply(437, "Unsupported Certificate");
        exit;
        break;
    case -436:
        xlog("L_DBG" ,"verifier: returnvalue: -436\n");
        sl_send_reply(436, "Bad Identity-Info");
        exit;
        break;
    case -428:
        xlog("L_DBG" ,"verifier: returnvalue: -428\n");
        sl_send_reply(428, "Use Identity Header");
        exit;
        break;
    case -3:
        xlog("L_DBG" ,"verifier: error verifying Date header field\n");
        exit;
        break;
    case -2:
        xlog("L_DBG" ,"verifier: authentication service is not authoritative\n");
        exit;
        break;
    case -1:
        xlog("L_DBG" ,"verifier: ERROR, returnvalue: -1\n");
        exit;
        break;
    case 1:
        xlog("L_DBG" ,"verifier: verification OK\n");
        route(1); # forward
        exit;
        break;
    default:
        xlog("L_DBG" ,"unknown returnvalue of verifier\n");
        exit;
}
exit;
...
```


### Known Limitations


- Certificates are not downloaded. They have to be stored locally.
- Call-IDs of valid requests containing an Identity header are not recorded. 
				Hence the verifier does not provide full replay protection.
- Authentication service and verifier use the original request. Changes resulting from message processing in OpenSER script are ignored.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Alexander Christ | 22 | 1 | 2571 | 0 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 14 | 12 | 28 | 19 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 11 | 9 | 82 | 24 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 11 | 8 | 23 | 67 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 6 | 4 | 15 | 16 |
| 6. | Sergio Gutierrez | 4 | 2 | 7 | 2 |
| 7. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 3 | 1 | 18 | 2 |
| 8. | Julián Moreno Patiño | 3 | 1 | 2 | 2 |
| 9. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 3 | 1 | 1 | 18 |
| 10. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |


**All remaining contributors**: Saúl Ibarra Corretgé ([@saghul](https://github.com/saghul)).


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - Jan 2021 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Sep 2020 - Sep 2020 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Apr 2020 |
| 4. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jul 2009 - Apr 2019 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 7. | Julián Moreno Patiño | Feb 2016 - Feb 2016 |
| 8. | Saúl Ibarra Corretgé ([@saghul](https://github.com/saghul)) | Oct 2014 - Oct 2014 |
| 9. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Jan 2013 - Jan 2013 |
| 10. | Sergio Gutierrez | Feb 2009 - Feb 2009 |


**All remaining contributors**: Alexander Christ.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Alexander Christ.


*Documentation Copyrights:*


Copyright © 2007 Alexander Christ, Cologne University of Applied Sciences
