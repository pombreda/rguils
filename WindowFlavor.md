﻿#summary RGUILS module to define window properties
#labels Documentation

# Introduction #

The windowflavor module defines window properties that depend on the operating system and version. It's main purpose is to import properties from one of the operating system specific modules.


# Examples #

```
from seagull.windowflavor import WINDOW_TITLEBAR_HEIGHT
```

# Module contents #

## Functions ##

### getWindowsVersion ###

Returns the Windows version. The return value is 'XP', 'Vista' or '7'.
```
from seagull.windowflavor import getWindowsVersion

print 'Windows version: %s' % getWindowsVersion()
```

# See also #

  * [Window](Window.md)
  * [WindowsXP](WindowsXP.md)