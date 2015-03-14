﻿#summary RGUILS module to model windows for GUI automation
#labels Documentation

# Introduction #

The window module provides classes to model windows for GUI automation. It provides an object-oriented API to perform basic operations on windows, such as minimizing, maximizing, closing, and killing the application that owns a window.

# Module contents #

## Classes ##

### Window ###

The Window class models basic window functionality: minimizing, maximizing, closing and setting the focus on a window. In addition it allows you to kill the process that owns the window.

To create a Window instance, you specify the region of the window on the screen and the window title. The region must include the title bar.
```
from sikuli.Sikuli import SCREEN
from seagull.window import Window

window_region = SCREEN.find('window.png')
window = Window(window_region, 'Main Window')

window.setFocus()  # sets the focus on the window by clicking on the center of the title bar
window.minimize()
window.maximize()
window.close()     # uses the close button in the title bar

window.kill()      # uses the window title to kill the process that owns the window
```

### AnchoredWindow ###

Models a window that covers an anchored region (see [Util](Util.md)). Unlike a window, the position and size of an anchored window is not specified when the instance is created (by passing a region), instead you have to call the anchor method to match the anchored window to an actual window on the screen. This means you can create an AnchoredWindow instance before the window is actually shown on the screen. After the window has appeared on the screen, you call the anchor method to set its position and size.

To create an anchored window instance, you specify the window title, the anchor image, the anchor image offset and the window size. If the anchor image is an image of the entire window, the offset and window size can be omitted.
```
from seagull.window import AnchoredWindow

window = AnchoredWindow(title = 'Main Window', anchorimage = 'windowanchor.png',
             offsetx = 10, offsety = 40, width = 480, height = 287)

# defines a window that covers the entire anchor image
window = AnchoredWindow(title = 'Main Window', anchorimage = 'window.png')

# match this instance to the actual window on the screen
window.anchor()
```
AnchoredWindow inherits all methods from Window and AnchoredRegion.

# See also #

  * AnchoredRegion in [Util](Util.md)