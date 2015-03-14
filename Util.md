﻿#summary Utility functions and classes for RGUILS
#labels Documentation

# Introduction #

The util module provides basic utility functions and classes for GUI automation with Sikuli.

# Module contents #

## Classes ##

### AnchoredRegion ###

An anchored region is a region of a specified size whose location is determined relative to the location of an image (the anchor image) on the screen. An anchored region is created by specifying an anchor image, the offset of the anchor image within the region, and the region's size. In order to determine the absolute location of the region, the anchor image is searched on the screen (or within another region) and the region's location is calculated from the location of the image. AnchoredRegion is a subclass of a Sikuli Region, therefore it inherits all of Region's methods.

Example:
```
from seagull.util import AnchoredRegion

app_region = AnchoredRegion(app_logo, offsetx = 30, offsety = 50, width = 500, height = 380)
app_region.anchor(timeout = 3)
app_region.click(button_next)
```
This defines a region of size 500 x 380 pixels whose top-left corner is 30 pixels to the left and 50 pixels above the top-left corner of the app\_logo image. The anchor method searches the app\_logo image on the screen and calculates the region's location.

An anchored region can be defined to have the same location and size as the anchor image by omitting the offset, width and height parameters:
```
popup_region = AnchoredRegion(popup_image)
popup_region.anchor()
```
This creates an anchored region that encloses the popup image.

You can check whether the anchor image is visible on the screen:
```
if popup_region.is_displayed():
    print 'popup window has appeared'
```
You can wait until the anchor image becomes visible or disappears:
```
popup_region.wait_until_displayed(timeout = 15)
print 'popup window has appeared'

popup.close()
popup_region.wait_until_displayed(timeout = 10, is_displayed = False)
print 'popup window has disappeared'
```
An anchored region can have a parent region, in which case the anchor image is only searched in the parent region, rather than the entire screen. If the parent region is also an anchored region and its location has not been calculated, calling the anchor method on the subregion will automatically call the anchor method on the parent region first:
```
app_menu_region = AnchoredRegion(menu_header_image, offsetx = 0, offsety = -30, width = 100, height = 250, parentregion = app_region)
app_menu_region.anchor()
```
This searches for menu\_header\_image in app\_region and creates an anchored region 30 pixels below the top-left corner of menu\_header\_image. It also calls `app_region.anchor()` if it has not been called before.

### Wait ###

The Wait class can be used to sleep in intervals until a timeout is reached. TimeoutExceeded is raised when the timeout is reached.

Example:
```
from seagull.util import Wait

waiting = Wait(30, interval = 3, exception_message = 'waiting time exceeded')
while not test_some_condition():
    waiting.wait()
```
If the condition is not true, it is retested repeatedly after 3 seconds, until it becomes true or the waiting instance has spent 30 seconds sleeping. In the latter case, TimeoutExceeded with the specified message is raised.

### TimeoutExceeded ###

an exception raised by `wait()` when the timeout is exceeded.

Example:
```
raise TimeoutExceeded('condition not true after the specified timeout')
```

## Functions ##

### find ###

Like `Region.find(arg)` except that the timeout and exception can be specified as parameters:
```
find(arg, region = search_region, timeout = 1, exception = False)
```

### findAny ###

Searches the specified region for the elements in the given list repeatedly until it finds one, or the timeout is exceeded, and returns the element index and the match:
```
# returns (0, match1) if image1 is found first
# returns (1, match2) if image2 is found first
# raises FindFiled if none of the images is found within 1 second
findAny([image1, image2], region = SCREEN, timeout = 1, exception = True)
```

### existsAny ###

Searches the specified region for the elements in the given list and returns the index of the first one that is found, or None if none of the elements is found within the specified time:
```
# returns 0 if image1 is found on the screen
# returns 1 if image1 is not found but image2 is found on the screen
# returns None if neither image is found on the screen
existsAny([image1, image2], region = SCREEN, timeout = 0)
```

### waitAny ###

Waits until one of the elements in the given list is found in the specified region:
```
# returns when image1 or image2 are found on the screen within 10 seconds
# raises FindFailed if none of the images is found within 10 seconds
waitAny([image1, image2], region = SCREEN, timeout = 10)
```

### getAllMatches ###

Searches the specified region for all elements in the given list simultaneously, until all elements have been found or the timeout is exceeded. Returns a list of the matches (None for elements that could not be found).
```
# returns [match1, match2] if both images are found within 3 seconds
# returns [match1, None] if only image1 is found within 3 seconds
# returns [None, match2] if only image2 is found within 3 seconds
# returns [None, None] if no image is found within 3 seconds
getAllMatches([image1, image2], region = SCREEN, timeout = 3)
```

### getAllScores ###

Like getAllMatches, but returns a list of the match scores rather than the matches. Returns 0 for elements that could not be found.
```
getAllScores([image1, image2], region = SCREEN, timeout = 3)
```

