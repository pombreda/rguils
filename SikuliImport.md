﻿#summary RGUILS package to import symbols from Sikuli projects
#labels Documentation

# Importing images from Sikuli projects #

The sikuliimport package provides a mechanism to import symbols, especially image constants, from Sikuli projects into python modules.

A module that wants to import symbols from a Sikuli project can do so by importing them from the sikuliimport.projects module:
```
from sikuliimport.projects import *
```
The sikuliimport.projects module imports symbols from one or more Sikuli projects that are listed in a configuration file. In addition, any string that appears to be a Sikuli image filename is replaced with its absolute filename. For example, given the following constant in the foo.sikuli project:
```
FOO_CONST = '1270078045794.png'
```
`FOO_CONST` is imported from sikuliimport.projects as
```
FOO_CONST = r'C:\Documents and Settings\username\Sikuli\foo.sikuli\1270078045794.png'
```
The image filename expansion goes recursively through the basic python data structures (i.e. lists, tuples, dictionaries, sets, frozensets).

# Configuration #

You specify which Sikuli projects are imported by listing them in sikuliimport/settings.py, which is imported by sikuliimport.projects. For example, to import symbols from two Sikuli projects, foo.sikuli and bar.sikuli, you put the following in sikuliimport/settings.py:
```
SIKULI_PROJECT_DIRS = [r'C:\Documents and Settings\username\Sikuli\foo.sikuli',
                       r'C:\Documents and Settings\username\Sikuli\bar.sikuli']
```
If sikuliimport/settings.py does not exist, sikuliimport.projects reverts to sikuliimport/defaultsettings.py.