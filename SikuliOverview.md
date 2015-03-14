﻿#summary An overview of Sikuli

# Introduction #

[Sikuli](http://sikuli.org/) is a tool to automate GUIs (graphical user interfaces). Sikuli uses images of GUI elements (e.g. buttons) and [open source computer vision algorithms](http://opencv.willowgarage.com/) to locate them on the screen in order to interact with them.

Sikuli consists of Sikuli Script, a Jython module with a scripting API, and the Sikuli IDE, an integrated development environment for visual scripting.

# How Sikuli works #

Sikuli determines the position of GUI elements, such as buttons and windows, on the screen by taking a screenshot and using fuzzy image matching algorithms to find the best matching occurrence of an image of the GUI element in the screen image. Once the position of the GUI element is known, Sikuli can create keyboard and mouse click events at that position to interact with the element.

To interact with a particular button or other GUI element, you take an image of the button or GUI element (the Sikuli IDE provides a convenient way to do that) and call a function or method in Sikuli Script, passing the image file name as a function argument. For example, a simple Sikuli script to click a button on the screen could look like this:
```
from sikuli.Sikuli import click

click('button.png')
```
When the script is run and the click method is called, Sikuli captures the screen and searches the screen image for an area that matches the button image. If a match is found, Sikuli generates a click event that is located at the center of the button. If no match is found, an Exception is thrown.

# Visual scripting with the Sikuli IDE #

The Sikuli IDE enables _visual scripting_ by displaying images inline in a script. The Sikuli IDE makes it particularly easy to take images of GUI elements (or of anything on the screen). When you click the camera icon in the IDE, the IDE captures the screen (after minimizing itself) and then presents a cursor to select a rectangle in the screenshot and saves the contents of the selected rectangle to a PNG file.

Sikuli script files are saved in project folders with the filename extension _.sikuli_, along with all the images contained in the script. Images taken with the IDE are saved with the timestamp of the image as the filename.

Scripts can be run in the Sikuli IDE by clicking the run button. However, since it reuses the same Python interpreter instance for all scripts, any changes that a script makes to the Python runtime environment (e.g. by importing modules) are persistent for the lifetime of the IDE process and are shared across all scripts. In particular, modules that are imported by a script are not reloaded when the script is run again, unless the IDE is shut down and restarted. This is a major obstacle when developing Sikuli modules. An alternative is to run scripts with Sikuli Script from the command line.

# Using Sikuli Script without the IDE #

Since Sikuli scripts are Python scripts, any text editor can be used to edit Sikuli scripts. As of now, editing capabilities in the Sikuli IDE are very basic so that an external editor may be more comfortable.

Sikuli scripts can be run from the command line using the sikuli-script.bat batch file (see [GettingStarted#Running](GettingStarted#Running.md)). Sikuli Script can only run a script if it is in a folder with the same basename as the script and with the extension _.sikuli_, for `example myproject.sikuli\myproject.py`. You cannot pass command line arguments to a script. Sikuli Script treats all arguments as project folder names.

You can import python modules (other than the Python standard library modules) into a Sikuli script by adding the folder that contains the module to your python path. If you install the `userlib.py` module, your Sikuli project folder and all project folders in it are automatically added to the python path (see [GettingStarted#Installation](GettingStarted#Installation.md)).

You can import a Sikuli project into your own python modules by adding the Sikuli project folder pathname to your python path. If you define constants for the images in a Sikuli project, you can import these constants to use the images in other python modules. However, because Sikuli puts only relative image filenames in the project script, you have to make the filenames absolute so that Sikuli Script can find them. If you use the SikuliImport package, any imported constants representing Sikuli images are automatically converted to absolute filenames.

# Essential Sikuli concepts #

Since Sikuli Script is a Jython module, Sikuli scripts are essentially Python scripts, but with the ability to import Java classes. Sikuli Script defines a number of classes and functions for visual scripting, the most important of which are:
  * Region: a region defines a rectangular section on a screen. A region is defined by the coordinates of its top-left corner and its width and height. A region knows nothing about the visual contents of the section. A region can be created by specifying its location and size on the screen, or relative to an existing region, or by finding a image in the screen and using the region of the match. A region has additional attributes (timeout, exception flag) that determine the behavior of find operations within the region. Sikuli defines a special region (SCREEN) that includes the entire screen.
  * Match: a match is the result of a successful search for an image on a screen or within a region. It is essentially a region with a match score. The region determines the position of the match while the match score is a measure of the similarity between the contents of the region and the image.
  * Pattern: a pattern is an image with a minimum similarity. When searching for an image, Sikuli ignores matches if the score is lower than the minimum similarity.
  * Location: a location is a position on the screen. It defines the target for click events.
  * find: a method on a region that searches for an image within the region and returns a match object representing the best match. If the image is not found (or none of the matches has a high enough score), it returns None or raises a FindFailed exception, depending on the exception flag. Sikuli Script also defines a find function that searches on the entire screen.
  * click: a method on a region that generates a click event. It uses its argument to determine the target location of the event. A Location instance defines the target directly. A match or region defines the target as the center of the region (but this can be changed). In these cases the region instance on which click is called is irrelevant. If an image filename or a pattern is passed, `click` calls `find` to search for the image in the region on which it is called, then uses the match to determine the target.
Besides `find` and `click`, a region also has methods to right-click, doubleclick, drag, drop and hover. For a complete overview, see the [Sikuli Script reference](http://sikuli.org/guide).