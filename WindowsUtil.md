﻿#summary RGUILS module with utility functions for Windows
#labels Documentation,Windows

# Introduction #

This module provides useful functions for GUI automation under Windows.
```
from seagull.os.windows.util import *
```

# Module contents #

## Functions ##

### getControlPanelPath ###

Returns the path of the Windows Control Panel. The path is Windows version specific.
```
path = getControlPanelPath()
```

### getInternetExplorerPath ###

Returns the path of Internet Explorer. The path is Windows version specific.
```
path = getInternetExplorerPath()
```

### getMSOfficePath ###

Returns the root path of Microsoft Office. The path is Windows version specific.
```
path = getMSOfficePath()
```

### getNotepadPath ###

Returns the path of the Notepad. The path is Windows version specific.
```
path = getNotepadPath()
```

### getOutlookPath ###

Returns the path of Microsoft Outlook. The path is Windows version specific.
```
path = getOutlookPath()
```

### getWindowsExplorerPath ###

Returns the path of Windows Explorer. The path is Windows version specific.
```
path = getWindowsExplorerPath()
```

### getUsername ###

Returns the current username.
```
print 'current username: %s' % getUsername()
```

### getMSOfficeVersion ###

Returns the Microsoft Office version as an integer between 9 and 12. If Microsoft Office is not installed or the version cannot be determined, an exception is raised.
```
print 'MS Office version: %d' % getMSOfficeVersion()
```

### getTempDir ###

Returns the directory in which temporary files are created.
```
print 'Tempdir=%s' % getTempDir()
```

### getClipboardText ###

Returns the text in the system clipboard, or None if the system clipboard does not contain text. This uses java.awt to access the system clipboard.
```
text = getClipboardText()
```

### openInternetExplorer ###

Launches Internet Explorer. If a URL is specified, it is opened in Internet Explorer.
```
# launches Internet Explorer with the default home page
openInternetExplorer()

# launches Internet Explorer with the Google home page
openInternetExplorer('http://www.google.com/')
```

### openNotepad ###

Launches Notepad. If a filename is specified, it will be opened in Nodepad.
```
openNotepad()
openNodepad('C:\\Documents and Settings\\username\\My Documents\\readme.txt')
```

### openOutlook ###

Launches Microsoft Outlook.
```
openOutlook()
```

### openWindowsExplorer ###

Launches Windows Explorer with a new single-pane window. If a folder is specified, it is opened in the window, otherwise the default folder (usually C:\) is opened.
```
# opens "C:\" in Windows Explorer
openWindowsExplorer()

# opens "C:\Program Files" in Windows Explorer
openWindowsExplorer('C:\\Program Files')
```
The select argument can be used to select a filename or folder in the window. You must specify the absolute path of the folder or filename to select. The folder argument will be ignored in this case.
```
# opens "C:\" and selects "Program Files"
openWindowsExplorer(select = 'C:\\Program Files')

# display Microsoft Outlook file properties
openWindowsExplorer(select = getOutlookPath())
keyRightClick()
```

### makeTempFile ###

Creates a temporary file and opens it for writing. The function returns the filehandle and the filename as a tuple. Unless a suffix is specified, the filename is created with no suffix. Note that in order to create a filename with a filename extension separated by a dot, the dot must be included in the suffix. If you want the file to be closed, set the optional argument close to True.
```
# creates a temporary file with no filename extension
filehandle, filename = makeTempFile()

# creates a temporary file with a .txt extension
filehandle, filename = makeTempFile(suffix = '.txt')

# creates a closed temporary .txt file
filehandle, filename = makeTempFile(suffix = '.txt', close = True)
```
_This function is not secure and prone to race conditions._

### saveInTempFile ###

Saves a string in a temporary file and returns the filename.
```
text = getClipboardText()
filename = saveInTempFile(text, suffix = '.txt')
```

### keyCopy ###

Types Control-C.
```
keyCopy()
```

### keyCut ###

Types Control-X.
```
keyCut()
```

### keyPaste ###

Types Control-V.
```
keyPaste()
```

### keyRightClick ###

Types Shift-F10. This is the keyboard equivalent to a click with the right mouse button. If a region is specified, it performs a left click on the region first.
```
keyRightClick()
keyRightClick(region)
```

### runApplication ###

Launches an application. If no commandline arguments are specified, it uses the openApp function in Sikuli to launch the application, which is faster and more reliable than the Run dialogue in the Windows start menu. However, openApp cannot be used to launch an application with commandline arguments. In this cases, it uses the Run dialogue. The optional quoteargs argument can be used to disabled argument quoting.
```
# launches Internet Explorer using openApp
runApplication('iexplore.exe')

# launches Internet Explorer using the Run dialogue
runApplication('iexplore.exe', 'http://www.google.com/')
```

### startCommand ###

Runs a command in a new process using the Windows `start` command. You can specify arguments that are passed to the command.
```
startCommand('iexplore')
startCommand('iexplore', 'http://www.google.com/')
```
You can also specify arguments for the `start` command to control the appearance of the application window. For example, to launch IE in a maximized window, use
```
startCommand('iexplore', 'http://www.google.com/', startargs = ['/max'])
```
Type `start /?` in a Windows command prompt to see the available arguments.