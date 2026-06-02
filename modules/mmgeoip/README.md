---
title: "mmgeoip Module"
description: "This module is a lightweight wrapper for the MaxMind GeoIP API. It adds IP address-to-location lookup capability to OpenSIPS scripts."
---

## Admin Guide


### Overview {#overview}


This module is a lightweight wrapper for the MaxMind GeoIP
	  API. It adds IP address-to-location lookup capability to
	  OpenSIPS scripts.


Lookups are executed against the freely-available GeoLite City
	  database; and the non-free GeoIP City database is drop-in
	  compatible. All lookup fields provided by the API are accessible
	  by the script. Visit the
	  [MaxMind
	  website](https://dev.maxmind.com/geoip/) for more information on the location
	  databases.


The module is compatible with both legacy GeoIP and the
		newer GeoIP2 APIs and databases.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following  modules must be loaded before this module:


- *No dependencies on other OpenSIPS modules*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *libGeoIP* - for the legacy GeoIP API and database;
- *libmaxminddb* - for the GeoIP2 API and database.


You can select which GeoIP library to use by setting the GEOIP environment variable,
	  	before compiling the module, to one of the following values:


- *GEOIPLEGACY  **** libGeoIP library shall be used
- *GEOIP2  **** libmaxminddb library shall be used;


IMPORTANT: If the selected library is not installed the module won't compile.
	  	NOTE: If GEOIP env is not set, the module will try to find which GeoIP library is installed,
	  		prioritizing libmaxminddb.


### Exported Parameters {#exported_parameters}


#### mmgeoip_city_db_path (string) {#param_mmgeoip_city_db_path}


Path to either a GeoLite or GeoIP City database file.


*Mandatory parameter.*


```c title="Set 'mmgeoip_city_db_path' parameter"
...
modparam("mmgeoip", "mmgeoip_city_db_path",
  "/usr/share/GeoIP/GeoLiteCity.dat")
...
		
```


#### cache_type (string) {#param_cache_type}


Databse memory caching options. The following options are available:


- *STANDARD* - Read database from file system;
					least memory used;
- *MMAP_CACHE* - Load database into mmap allocated
					memory;
					*WARNING: this option will cause a segmentation
							fault if database file is changed at runtime!*
- *MEM_CACHE_CHECK* - Load database into memory;
					this mode checks for database updates; if database was modified,
					the file will be reloaded after 60 seconds; it will be slower than
					*MMAP_CACHE* but it will allow reloads;


Default value for this parameter is *MMAP_CACHE*.


NOTE: If libmaxminddb is used, this parameter will be ignored as the library only
	  	supports loading the database into mmap allocated memory.


```c title="Set 'cache_type' parameter"
...
modparam("mmgeoip", "cache_type","MEM_CACHE_CHECK")
...
		
```


### Exported Functions {#exported_functions}


#### mmg_lookup([fields,]src,dst) {#func_mmg_lookup}


Looks up information specified by `field` associated with
		the IP address `src`. The resulting data is loaded in
		*reverse* order into the `dst` AVP.


Parameters:


- *fields* (string, optional) - a list of elements delimited by
			one of these separators: ':', '|', ',', '/' or ' '(space). Accepts the following tokens:
	  		  
	  			 *lat* - Latitude
	  			 *lon* - Longitude
	  			 *cont* - Continent
	  			 *cc* - Country Code
	  			 *reg* - Region
	  			 *city* - City
	  			 *pc* - Postal Code
	  			 *dma* - DMA Code
	  			 *ac* - Area Code, only available in the legacy GeoIP
	  			database
	  			 *tz* - Time Zone
- *src* (string) - IP address
- *dst* (var) - AVP to return the information associated with the IP in.


When using the GeoIP2 library, each token from the list given in the `fields`
	  	parameter can be provided as a path to a specific key in the data structure associated with an
	  	IP. Thus, the token format is '*key_name*.*key_name*[*.key_name*]*'. If a key's value is an array, instead of a subkey name, an index should be
	  	provided in order to select the appropriate value.


Example tokens: '*country.names.en*', '*continent.names.en*', '*subdivisions.0.iso_code*'. For more details about
	  	the available fields in the database and the key names that should be used to
	  	retrieve them, check the [MaxMind
		GeoIP2 documentation](https://dev.maxmind.com/geoip/geoip2/).


This function can be used from REQUEST_ROUTE, FAILURE_ROUTE,
		ONREPLY_ROUTE, BRANCH_ROUTE,ERROR_ROUTE, and LOCAL_ROUTE.


```c title="mmg_lookup usage"
...
if(mmg_lookup("lon:lat",$si,$avp(lat_lon))) {
  xlog("L_INFO","Source IP latitude:$(avp(lat_lon)[0])\n");
  xlog("L_INFO","Source IP longitude:$(avp(lat_lon)[1])\n");
};
...
# fields format only supported for GeoIP2
if(mmg_lookup("continent.names.en:country.iso_code,",$si,$avp(geodata))) {
  xlog("L_INFO","Source IP country code:$(avp(geodata)[0])\n");
  xlog("L_INFO","Source IP continent:$(avp(geodata)[1])\n");
};
...
		
```


### Known Issues


It is not currently possible to load an updated location
	  database without first stalling the server.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 15 | 5 | 653 | 238 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 10 | 8 | 32 | 24 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 10 | 8 | 18 | 9 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 10 | 8 | 16 | 38 |
| 5. | Kobi Eshun ([@ekobi](https://github.com/ekobi)) | 9 | 3 | 480 | 4 |
| 6. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 6 | 4 | 6 | 6 |
| 7. | Sergio Gutierrez | 4 | 2 | 5 | 3 |
| 8. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | 3 | 1 | 84 | 1 |
| 9. | Anca Vamanu | 3 | 1 | 6 | 2 |
| 10. | Ken Rice | 3 | 1 | 1 | 1 |


**All remaining contributors**: Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)).


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Ken Rice | Sep 2025 - Sep 2025 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Oct 2022 - Feb 2023 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Jun 2011 - Apr 2021 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Jan 2021 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 6. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jul 2009 - Apr 2019 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 8. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | May 2016 - May 2016 |
| 9. | Kobi Eshun ([@ekobi](https://github.com/ekobi)) | Nov 2008 - Dec 2009 |
| 10. | Anca Vamanu | Sep 2009 - Sep 2009 |


**All remaining contributors**: Sergio Gutierrez.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Kobi Eshun ([@ekobi](https://github.com/ekobi)).
