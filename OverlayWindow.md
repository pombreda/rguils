﻿#summary RGUILS module to draw overlay windows for debugging
#labels Documentation

# Introduction #

The overlaywindow module is a Jython module that contains classes to draw overlay windows on the screen. It uses javax.swing and java.awt.

# Module contents #

## Classes ##

### OverlayWindow ###

This is an abstract base class to show a region on the screen. Subclasses must implement the methods prepareShowRegion() to prepare the overlay window for drawing, and paint(graphics) (specified in javax.swing.Jwindow) to draw the overlay window.
```
from seagull.overlaywindow import OverlayWindow

# shows a region by drawing its outline in red
class SimpleOverlayWindow(OverlayWindow):
    def __init__(self, screen = None):
        OverlayWindow.__init__(self, screen)

    def prepareShowRegion(self):
        x, y, w, h = self.region.getX(), self.region.getY(), self.region.getW(), self.region.getH()
        self.region_image = self.screen.capture(x, y, w, h).getImage()
        self.setLocation(x, y)
        self.setSize(w, h)

    def paint(self, graphics):
        graphics.drawImage(self, region_image, 0, 0, self)
        w, h = self.region.getW(), self.region.getH()
        if w < 1 or h < 1:
            return
        graphics.setColor(Color.red)
        graphics.drawRect(0, 0, w-1, h-1)
```
To show a region on the screen, create an overlay window instance and call the showRegion method:
```
overlay_window = SimpleOverlayWindow()
overlay_window.showRegion(region, duration = 3)
```
If a duration is specified, the overlay window automatically disappears from the screen after the specified number of seconds. If no duration is specified, the overlay window remains visible until the close method is called or the user clicks on the overlay window.

### DimOverlayWindow ###

This is a subclass of OverlayWindow that shows a region by dimming the screen around the region and drawing a cross in the center of the region.
```
from seagull.overlaywindow import DimOverlayWindow

overlay_window = DimOverlayWindow()
overlay_window.showRegion(region, duration = 3)
```

### OutlineOverlayWindow ###

This is a subclass of OverlayWindow that shows a region by drawing its outline as a red rectangle and drawing a horizontal and vertical line through the center of the region.
```
from seagull.overlaywindow import OutlineOverlayWindow

overlay_window = OutlineOverlayWindow()
overlay_window.showRegion(region, duration = 3)
```