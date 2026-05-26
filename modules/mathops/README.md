---
title: "mathops Module"
description: "The mathops module provides a series of functions which enable various floating point operations at OpenSIPS script level."
---

## Admin Guide


### Overview {#overview}


The mathops module provides a series of functions which enable various
		floating point operations at OpenSIPS script level.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *No dependencies on other OpenSIPS modules.*.


#### External Libraries or Applications


The following libraries or applications must be installed before 
		running OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### decimal_digits (integer) {#param_decimal_digits}


The precision of the results returned by all the module functions.
		The higher the "decimal_digits" value, the more decimal
		digits the results will have.


Default value is "6".


**Example: Setting the decimal_digits module parameter**


```opensips
modparam("mathops", "decimal_digits", 10)
```


### Exported Functions {#exported_functions}


#### math_eval(expression, result_var) {#func_math_eval}


The function evaluates a given expression and writes the result in the
		output pseudo-variable. Evaluation uses tinyexpr (see https://github.com/codeplea/tinyexpr).


Currently allowed syntax for specifying an expression:


- Nested parentheses
- addition (+), subtraction/negation (-), multiplication (*), division (/), exponentiation (^) and modulus (%) with the normal operator precedence (the one exception being that exponentiation is evaluated left-to-right)
- C math functions: abs (calls to fabs), acos, asin, atan, ceil, cos, cosh, exp, floor, ln (calls to log), log (calls to log10), sin, sinh, sqrt, tan, tanh


Meaning of the parameters is as follows:


- *expression* (string) - a mathematical expression.
- *result_var* (var) - variable which will
			hold the result of the evaluation.


This function can be used from any route.


**Example: math_eval usage**


```opensips
...
# Compute some random math expression

$avp(1) = "3.141592";
$avp(2) = "2.71828";
$avp(3) = "123.45678";

if (math_eval("$avp(1) * ($avp(3) - ($avp(1) - $avp(2))) / $avp(3)", $avp(result))) {
	xlog("Result of expression: $avp(result)\n");
} else {
	xlog("Math eval failed!\n");
}

...
```


#### math_rpn(expression, result_var) {#func_math_rpn}


The function evaluates a given RPN expression and writes the result in the
		output variable.


The expression is specified in Reverse Polish Notation. Values are pushed
    onto a stack, while operations are executed on that stack. The following operations
    are supported:


- binary operators: + - / * mod pow
- unary functions: neg exp ln log10 abs sqrt cbrt floor ceil round nearbyint trunc
neg will change the sign of the top of the stack
ln is natural logarithm; abs is absolute value; other functions are standard C functions
- constants: e pi
- stack manipulations commands: drop dup swap


Meaning of the parameters is as follows:


- *expression* (string) - a RPN expression.
- *result_var* (var) - variable which will
			hold the result of the evaluation.


This function can be used from any route.


**Example: math_rpn usage**


```opensips
$avp(1) = "3";

if (math_rpn("1 $avp(1) swap swap dup drop / exp ln 1 swap /", $avp(result))) {
	xlog("Result of expression: $avp(result)\n");
} else {
	xlog("RPN eval failed!\n");
}

/* This example RPN script will push 1 then 3 onto the stack, then do a couple no-ops
(exchange the two values twice, duplicate one of them then drop the duplicate),
compute the division of 1 by 3, then do another no-op (exponentiation then logarithm), and
finally compute 1 divided by the result, giving 3 as the result. */
```


#### math_trunc(number, result_var) {#func_math_trunc}


Truncation of a number towards zero. This means that trunc(3.7) = 3.0 and
		trunc(-2.9) = -2.0.


Meaning of the parameters is as follows:


- *number* (string) - Number to be truncated.
- *result_var* (var) - variable which will
			hold the result of the evaluation.


This function can be used from any route.


**Example: math_trunc usage**


```opensips
...
# Truncate a random number

$avp(1) = "3.141492";

if (math_trunc($avp(1), $avp(result))) {
	xlog("Truncate result: $avp(result)\n");
} else {
	xlog("Truncate failed!\n");
}
...
```


#### math_floor(number, result_var) {#func_math_floor}


Truncates a number, always towards -infinity. This means that floor(3.7) = 3.0
		and floor(-2.9) = -3.0


Meaning of the parameters is as follows:


- *number* (string) - Number to be truncated.
- *result_var* (var) - variable which will
			hold the result of the evaluation.


This function can be used from any route.


**Example: math_floor usage**


```opensips
...
# Truncate a random number

$avp(1) = "3.141492";

if (math_floor($avp(1), $avp(result))) {
	xlog("Floor result: $avp(result)\n");
} else {
	xlog("Floor operation failed!\n");
}
...
```


#### math_ceil(number, result_var) {#func_math_ceil}


Truncates a number, always towards +infinity. This means that ceil(3.2) = 4.0
		and ceil(-2.9) = -2.0


Meaning of the parameters is as follows:


- *number* (string) - Number to be truncated.
- *result_var* (var) - variable which will
			hold the result of the evaluation.


This function can be used from any route.


**Example: math_ceil usage**


```opensips
...
# Truncate a random number

$avp(1) = "3.141492";

if (math_ceil($avp(1), $avp(result))) {
	xlog("Ceil result: $avp(result)\n");
} else {
	xlog("Ceil operation failed!\n");
}
...
```


#### math_round(number, result_var[, decimals]) {#func_math_round}


The round function returns the nearest integer, and tie-breaking is done away
		from zero. Examples: round(1.2) = 1.0, round(0.5) = 1.0, round(-0.5) = -1.0


By default, the function returns an integer. An additional parameter controls
		the number of decimal digits of the initial number which will be kept. The
		rounding will then be done using the remaining decimal digits, and the result
		will be a float value, represented as a string.


Meaning of the parameters is as follows:


- *number* (string) - Number to be rounded.
- *result_var* - variable which will
			hold the result of the evaluation.
- *decimals* (int, optional) -
			further improves the precision of the rounding.


This function can be used from any route.


**Example: math_round usage**


```opensips
...
# Rounding PI

$avp(1) = "3.141492";

if (math_round($avp(1), $avp(result))) {

	# result should be: 3
	xlog("Round result: $avp(result)\n");
} else {
	xlog("Round operation failed!\n");
}

...

if (math_round($avp(1), $avp(result), 4)) {

	# result should be: "3.1415"
	xlog("Round result: $avp(result)\n");
} else {
	xlog("Round operation failed!\n");
}
...
```


#### math_round_sf(number, result_var, figures) {#func_math_round_sf}


To give a simple explanation, rounding to N significant figures is done by 
		first obtaining the number resulted from keeping N significant figures
		(0 padded if necessary), then adjusting it if the N+1'th digit is greater
		or equal to 5.


Some examples:


- round_sf(17892.987, 1) = 20000
round_sf(17892.987, 2) = 18000
round_sf(17892.987, 3) = 17900
round_sf(17892.987, 4) = 17890
round_sf(17892.987, 5) = 17893
round_sf(17892.987, 6) = 17893.0
round_sf(17892.987, 7) = 17892.99


Meaning of the parameters is as follows:


- *number* (string) - Number to be rounded.
- *result_var* (var) - variable which will
			hold the result of the evaluation.
- *figures* -
			further improves the precision of the rounding.


This function can be used from any route.


**Example: math_round_sf usage**


```opensips
...
# Rounding PI

$avp(1) = "3.141492";

if (math_round_sf($avp(1), $avp(result), 4)) {

	# result should be: "3.141"
	xlog("Round result: $avp(result)\n");
} else {
	xlog("Round operation failed!\n");
}

...
```


#### math_compare(exp1, exp2, result_var) {#func_math_compare}


Compare exp1 with exp2 and returns the comparison result in the result_var.
		Standard comparison return codes used : If exp1 > exp2, result_var = 1.
		Else if exp2 > exp1, result_var = -1, else (in case they are equal), 
		0 is populated in the result_var


Meaning of the parameters is as follows:


- *exp1* (string) - First expression to be evaluated and used for comparison.
- *exp2* (string) - Second expression to be evaluated and used for comparison.
- *result_var* (var) - variable which will
			hold the result of the comparison.


This function can be used from any route.


**Example: math_compare usage**


```
...
# Rounding PI

$var(exp1) = "1 + 8";
$var(exp2) = "7/2";

if (math_compare($var(exp1), $var(exp2), $var(result))) {

	# $var(result) will be 1, since 9 > 3.5
}

...
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 26 | 12 | 1355 | 66 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 10 | 8 | 39 | 44 |
| 3. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 9 | 3 | 96 | 302 |
| 4. | Ryan Bullock ([@rrb3942](https://github.com/rrb3942)) | 9 | 1 | 552 | 160 |
| 5. | Stephane Alnet | 6 | 2 | 327 | 36 |
| 6. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 5 | 3 | 3 | 3 |
| 7. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 4 | 2 | 3 | 1 |
| 8. | Julián Moreno Patiño | 3 | 1 | 3 | 3 |
| 9. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |
| 10. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 2 | 1 | 88 | 0 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Jan 2021 - Feb 2023 |
| 2. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Jan 2022 - Jan 2022 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - Oct 2019 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Feb 2013 - Jun 2019 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 6. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Oct 2014 - Apr 2019 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 8. | Ryan Bullock ([@rrb3942](https://github.com/rrb3942)) | Feb 2016 - Feb 2016 |
| 9. | Julián Moreno Patiño | Feb 2016 - Feb 2016 |
| 10. | Stephane Alnet | Nov 2013 - Nov 2013 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Ryan Bullock ([@rrb3942](https://github.com/rrb3942)), Julián Moreno Patiño, Stephane Alnet.


*Documentation Copyrights:*


Copyright © 2013 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