### click ###

Like `Region.click(arg, modifiers)` except that the timeout and exception can be specified as parameters:
```
click(arg, modifiers, region = search_region, timeout = 1, exception = False)
```

### clickAny ###

Searches the specified region for the elements in the list repeatedly and clicks on the first one that is found:
```
# clicks on image1 if image1 is found first
# clicks on image2 if image2 is found first
# raises FindFailed if none of the images is found within 5 seconds
clickAny([image1, image2], region = SCREEN, timeout = 5, exception = True)
```

### clickOffset ###

Like `click` but allows to specify an offset. The click location is determined relative to the original location using the specified offset:
```
clickOffset(image, offsetx = -30, offsety = 10)
```

### typeKeys ###

Like the type function defined in sikuli.Sikuli, but with the following differences:
  * the function name is typeKeys so that the builtin python function type is still available
  * region is a keyword argument
  * the additional repeat argument allows to type a sequence repeatedly
Example:
```
typeKeys(Key.DOWN, repeat = 8)
```
presses the curser down key 8 times.

### setException ###

Like `Region.setThrowException`, but returns the previous value.
```
previous_flag = setException(region, flag)
```

### setTimeout ###

Like `Region.setAutoWaitTimeout`, but returns the previous value.
```
previous_timeout = setTimeout(region, timeout)
```

### getOverlap ###

Returns the overlap of two regions as a fraction of the first region.
```
r1 = Region(10, 10, 40, 20)   # tlc = (10, 10), brc = (50, 30), area = 800 sq px
r2 = Region(40, 20, 20, 20)   # tlc = (40, 20), brc = (60, 40), area = 400 sq px
overlap = getOverlap(r1, r2)  # overlap = 0.125 (100 sq px / 800 sq px)
```

### sameRegion ###

Returns True if the overlap of two regions exceeds a minimum value, which can be interpreted as the two regions covering approximately the same area.
```
r1 = Region(10, 10, 40, 20)
r2 = Region(11, 9, 42, 19)
r3 = Region(10, 70, 40, 20)
print sameRegion(r1, r2, minOverlap = 0.7)  # True
print sameRegion(r1, r3, minOverlap = 0.7)  # False
```

### sortRegions ###

Sorts a list of regions horizontally and vertically by the position of their top-left corner on the screen. The sort order is specified by the second argument. The list is sorted in place.

One of the two dimensions (horizontal or vertical) is the major sort order, and the other dimension is the minor sort order. Each of the two sort orders can be ascending or descending. For example, if the regions are sorted major horizontal ascending, minor vertical descending, any region whose top-left corner is to the left of the top-left corner of another region will be sorted before that other region, and any region whose top-left corner is exactly below the top-left corner of another region (with the same y-coordinate) will be sorted before that other region. More precisely, the sort order is specified by a bit-wise OR of a subset of the following constants:
```
REGION_SORT_HORIZONTAL = 4
REGION_SORT_HDESC = 2
REGION_SORT_VDESC = 1
```
Given four regions with coordinates of their top-left corners A=(10, 10),  B=(20, 10),  C=(10, 20),  D=(20, 20),  which would be positioned as follows:
```
A   B

C   D
```
the following sort orders are possible:
```
HORIZONTAL   HDESC     VDESC     sort order
False        False     False     A, B, C, D
False        False     True      C, D, A, B
False        True      False     B, A, D, C
False        True      True      D, C, B, A
True         False     False     A, C, B, D
True         False     True      C, A, D, B
True         True      False     B, D, A, C
True         True      True      D, B, C, A
```
For example:
```
regions = [A, B, C, D]
sortRegions(regions, REGION_SORT_HORIZONTAL | REGION_SORT_VDESC)
print regions  # [C, A, D, B]
```

### getUniqueRegions ###

Takes a list of regions and returns a new list that contains the same regions as the original list, in the same order, but with any duplicate regions removed. Two regions are duplicates if they have the same coordinates and size.
```
r1 = Region(10, 10, 40, 20)
r2 = Region(30, 10, 10, 40)
r3 = Region(10, 10, 40, 20)
regions = [r1, r2, r3]
unique_regions = getUniqueRegions(regions)  # [r1, r2]
```

### bestMatch ###

Takes a list of images and a base region, and for each image, finds the best match in the base region. If all the matches found in this manner cover approximately the same region, the function returns the index of the match with the highest score, and the match, as a tuple. If the matches cover different regions, an exception is raised. Images that are not found in the base region are ignored. However, if none of the image is found, a FindFailed exception is raised (unless raising FindFailed is turned off, in which case the function returns None). The base region is optional, if it is not specified, the entire screen is used as the base region. The minimum overlap for two matches to cover the same region can be specified with the optional argument minOverlap.

