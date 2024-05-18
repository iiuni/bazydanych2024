# Database Project

**Please report any ambiguities, issues, or bugs. I reserve the right to make corrections to the content until Thursday, May 23, inclusive.**

### Research Quality Evaluation System

Scientists write scientific papers published at conferences.

Each conference is assigned a number of points awarded for each paper published at it: 200, 140, 100, 70, or 20.
The points for a given conference can change over time (but not more than once a day).
Papers have a publication date, title, list of authors, and conference, and each author of a given paper is associated with a research unit (affiliation). In each published paper of a given author, the affiliation can be different. We assume that if the author has a different affiliation in the paper than in any previous work, there was a change of affiliation - effective from the publication date of the new paper.
A research unit gets points for each paper published by people affiliated with that unit. Points are awarded as follows:
1. For papers worth 200, 140, and 100 points - the unit receives the full points for the paper, even if the paper also has co-authors from outside the unit.
2. For papers worth 70 points - *70\*sqrt(k/m)*, where *k* is the number of authors from the given unit, *m* is the total number of authors; but not less than 7 points, rounded to 4 decimal places.
3. For papers worth 20 points - *20\*k/m* (*k* and *m* as defined above), but not less than 2 points, rounded to 4 decimal places.

Of course, for each paper, the points for the conference on the day of publication and the author's unit stated in the paper are taken into account.

Your task is to implement the following API:
1. Adding papers (which may mean changing the affiliation for a given author),
2. Changing conference points (be prepared for frequent changes in these points),
3. Calculating a list of units with total points for publications in a given date range (sorted in descending order by the number of points),
4. Calculating a list of authors with assigned total points for publications in a given date range (considering full points for each conference),
5. Calculating the detailed achievements of a given author.

The technical specification for each operation is given below.

The data will not be inconsistent (in particular, there will not be two papers by the same author on the same day with different affiliations), but it may happen that papers are not added in chronological order.
Functions are always called with correct parameters, of the appropriate types, as per the specification below.

