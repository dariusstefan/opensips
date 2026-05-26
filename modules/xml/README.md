---
title: "XML Module"
description: "This module exposes a script variable that provides basic parsing and manipulation of XML documents or blocks of XML data. The variable provides ways to access entire XML elements, their text content or their attributes. You can modify the content and attributes as well as adding or removing node..."
---

## Admin Guide


### Overview


This module exposes a script variable that provides basic parsing and manipulation of XML documents or blocks of XML data. The variable provides ways to access entire XML elements, their text content or their attributes. You can modify the content and attributes as well as adding or removing nodes in the XML tree.


The processing does not take into account any DTDs or schemas in terms of validation.


### Dependencies


#### OpenSIPS Modules


This module does not depend on other modules.


#### External Libraries or Applications


- *libxml2* Most Linux and BSD distributions include libxml but the library can also be downloaded from: xmlsoft.org


### Exported Parameters


The module does not export any parameters.


### Exported Pseudo-Variables


#### `$xml(path)`


This module exports the *$xml(path)* variable.


##### Variable lifetime


The xml variables will be available to the
			process that created them from the moment they were
			initialized. They will not reset per message or per
			transaction. If you want to use them on a per message
			basis you should initialize them each time.


##### Accessing the $xml(path) variable


Accessing elements and attributes is based on the tree representation of the XML document thus a complete path from the root node is required. The in-memory equivalent of an XML document is an "XML object" which must be initilized with a well-formed block of XML data before use. In consequence, the path must start with the object name, followed by any number of nodes leading to the desired element.


The grammar that describes the path is:


path = name | name(identifier)+(acces)?


identifier = element(index)?


element = /string | /$var


index = [integer] | [$var]


access = .val | .attr/string | .attr/$var


In order to select between nodes with identical names on a certain level in the tree, an index can be provided, starting from 0.


The sequence of nodes in the path can be followed by *.val* in order to access the last node's text content or by *.attr/attr_name* in order to access it's attribute named *attr_name*. Otherwise the entire element (start-tag, end-tag, children elements and content) is accessed.


Assiging NULL to the variable removes the entire element or it's text content or attribute acording to the access mode.


If you want to insert an element, you must assign a string value (containg a well-formed block of XML data that has a root node) to the parent node. Note that assigning a value directly to a node does not replace it with that value.


IMPORTANT: In XML all characters in the content of the document are significant including blanks and formatting line breaks. An element and it's content will be returned WITH all the whitespaces and newlines and when adding a new node under an existing one, if you want to insert it with indentation, you must include the needed characters in the assigned string.


Other script variables can be used as element names, attribute names and indexes in the path. Variables that will be used as indexes must contain integer values. Variables that will be used as element or attribute names should contain string values.


**Example: Creating a document**


```opensips
...
$xml(my_doc) = "<doc></doc>";        # init object

$xml(my_doc/doc) = "<list></list>";  # add a "list" node

$xml(my_doc/doc/list) = "<item>some_value</item>";    # add an "item" node to the list

$xml(my_doc/doc/list) = "<item>another_value</item>"; # add another item to the list

$xml(my_doc/doc/list/item[1].val) = "new_val";        # set text content of previous item

$xml(my_doc/doc/list.attr/sort) = "asc";              # add attribute "sort" to list node

$xml(my_doc/doc/list.attr/sort) = NULL;               # remove previous attribute

$xml(my_doc/doc/list/item[1]) = NULL;                 # remove second item

$xml(my_doc/doc/list.val) = "end";                    # add text content to list which now has
                                                      # mixed content

$xml(my_doc/doc/list.val) = NULL;                     # remove the text content

xlog("$xml(my_doc/doc/list)\n");                      # display the entire list

xlog("$xml(my_doc)\n");                               # display the entire document

$xml(my_doc) = NULL;                                  # clear the entire document
...
				
```


**Example: Inserting nodes with indentation**


```
...
$xml(my_doc) = "<doc>\n</doc>";
$xml(my_doc/doc) = "\t<list></list>\n";
$xml(my_doc/doc/list) = "\n\t\t<item></item>\n\t";

# this creates the following document:
# <doc>
#	<list>
#		<item></item>
#	</list>
# </doc>
#
# without the explicit formating characters the document would be:
# <doc><list><item></item></list></doc>
...
				
```


**Example: Using script variables in path**


```opensips
...
# accessing the attribute of second item in list
$var(my_list) = "list";
$var(my_idx) = 1;
$var(my_attr) = "sort";
xlog("$xml(my_doc/doc/$var(my_list)/item[$var(my_idx)].attr/$var(my_attr))\n");
...
				
```


### Exported Functions


The module does not export any script functions.


## Contributors


### By Commit Statistics


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 28 | 14 | 1367 | 81 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 8 | 6 | 22 | 33 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 8 | 6 | 11 | 4 |
| 4. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 6 | 4 | 6 | 6 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 3 | 1 | 2 | 1 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Jan 2021 - Nov 2023 |
| 2. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | Feb 2017 - Jul 2022 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Mar 2017 - Sep 2019 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Apr 2019 - Apr 2019 |
| 5. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 6. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2017 - Jun 2018 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation


### Contributors


**Last edited by:** Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)).


*Documentation Copyrights:*


Copyright © 2017 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
