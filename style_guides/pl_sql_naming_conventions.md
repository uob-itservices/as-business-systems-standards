# Variable Naming Conventions

## Variable Names

**If you are working in an existing procedure or function please follow the format that it already uses.** For new objects follow these naming conventions.

These variable naming prefixes are based on those at [Oracle-Base: Naming Conventions](https://oracle-base.com/articles/misc/naming-conventions).

They apply across all database objects and cursors.

Variable naming prefixes:

  ```
  Package Global Variables : g_variable_name
  Local Variables          : l_variable_name
  Types                    : t_type_name
  Cursors                  : c_cursor_name
  Exceptions               : e_exception_name
  Parameters               : p_parameter_name
  ```

Constant variables should be named in CAPS, but otherwise use the appropriate prefix. For example:

```
Global Constant Variable  : g_VARIABLE_NAME
Local Constant Variable   : l_VARIABLE_NAME
```
Aside from constant values, variables should be underscore separated and lower case. Some examples:

```
l_calculated_mean
c_get_level_m_marks
p_pidm
p_term_code
```

## General Variable Naming Guidelines

1. Never use names with a leading numeric character.
3. Always choose meaningful and specific names.
4. Avoid using abbreviations unless the full name is excessively long.
5. Avoid long abbreviations. Abbreviations should be shorter than 5 characters.
6. Any abbreviations used must be widely known and accepted.
7. Never use keywords as names. A list of keywords may be found in the dictionary view v$reserved_words.
8. Avoid adding redundant or meaningless prefixes and suffixes to identifiers. Example: create table emp_table.
9. Always use the same names for elements with the same meaning.