*Note: These are significantly simplified evaluation rules, interested parties can refer to the relevant [regulation](https://isap.sejm.gov.pl/isap.nsf/download.xsp/WDU20220000661/O/D20220661.pdf).*

### What will be evaluated?

**Conceptual model** - should consist of an E-R diagram, and comments containing constraints omitted in the diagram.

**Physical model** - should be an SQL file readable (and evaluable) by a human. It should contain definitions of all necessary database users and their permissions, tables, constraints, indexes, keys, referential actions, functions, views, and triggers. It is not necessary to use all these features, but where appropriate, they should be used.

**Project documentation** - should consist of a single PDF file containing the final conceptual model and detailed instructions on how to run the application. The documentation should cover what is implemented; specifically, you cannot submit documentation without the project.

**Program** - the source code and correctness and efficiency of the operation will be evaluated.

Submit an archive containing all the program source files, documentation in a PDF file, the physical model in an SQL file, and a make command (or run.sh script, etc.) enabling the compilation and running of the system.
Projects should be prepared individually.

### Technologies

Projects will be tested on a Linux system (Ubuntu 22.04.4 LTS). The recommended programming language is Python 3. Choosing another language requires approval from the lab supervisor. Your program, upon running, will be able to connect to an already running empty PostgreSQL database (version >=13, the exact version will be provided later).

Your program, upon running, should read a sequence of API function calls from standard input and print the results of their operation to standard output.

All data should be stored in the database, and the effect of each function modifying the database, for which an execution confirmation (OK value) was printed, should be preserved. The program can be run multiple times, e.g., first reading a file with initial data, and then subsequent correctness tests. On the first run, the program should create all necessary database elements (tables, constraints, functions, triggers) according to the physical model prepared by the student. The database will not be modified between successive runs. The program will not have permissions to read, create, or write any files except for reading files from the current directory (e.g., the contents of the SQL file with the physical schema).

### Input File Format

Each line of the input file contains a JSON object (http://www.json.org/json-pl.html). Each object describes a call to one API function with arguments.

Example: the object `{ "function": { "arg1": "val1", "arg2": "val2" } }` means calling the function named `function` with argument `arg1` having value `val1` and `arg2` having value `val2`.

The first line of input contains the call to the `open` function with arguments enabling connection to the database.

**Sample Input**
```
{ "open": { "host": "localhost", "database": "student", "login": "student", "password": "qwerty"}}
{ "function1": { "arg1": "value1", "arg2": "value2" } }
{ "function2": { "arg1": "value4", "arg2": "value3", "arg3": "value5" } }
{ "function3": { "arg1": "value6" } }
{ "function4": { } }
```



**Output Format**

For each call, print a separate line containing a JSON object with the execution status OK/ERROR/NOT IMPLEMENTED and the data returned by the function as per its specification.

Output data format (for readability contains prohibited newline characters):

```
{ "status":"OK",
"data": [ { "atr1":"v1",
"atr2":"v2" },
{ "atr1":"v3",
"atr2":"v3" } ]
}
```


The `data` table contains all the resulting tuples. Each tuple contains values for all attributes.

**Sample Output**

```
{ "status": "OK" }
{ "status": "NOT IMPLEMENTED" }
{ "status": "OK", "data": [ { "a1": "v1", "a2": "v2"}, { "a1": "v3", "a2": "v3"} ] }
{ "status": "OK", "data": [ ] }
{ "status": "ERROR" }
```


### API Description Format

```
<function> <arg1> <arg2> … <argn> // function name and argument names

// description of function operation
// description of the result format: list of result table attributes
```


You must submit a self-written program implementing all functions, documentation (including the conceptual model), and the physical model.

### Establishing Connection and Defining New Users

```
open <host> <database> <login> <password>
// passes data enabling your program to connect to the database
// - host name and database name, login, and password,
// called exactly once, on the first line of input
//
// returns status OK/ERROR depending on whether
// the connection to the database was successful

newuser <secret> <newlogin> <newpassword>
// creates a user <newlogin> with password <newpassword>,
// the <secret> argument must be equal to d8512346fbc5bb7a325ca4
//
// returns status OK/ERROR

deluser <secret> <login>
// deletes user <login>
// the <secret> argument must be equal to d8512346fbc5bb7a325ca4
//
// returns status OK/ERROR

```


### Database Modifying Operations

Each of the following calls should return a JSON object containing only the execution status: OK/ERROR/NOT IMPLEMENTED.
Calls with incorrect `<login> <password>` values must return status ERROR and not execute.
If you have not implemented a function (not recommended), its call must return status NOT IMPLEMENTED.

1. Adding papers (JSON: date/title/conference/authors with affiliations) - may mean changing the affiliation for a given author

```
article <login> <password> <date> <title> <conference> <author list>
// <date> - publication date
// <title> <conference> pairs are unique, <title> is the paper title
// <conference> uniquely identifies the conference,
// <author list> is an array of objects in the form:
// [ { <author>, <institution>},
// { <author>, <institution>},
// ]
// <author> uniquely identifies the author,
// <institution> uniquely identifies the institution
```

2. Changing conference points (JSON: conference, date, new number of points) - be prepared for frequent changes in these points :)

```
points <login> <password> <date> <points_list>
// <date> - the date from which the change is effective
// <points_list> is an array of objects in the form:
// [ { <conference>, <points> },
```


### Other Operations

Each of the following calls should return a JSON object containing the execution status OK/ERROR and a data table containing attribute value tuples according to the specifications below.

1. List of institutions with assigned points for publications within a given date range (sorted in descending order by the number of points)

```
institution <start_date> <end_date>

// returns a list of institutions along with their points for publications
// within the given date range (sorted in descending order by the number of points)
//
// Attributes of returned tuples:
// <institution> <number of points>
```

2. List of authors with assigned total points for publications within a given date range (in the form of the sum of the full number of points assigned to each conference at which the person published a paper)

```
author <start_date> <end_date>
// returns a list of authors along with their points for publications
// within the given date range (sorted in descending order by the number of points)
//
// Attributes of returned tuples:
// <author> <number of points>
```


3. Detailed achievements of a given author

```
author_details <author>
// returns a list of publications of the given author
// (grouped by conference and sorted in descending order
// by the number of points currently assigned to the conference,
// and within the group alphabetically by titles)

// Attributes of returned tuples:
// <conference> <year> <number of points> <institution> <title>
// where
// <conference> uniquely identifies the conference
// <year> the year the paper was published
// <number of points> the number of points assigned to the conference at the time of publication
// <institution> the author's affiliation at the time of publication
// <title> the title of the paper
```