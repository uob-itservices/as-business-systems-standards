# Formatting PL/SQL and SQL Code

We apply some general standards to PL/SQL and SQL code:

* No tabs and 4 spaces per indent. 

* No trailing whitespace.

* Always capitalize keywords (e.g., `SELECT` or `AS`)

* Variables should be in lowercase and underscore separated, with global constant variables capitalised ( see section on Variables and Naming Conventions ). 

**These can all be applied using the SQL developer code formatting tool.**

Your code must be run through the SQL Developer code formatting tool prior to code review. The simplest way to run this is to open your sql script in SQL Developer and press **CTRL + F7**.

A copy of SQL Formatter settings used within the team can be downloaded here: [SQL Developer Formatting Configuration File](sqldeveloper_formatter_settings.xml)

## Unix Line Endings

**All files must use UNIX line endings.**

SQL Developer can **default to using UNIX line endings** for new files by setting *Window -> Preferences -> Environment -> Line Terminator to Line Feed (Unix/Mac)*. Note that this setting will not retrospectively change line endings for existing files and the formatter tool will not update line endings. 

Notepad++ also includes functionality to quickly switch the line endings used by a file. Open your file in Notepad++ and click on *Edit -> EOL Conversion -> Unix (LF)* to switch to Unix line endings. 

The use of a .gitattributes file in your repository can ensure that line endings are always translated to UNIX format on check in of code. Simply create a new text file called **.gitattributes** in the root directory of your repository and add the following line:

```
* text eol=lf
```