The purpose of this method is to find an element in the base region if there exist multiple images of the element, i.e. the element can look differently depending on its state. For example, a button can have a different background, foreground and border color depending on whether it is enabled or disabled, has focus or not, and the mouse pointer is over it or not. To find the button reliably and to identify its state (enabled/disabled, focus, mouse-over), you take an image of the button in each state and call bestMatch. The images should all have approximately the same size, so their matches cover the same region.
```
IMAGES = ['button.png', 'button_focus.png', 'button_mouseover.png', 'button_focus_mouseover.png', 'button_disabled.png']

try:
    index, match = bestMatch(IMAGES, region = base_region, timeout = 3, minOverlap = 0.8)
    if index == 4:
        print 'button is disabled'
    if index in [1, 3]:
        print 'button has focus'
    if index in [2, 3]:
        print 'mouse is over button'
except FindFailed:
    print 'button not found'
```

### bestMatches ###

This function is similar to `bestMatch` but without the requirement that all matches cover the same region. It takes a list of images and a base region, and for each image, finds the best match in the base region. It then groups together all matches that cover the same region, and in each group determines the match with the highest score and returns the list of highest scoring matches, and their indexes. Images that are not found in the base region are ignored. If none of the images is found, a FindFailed exception is raised (unless raising FindFailed is turned off, in which case the function returns None).

This function is useful if a region contains multiple, different elements and each element can look differently, depending on its state. For example, imagine a dialogue window with two buttons, labeled "Next" and "Back", and you have images of both buttons in various states (disabled, with/without focus, etc.). Since the two buttons look very similar, each image can match both buttons, although with different scores. However, if the "Next" button has focus, an image of the "Back" button with focus may have a better match with the "Next" button (with focus) than with the "Back" button (without focus). Therefore, identifying the buttons separately (e.g. using `bestMatch`) is unreliable and likely to fail.

The `bestMatches` function finds the best match for each image. It groups the matches into two groups, one including matches that cover the "Next" button, and one for the "Back" button (assuming there are no other buttons). The image of the "Back" button with focus is still in the wrong group, but it is ignored because the image of the "Next" button with focus has a higher score in the first group, and the image of the "Back" button without focus has the highest score in the second group. The function then returns the indexes of these images, and the corresponding matches.
```
IMAGES_NEXT = ['next.png', 'next_focus.png', 'next_mouseover.png', 'next_focus_mouseover.png', 'next_disabled.png']
IMAGES_BACK = ['back.png', 'back_focus.png', 'back_mouseover.png', 'back_focus_mouseover.png', 'back_disabled.png']

matches = bestMatches(IMAGES_NEXT + IMAGES_BACK, base_region, timeout = 3, minOverlap = 0.8)

for index, match in matches:
    if index < len(IMAGES_NEXT):
        label = 'Next'
    else:
        label = 'Back'
        index -= len(IMAGES_NEXT)
    if index == 4:
        print '%s button is disabled' % label
    if index in [1, 3]:
        print '%s button has focus' % label
    if index in [2, 3]:
        print 'mouse is over %s button' % label
```

### extendRegion ###

Changes the coordinates, width and height of a region. For positive arguments, the region is extended in all four directions.
```
region = Region(10, 10, 40, 20)
extendRegion(region, top = 5, right = 30, bottom = 7, left = 3)
print region  # Region(7, 5, 73, 32)
```
Note that the original region is changed, so you should make a copy if you still need the original region.

### setDebug ###

Turns debug logging on or off. If the argument is True, the log level of the root logger is set to logging.DEBUG. If the argument is False, it is set to logging.INFO.
```
setDebug(True)
```

### getDebug ###

Returns True if debug messages are logged.
```
if getDebug():
    print 'debugging is turned on'
```

### setDebugRegion ###

Sets the region for debugging in this module. If a function in this module tries to find an image and debugging is turned on and no debug region is defined, the result is printed on the debug logger. If a debug region is set, debugging is limited to instances where the find is invoked on the debug region.
```
setDebug(True)
setDebugRegion(base_region)
index, match = bestMatch(images, region = base_region)

setDebugRegion(None)  # log all matches
```

### getDebugRegion ###

Returns the current debug region, or None if no debug region is defined.
```
debug_region = getDebugRegion()
```

### setShowRegions ###

Turns visual debugging of matches found by functions in this module on or off. If turned on, when a function finds a match, the outline and center of the match region is shown on the screen for 2 seconds.
```
setShowRegion(True)
```

### getShowRegions ###

Returns True if visual debugging of matches is turned on, else False.
```
if getShowRegions():
    print 'visual debugging is turned on'
```

### showRegion ###

Shows the outline and center of a region on the screen for 2 seconds, or for the specified amount of time. Useful for debugging matches. It uses the OutlineOverlayWindow class from the OverlayWindow module.
```
match = find('image.png')
showRegion(match)                # show outline for 2 seconds
showRegion(match, duration = 5)  # show outline for 5 seconds
```