#summary Installing and using RGUILS
#labels Featured

# Introduction #

RGUILS (<b>R</b>obust <b>GUI</b> automation <b>L</b>ibrary for <b>S</b>ikuli, pronounced as _rogels_ or _ragils_) is a python library that provides an object-oriented API to interact with common GUI elements, such as buttons, checkboxes, windows and dialogue hierarchies. RGUILS uses [Sikuli Script](http://sikuli.org/) to interact with GUIs. RGUILS has been designed to make GUI automation and testing easier and more robust.

To learn more about Sikuli, read the SikuliOverview. To learn how to use RGUILS, read the SampleInstaller tutorial. For a more in-depth discussion of GUI automation issues, read the article about [RobustGUIAutomation](RobustGUIAutomation.md).

# Installation #

The following instructions are for Windows:
  1. Download and install Sikuli from http://sikuli.org/
  1. Create a folder for Sikuli projects. RGUILS assumes that this folder is called _Sikuli_ and is located in your home directory (C:\Documents and Settings\_{username}_\Sikuli in Windows XP, C:\Users\_{username}_\Sikuli in Windows Vista and 7).
  1. Check out RGUILS and put the contents of the src/python folder (seagull, sikuliimport) into your Sikuli projects folder.
  1. Copy the following files from the resources folder to the Sikuli IDE installation folder (C:\Program Files\Sikuli): sikuli-script.bat, sikuli-shell.bat, userlib.py
  1. If your Sikuli projects folder is not in the default location (see above), edit userlib.py in the Sikuli IDE installation folder and set the USERLIBDIR variable to point to your Sikuli projects folder.

# Running #

The sikuli-script.bat Windows batch file can be used to run a Sikuli script file from the command line. Simply open a command prompt, cd into your Sikuli projects folder and type the command:
```
C:\Documents and Settings\username\Sikuli>"\Program Files\Sikuli\sikuli-script.bat" foo.sikuli
```
For more information see the SikuliOverview.

# List of RGUILS modules #

  * [Buttons](Buttons.md): classes to model sets of buttons
  * [Checkboxes](Checkboxes.md): classes to model sets of checkboxes and radio buttons
  * DialogueWindow: classes to model dialogue window hierarchies
  * [Images](Images.md): creates dictionary objects with button images imported from Sikuli projects
  * OverlayWindow: classes to display overlay windows for debugging
  * [Util](Util.md): utility functions and classes
  * [Window](Window.md): an object-oriented API to interact with application windows
  * WindowFlavor: OS specific window properties
  * [WindowsXP](WindowsXP.md): definitions for Windows XP
  * WindowsVista: definitions for Windows Vista
  * [Windows7](Windows7.md): definitions for Windows 7
  * WindowsUtil: utility functions for Windows operating systems
  * SikuliImport: import images and other symbols from Sikuli projects