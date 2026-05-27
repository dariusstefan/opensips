---
title: "cachedb_dynamodb Module"
description: "This module is an implementation of a cachedb system designed to work with Amazon DynamoDB. It uses the AWS SDK library for C++ to connect to a DynamoDB instance. It leverages the Key-Value interface exported from the core. [https://aws.amazon.com/pm/dynamodb/](https://aws.amazon.com/pm/d..."
---

## Admin Guide


### Overview {#overview}


This module is an implementation of a cachedb system designed to work with 
		Amazon DynamoDB. It uses the AWS SDK library for C++ to connect to a DynamoDB instance.
		It leverages the Key-Value interface exported from the core. 
		[https://aws.amazon.com/pm/dynamodb/](https://aws.amazon.com/pm/dynamodb/)


#### Functionalities


- *set*  - sets a key in DynamoDB using the  *cachedb_store* function
- *get*  - queries a key from DynamoDB using the  *cachedb_fetch* function
- *remove*  - removes a key from DynamoDB using the 
				*cachedb_remove* function
- *get_counter*  - queries a key with a numerical value
				 from DynamoDB using the  *cachedb_counter_fetch* function
- *add*  - increments the value of a specific item with a given value
				 using the *cachedb_add* function
- *sub*  - decrements the value of a specific item with a given value
				using the  *cachedb_sub* function


The following are internally used by OpenSIPS:


- *map_get*
- *map_set*
- *map_remove*


#### Table Format and TTL Option


The tables used with DynamoDB must adhere to a specific format. 
			Below is an example of creating a table:


```c
aws dynamodb create-table \
--table-name TableName \
--attribute-definitions \
	AttributeName=KeyName,AttributeType=S \
--key-schema \
	AttributeName=KeyName,KeyType=HASH \
--provisioned-throughput \
	ReadCapacityUnits=5,WriteCapacityUnits=5 \
--table-class STANDARD
			
```


If you create the table using the above command, then you have to specify the key in the 
			cachedb_url: *modparam("cachedb_dynamodb", "cachedb_url", 
			"dynamodb://localhost:8000/TableName?key=KeyName;val=ValName")"*


For additional examples of how cachedb_url should be formatted, refer to the 
		[cachedb_url (string)](#param_cachedb_url) section.


To enable TTL (Time to Live) for the table, which can be used with operations like set,
			add, and subtract, you can update the table with the TTL option:


```c
aws dynamodb update-time-to-live --table-name TableName --time-to-live-specification
"Enabled=true, AttributeName=ttl"
			
```


For additional information about the table format and TTL options, follow these links:


[Creating a Table](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/getting-started-step-1.html)


[Time to Live (TTL)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/time-to-live-ttl-how-to.html)


### Advantages


- *scalable and fully managed NoSQL database service provided by AWS*
- *integrated with other AWS services, providing robust security 
				and scalability features*
- *high availability and durability due to data replication across 
				multiple AWS Availability Zones*
- *serverless architecture, reducing operational overhead*
- *offers single-digit response times, with DynamoDB Accelerator (DAX) 
				for even lower latencies*


### Limitations


- *relies heavily on indexes; without them, querying involves costly full table scans*
- *does not support table joins, limiting complex queries involving multiple tables*
- *item size limit:each item has a size limit of 400KB, which cannot be increased.*


### Dependencies {#dependencies}


#### OpenSIPS Modules


There is no need to load any module before this module.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *AWS SDK for C++:*
By following these steps, you'll have the AWS SDK for C++ installed and 
				configured on your Linux system, allowing you to integrate with DynamoDB:
				[AWS SDK for C++ Installation Guide](https://docs.aws.amazon.com/sdk-for-cpp/v1/developer-guide/setup-linux.html)
Additional instructions for installation can be found at:
				[AWS SDK for C++ GitHub Repository](https://github.com/aws/aws-sdk-cpp)


#### Deploying DynamoDB locally on your computer


For testing purposes, you can run a DynamoDB locally. To achieve this, you should follow
		[these](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.DownloadingAndRunning.html) steps in order to deploy dynamodb locally.


Don't forget to always run the server using this command:
		
		`java -Djava.library.path=./DynamoDBLocal_lib -jar DynamoDBLocal.jar -sharedDb`
			in the directory where you extracted *DynamoDBLocal.jar*.


### Exported Parameters {#exported_parameters}


#### cachedb_url (string) {#param_cachedb_url}


The URLs of the server groups that OpenSIPS will connect to in order
			to use, from script, the cache_store(), cache_fetch(), etc. operations.
			It may be set more than once.  The prefix part of the URL will be
			the identifier that will be used from the script.


There are some default parameters that can appear in the URL:


- *region* - specifies the AWS region where the DynamoDB table is located
- *key* - specifies the table's Key column; default value is *"opensipskey"*
- *val* - specifies the table's Value column on which cache operations such as cache_store, cache_fetch, etc., will be performed;
				default value is *"opensipsval"*


Syntax for *cachedb_url*


- when using a previously created table (you have to specify the key and value):

  - host and port
*"dynamodb://id_host:id_port/tableName?key=key1;val=val1"*
  - region
*"dynamodb:///tableName?region=regionName;key=key2;val=val2"*
- when using the default key and value:

  - host and port
*"dynamodb://id_host:id_port/tableName"*
  - region
*"dynamodb:///tableName?region=regionName"*


```c title="Set cachedb_url parameter"
...

# single-instance URLs
modparam("cachedb_dynamodb", "cachedb_url", "dynamodb://localhost:8000/table1")
modparam("cachedb_dynamodb", "cachedb_url", "dynamodb:///table2?region=central-1")


# multi-instance URL (will perform circular 
```


```c title="Use Dynamodb servers"
...

cache_store("dynamodb", "call1", "10");
cache_store("dynamodb", "call2", "25", 150) // expires = 150s -optional
cache_fetch("dynamodb", "call1", $var(total));
cache_remove("dynamodb", "call1");


cache_store("dynamodb", "counter1", "200");
cache_sub("dynamodb", "counter1", 4, 1000); // expires = 1000s -mandatory parameter
cache_add("dynamodb", "call2", 5, 0) // -this update will not expire  -mandatory parameter
cache_remove("dynamodb", "counter1");

...
		
```


### Exported Functions {#exported_functions}


The module does not export functions to be used
		in configuration script.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Alexandra Titoc | 68 | 17 | 3573 | 1192 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 8 | 3 | 3 | 214 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2024 - May 2026 |
| 2. | Alexandra Titoc | Jul 2024 - Sep 2024 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Alexandra Titoc.


*Documentation Copyrights:*


Copyright © 2024 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
