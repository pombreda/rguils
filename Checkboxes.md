﻿#summary RGUILS module to model checkboxes and radio buttons
#labels Documentation

# Introduction #

The checkboxes module provides an object-oriented API to model rows and columns of checkboxes, radio buttons, and similar elements in a robust way.

# Module contents #

## Classes ##

### Checkable ###

This is a base class to model a row or column of identical elements that can be checked or unchecked, such as checkboxes and radio buttons. An instance is defined by providing one or more images of checked elements and one or more images of unchecked elements. The class locates all occurrences of the element in the specified region and determines its status (checked or unchecked).

This class should not be used directly, instead use the Checkboxes and RadioButtons classes or their subclasses.

### Checkboxes ###

Models a row or column of checkboxes.
```
from seagull.checkboxes import Checkboxes

CHECKED_IMAGES = [IMG_CHECKBOX_CHECKED_1, IMG_CHECKBOX_CHECKED_2]
UNCHECKED_IMAGES = [IMG_CHECKBOX_UNCHECKED_1, IMG_CHECKBOX_UNCHECKED_2]
CHECKBOX_IMAGES = {
    'checked' : CHECKED_IMAGES,
    'unchecked' : UNCHECKED_IMAGES
}
checkboxes = Checkboxes(CHECKBOX_IMAGES, region = app_window_region, orientation = 0)
```
This defines a vertical row of checkboxes in the app\_window\_region (to define a horizontal row, use orientation = REGION\_SORT\_HORIZONTAL, defined in [Util](Util.md)). After the checkboxes are defined, they must be located in the region by calling find\_elements:
```
checkboxes.find_elements()
print 'found %d checkboxes' % checkboxes.length()

if checkboxes.is_checked(0):
    print 'first checkbox is checked'

print 'indexes of checked elements: %s' % ', '.join(map(str, checkboxes.checked_elements()))

for i, is_checked in enumerate(checkboxes.checked()):
    if is_checked:
        print 'checkbox %d is checked' % (i + 1)

checkboxes.check(1)             # checks the second checkbox
checkboxes.check_all([0,2,4])   # checks the first, third and fifth checkbox
checkboxes.check_all()          # checks all checkboxes
checkboxes.uncheck(1)           # unchecks the second checkbox
checkboxes.uncheck_all([0,2,4]) # unchecks the first, third and fifth checkbox
checkboxes.uncheck_all()        # unchecks all checkboxes
checkboxes.toggle(1)            # toggles the first checkbox
checkboxes.set([1,2,3])         # checks the second, third and fourth checkbox and unchecks all other checkboxes
```
To update a Checkboxes instance after a checkbox has been changed by an external event (e.g. some application logic), call update\_element:
```
checkboxes.update_element(2)    # updates the third checkbox
```
You can also tell the instance what the current state of a checkbox is:
```
checkboxes.set_element_state(2, True)
```
You can wait until a checkbox becomes checked or unchecked:
```
checkboxes.wait(2, checked = True, timeout = 15)
```
If the optional parameter auto\_verify is set to True, the methods that click on checkboxes wait until the checkbox has changed on the screen. The timeout for this can be set using the optional timeout parameter. If a checkbox does not react to the click within the timeout, an Exception is raised.
```
checkboxes = Checkboxes(checkbox_images, region = app_window_region, orientation = 0, auto_verify = True, timeout = 5)
```

### VerticalCheckboxList ###

Subclass of Checkboxes to model a vertical column of checkboxes.
```
from seagull.checkboxes import VerticalCheckboxList

checkboxes = VerticalCheckboxList(checkbox_images, region = app_window_region)
```

### HorizontalCheckboxList ###

Subclass of Checkboxes to model a horizontal row of checkboxes.

### RadioButtons ###

Models a row or column of radio buttons. At most one radio button can be checked at a time. If a radio button is checked, any other radio button that was previously checked will be unchecked.
```
from seagull.checkboxes import RadioButtons

radiobuttons = RadioButtons(RADIO_BUTTON_IMAGES, region = app_window_region, orientation = 0)

radiobuttons.find_elements()
print 'button %d is checked' % radiobuttons.checked_element()
```

### VerticalRadioButtonList ###

Subclass of RadioButtons to model a vertical column of radio buttons.

### HorizontalRadioButtonList ###

Subclass of RadioButtons to model a horizontal row of radio buttons.

# See also #

  * [Buttons](Buttons.md)
  * [Images](Images.md)
  * SikuliImport