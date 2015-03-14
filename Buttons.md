﻿#summary RGUILS module to model sets of buttons
#labels Documentation

# Introduction #

The buttons module provides an object-oriented API to interact with buttons in a robust way.

# Module contents #

## Classes ##

### Buttons ###

Models a list (or set) of buttons. A button list is created by providing a dictionary object with one or more images for each button. The class locates all the buttons that exist in the specified region (which defaults to the screen). You can check which buttons exist, whether they are enabled or disabled, and click on them.
```
from seagull.buttons import Buttons

NEXT_BUTTONS = [IMG_NEXT_BUTTON_1, IMG_NEXT_BUTTON_2, IMG_NEXT_BUTTON_3]
PREV_BUTTONS = [IMG_PREV_BUTTON_1, IMG_PREV_BUTTON_2, IMG_PREV_BUTTON_3]
CANCEL_BUTTONS = [IMG_CANCEL_BUTTON_1, IMG_CANCEL_BUTTON_2, IMG_CANCEL_BUTTON_3]
BUTTONS = {
    'next' : NEXT_BUTTONS,
    'prev' : PREV_BUTTONS,
    'cancel' : CANCEL_BUTTONS
}

buttonlist = Buttons(BUTTONS, region = app_window_region)
```
This defines three buttons (next, prev, cancel) in the app\_window\_region. After the buttons are defined, they must be located in the region by calling find\_buttons:
```
buttonlist.find_buttons()

print 'found %d buttons' % buttonlist.button_count()
print 'found the following buttons: %s' % ', '.join(buttonlist.button_names())

if buttonlist.exists_button('cancel'):
    print 'cancel button exists'
```
Buttons are located by searching each button image in the specified region, and clustering matches that cover (approximately) the same region. Each region represents a button. The image with the highest match score in each region defines which button the region represents. Each button can exist only once. If two regions represent the same button, an Exception is raised.

To click a button, simply call the click method with the button name:
```
buttonlist.click('next')
```
An optional argument can be used to specify images of disabled buttons:
```
NEXT_BUTTONS_DISABLED = [IMG_NEXT_BUTTON_DISABLED]
PREV_BUTTONS_DISABLED = [IMG_PREV_BUTTON_DISABLED]
CANCEL_BUTTONS_DISABLED = [IMG_CANCEL_BUTTON_DISABLED]
DISABLED_BUTTONS = {
    'next' : NEXT_BUTTONS_DISABLED,
    'prev' : PREV_BUTTONS_DISABLED,
    'cancel' : CANCEL_BUTTONS_DISABLED
}
buttonlist = Buttons(BUTTONS, DISABLED_BUTTONS, region = app_window_region)
```
If the best match score for a disabled button image is higher than the best match score for a normal (non-disabled) image of the same button, the class assumes that the button is disabled. After locating buttons, you can check which buttons are enabled, and wait until all or specific buttons are enabled:
```
buttonlist.find_buttons()

if buttonlist.all_buttons_enabled():
    print 'all buttons are enabled'
if buttonlist.is_button_enabled('next'):
    print 'next button is enabled'

# returns when the 'next' button is enabled
# raises Exception if the 'next' button is not enabled after 10 seconds
buttonlist.waitUntilButtonIsEnabled('next', 10)

# returns when all buttons are enabled
# raises Exception if not all buttons are enabled after 10 seconds
buttonlist.waitUntilAllButtonsEnabled(10)
```
The wait methods update the button status (enabled/disabled) every second by searching for the button images within the region of a button. You can update the button status at any time:
```
# updates the status of the 'cancel' button
buttonlist.update_button('cancel')

# updates the status of all buttons
buttonlist.update_buttons()
```

# See also #

  * [Checkboxes](Checkboxes.md)
  * [Images](Images.md)
  * SikuliImport