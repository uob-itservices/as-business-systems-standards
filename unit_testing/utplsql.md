# UTPLSQL Unit Testing Framework

* [Unit Testing Approach](#1)
* [Creating Unit Tests](#2)
	* [Test Specification & Annotations](#21)
	* [Test Definition & Logic](#22)
* [Naming Conventions & Style Guide](#3)
* [Running Unit Tests](#4)


utPLSQL is a unit testing framework for Oracle PL/SQL. It allows us to create package based unit tests, which can be run by developers and automated by the build process in Team City.

It has been installed on our development Banner database, **DSTU**

**Comprehensive documentation and tutorials are available at: [http://utplsql.org/utPLSQL/latest/](http://utplsql.org/utPLSQL/latest/)**

<a name="1"></a>
## Unit Testing Approach

Our approach to unit testing PL/SQL is as follows:

* Wherever possible unit test should be created for all new functionality that we add to the database. 
* Unit tests should be created and run in the DSTU development database and should not be promoted to the UAT or live environments. 
* Running unit tests should be included as part of internal reviews and all pre-existing tests should be run for regression testing whenever a change is made. 
* Unit tests should be stored in GIT alongside the source code for the development they are associated with. 

At an individual test level:

* Tests should not need to be run in a specific order.
* Tests should not depend on other tests to execute.
* Tests should not depend on a specific database state and they should setup the expected state before being run.
* Tests should calculate any data required to run the test and should not require any code changes to run.
* Every test needs to be built so that it _can_ fail.
* Tests should keep the environment unchanged post execution. _utPLSQL creates a save point and rolls back any database changes once tests have completed by default, but care must be taken if code that is being tested contains COMMIT statements._

## utPLSQL Overview

<a name="2"></a>
### Creating Unit Tests

At a high level utplsql requires you to create database packages to contain the tests that need to be run. These packages need to be created in the **BANDEV** schema. 

Unit tests are organised into **suites**, which can contain one or more individual  **tests**. For our purposes, each **suite** should equate to a single package.  

<a name="21"></a>
#### Test Specification & Annotations

These are identified in the package body using annotations similar to those used in jUnit. The annotations include a description of the suite/test, which is displayed when the test is run.  For example:

    CREATE OR REPLACE PACKAGE BANDEV.PKW_TEST_SWBDIVD
    AS
    /*
    ---------------------------------------------------------------------------------
    Change History
    Version 	Date		Change  		Initials
    1.0 		03/10/18	Initial Version AH
    ---------------------------------------------------------------------------------
    */
    
    --%suite(Test SWBDIVD View)
    
    --%test(Test SWBDIVD View)
    PROCEDURE PW_TEST_SWBDIVD;
    
    --%test(Test SWBDIVD INSERT via SKVSSDT)
    PROCEDURE PW_TEST_INSERT_SWBDIVD;
    
    --%test(Test FUNCTION fw_get_div)
    PROCEDURE PW_TEST_DIV_CODE;
    
    --%test(Test VIEW BANDEV.AS_BHAM_DEPARTMENT)
    PROCEDURE PW_TEST_AS_BHAM_DEPARTMENT;

    FUNCTION FW_GET_RANDOM_SWBDIVD_DIV_CODE
        RETURN VARCHAR;


Annotations are also used to indicate when procedures should be run, for example to create and tear down data in preparation for running a test. Some examples are:

* ```%beforeall``` -  Denotes that the annotated procedure should be executed once before all elements of the suite
* ```%afterall``` -  	Denotes that the annotated procedure should be executed once after all elements of the suite.
* ```%beforeeach``` - Denotes that the annotated procedure should be executed before each %test procedure in the suite.
* ```%aftereach``` 	- Denotes that the annotated procedure should be executed after each %test procedure in the suite.
* ```%beforetest(<procedure_name>)``` - Denotes that mentioned procedure should be executed before the annotated %test procedure.
* ```%aftertest(<procedure_name>)``` - Denotes that mentioned procedure should be executed after the annotated %test procedure.
* ```%rollback(<type>)``` - Defines transaction control. Supported values: auto(default) - A savepoint is created before invocation of each “before block” is and a rollback to specific savepoint is issued after each “after” block; manual - rollback is never issued automatically. Property can be overridden for child element (test in suite)
* ```%disabled``` - Used to disable a suite or a test. Disabled suites/tests do not get executed, they are however marked and reported as disabled in a test run.

**All annotations available are documented at [http://utplsql.org/utPLSQL/v3.0.0/userguide/annotations.html](http://utplsql.org/utPLSQL/v3.0.0/userguide/annotations.html)**

<a name="22"></a>
#### Test Definition & Logic

The package body will contain the unit test logic along with any helper functions required to provide functions, procedures, etc... with appropriate input data. 

The procedure that runs the test will call a utplsql procedure to indicate  which package function will run the test and what the expected output of the test should be. 

For example, the below procedure PW\_TEST\_DIV\_CODE will use  ```ut.expect```  to call the function FW\_RUN\_DIV\_CODE and expect a return value of TRUE in order for the test to pass. 

**There are a number of evaluation types available in utPLSQL, which are documented here: [http://utplsql.org/utPLSQL/latest/userguide/expectations.html](http://utplsql.org/utPLSQL/latest/userguide/expectations.html)**

	
	/* Package of tests for Banner 9 - SWBDIVD */
	CREATE OR REPLACE PACKAGE BODY BANDEV.PKW_TEST_SWBDIVD
	AS
	    /*
	    ---------------------------------------------------------------------------------
	    Change History
	    Version     Date        Change                                      Initials
	    1.0         03/10/18    Initial Version                             AH
	    ---------------------------------------------------------------------------------
	    */
	
	
	    PROCEDURE PW_TEST_DIV_CODE
	    IS
	    BEGIN
	        ut.expect (FW_RUN_DIV_CODE).to_equal (TRUE);
	    END;


In this case, the test itself is a FUNCTION returning a boolean value. Depending on the evaluation being performed, this could also be a number or varchar value. 

```FW_RUN_DIV_CODE``` tests that the function ```FW_DIV_CODE``` outputs a value and calls it using a random SWBDIVD\_DPT\_CODE provided by a helper function. 

This allows the unit test to **encapsulate all functionality required to run the test** and **tests using different data each time the function is run**. 


    FUNCTION FW_RUN_DIV_CODE
        --Test function BANDEV.FW_DIV_CODE
        RETURN BOOLEAN
    IS
        v_swbdivd_dept_code   swbdivd.swbdivd_dept_code%TYPE
                                  := fw_get_random_swbdivd_dpt_code ();
        v_swbdivd_div_code    swbdivd.swbdivd_div_code%TYPE;
    BEGIN
        DBMS_OUTPUT.PUT_LINE (
            'Random swbdivd_dept_code generated = ' || v_swbdivd_dept_code);

        IF v_swbdivd_dept_code IS NULL
        THEN
            RAISE NO_DATA_FOUND;
        END IF;

        -- function bandev.fw_div_code (
        --      p_dept IN swbdivd.swbdivd_dept_code%TYPE )
        -- RETURN VARCHAR2 ;
        v_swbdivd_div_code := bandev.fw_div_code (v_swbdivd_dept_code);

        DBMS_OUTPUT.PUT_LINE (
               'swbdivd_div_code returned from bandev.fw_div_code = '
            || v_swbdivd_div_code);

        IF v_swbdivd_div_code IS NULL
        THEN
            RAISE NO_DATA_FOUND;
        END IF;

        RETURN TRUE;
    EXCEPTION
        WHEN NO_DATA_FOUND
        THEN
            RETURN FALSE;
    END FW_RUN_DIV_CODE;

    FUNCTION FW_GET_RANDOM_SWBDIVD_DPT_CODE
        RETURN VARCHAR
    IS
        v_swbdivd_dept_code   swbdivd.swbdivd_div_code%TYPE;
    BEGIN
        SELECT swbdivd_dept_code
          INTO v_swbdivd_dept_code
          FROM (  SELECT swbdivd_dept_code
                    FROM swbdivd
                ORDER BY DBMS_RANDOM.VALUE)
         WHERE ROWNUM = 1;

        IF v_swbdivd_dept_code IS NULL
        THEN
            RAISE NO_DATA_FOUND;
        ELSE
            RETURN v_swbdivd_dept_code;
        END IF;
    END;

<a name="3"></a>
### Naming Conventions & Style Guide

PL/SQL code created for unit testing should follow our [PL/SQL style guidance](../style_guides/pl/sql.md).

Unit test packages should always be prefixed with **PKW\_TEST**

	CREATE OR REPLACE PACKAGE BODY BANDEV.PKW_TEST_SWBDIVD

Procedures used to call ```ut.expect()``` should always be prefixed with **PW\_TEST**

	PROCEDURE PW_TEST_AS_BHAM_DEPARTMENT

**Suite and package annotations should always contain a meaningful description** as this is displayed in the output when a test is run.

    --%suite(Test SWBDIVD View)

    --%test(Test FUNCTION fw_get_div)
    PROCEDURE PW_TEST_DIV_CODE;

### Running Unit Tests
<a name="4"></a>

Developers can run tests by calling the ut.run procedure in a pl/sql block. In the example below, the suite of tests in the package ```PKW_TEST_SWBDIVD``` will be run. 

If expanded to include a procedure name,  e.g. ```PKW_TEST_SWBDIVD.PW_TEST_DIV_CODE```, a single test can be run.

If the package name is ommitted, all tests defined in the database will be run. 
	
	BEGIN
		ut.run('PKW_TEST_SWBDIVD');
	END;

The output from utPLSQL will display in the **DBMS\_OUTPUT** window when run in Toad. It lists the tests run, the parameters used / results and will highlight any failed tests. 

Unit tests can include references to ```dbms_output.put_line```, which will be output alongside the test results. 


	Test SWBDIVD View
	Test SWBDIVD View [.004 sec]
	First row returned from SWBDIVD: swbdivd_div_code = 022, swbdivd_dept_code = 022, swbdivd_coll_code = 10, swbdivd_user = BANDEV, swbdivd_activity_date = 20-AUG-18
	Total rows returned from bandev.swbdivd:94
	Test SWBDIVD INSERT via SKVSSDT [.021 sec]
	Record INSERT into SWBDIVD via SKVSSDT: swbdivd_div_code = 00
	Record SELECT FROM SWBDIVD: swbdivd_div_code = 000
	Record DELETE FROM SWBDIVD: swbdivd_div_code = 000
	Test FUNCTION fw_get_div [.068 sec]
	Random swbdivd_dept_code generated = 222
	swbdivd_div_code returned from bandev.fw_div_code = 047
	Test VIEW BANDEV.AS_BHAM_DEPARTMENT [.028 sec]
	First row returned from bandev.as_bham_department: script_version_department = 1.0.1W, stvdept_code = 133, stvdept_desc = Modern History (DNU), stvdept_vr_msg_no = 11008, swvdept_long_desc = Modern History, division_code = , division_desc =
	Total rows returned from bandev.as_bham_department:196
	Finished in .124452 seconds
	4 tests, 0 failed, 0 errored, 0 disabled, 0 warning(s)








