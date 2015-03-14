﻿#summary RGUILS module for collections of GUI images
#labels Documentation

# Introduction #

The images module imports images of buttons, checkboxes and radio buttons from Sikuli projects and creates dictionaries in the form required by the classes to automate [Buttons](Buttons.md), [Checkboxes](Checkboxes.md) and radio buttons. The type of image (i.e. whether it is a button, a checkbox, etc.), the button label (e.g. OK, Cancel, Next) and the button state (enabled/disabled, checked/unchecked) is derived from the name of the constant with the image filename. For example, the following constant in a Sikuli project indicates a Next button with focus (the focus is ignored here):
```
IMG_BUTTON_NEXT_FOCUS = '1270078045794.png'
```

# Module contents #

## Constants ##

### IMG\_BUTTONS ###

A dictionary object with button images. For each constant `IMG_BUTTON_name[_qualifier]` imported from Sikuli projects, _name_ is a key in `IMG_BUTTONS`, and the value is a list of the values of all such constants (the qualifier is ignored). For example, assume a Sikuli project defines the following constants:
```
IMG_BUTTON_NEXT_ORANGE        = '1270078081419.png'
IMG_BUTTON_NEXT_BLUE          = '1270078045794.png'
IMG_BUTTON_NEXT               = '1275000336584.png'
IMG_BUTTON_BACK               = '1274999069194.png'
IMG_BUTTON_BACK_BLUE_DOTTED   = '1274999098834.png'
IMG_BUTTON_BACK_ORANGE_DOTTED = '1274999116678.png'
```
Then `IMG_BUTTONS` would be
```
IMG_BUTTONS = {
    'next' : ['1270078081419.png', '1270078045794.png', '1275000336584.png'],
    'back' : ['1274999069194.png', '1274999098834.png', '1274999116678.png']
}
```
(the SikuliImport module expands image filenames to absolute pathnames, but for readability only the filenames are shown).

To use button images:
```
from seagull.images import IMG_BUTTONS
from seagull.util import clickAny

clickAny(IMG_BUTTONS['cancel'])
```

### IMG\_BUTTONS\_DISABLED ###

A dictionary object with images of disabled buttons. Similar to IMG\_BUTTONS, but uses constants named `IMG_DISABLED_BUTTON_name[_qualifier]`.

Here's an example using IMG\_BUTTONS and IMG\_BUTTONS\_DISABLED to create a [Buttons](Buttons.md) object:
```
from seagull.images import IMG_BUTTONS, IMG_BUTTONS_DISABLED
from seagull.buttons import Buttons

button_images = {
    'next' : IMG_BUTTONS['next'],
    'back' : IMG_BUTTONS['back']
}
button_images_disabled = {
    'next' : IMG_BUTTONS_DISABLED['next'],
    'back' : IMG_BUTTONS_DISABLED['back']
}

buttons = Buttons(button_images, button_images_disabled)
buttons.find_buttons()

buttons.waitUntilButtonIsEnabled('next', timeout = 15)
buttons.click('next')
```

### IMG\_CHECKBOXES ###

A dictionary object containing images of checkboxes. It has two keys, "checked" and "unchecked". The value of "checked" is a list of the values of all constants named `IMG_CHECKED_BOX[_qualifier]`. The value of "unchecked" is a list of the values of all constants named `IMG_UNCHECKED_BOX[_qualifier]`.
```
from seagull.images import IMG_CHECKBOXES
from seagull.checkboxes import Checkboxes

checkboxes = Checkboxes(IMG_CHECKBOXES)
```

### IMG\_RADIOBUTTONS ###

A dictionary object containing images of radio buttons. It has two keys, "checked" and "unchecked". The value of "checked" is a list of the values of all constants named `IMG_CHECKED_RADIOBUTTON[_qualifier]`. The value of "unchecked" is a list of the values of all constants named `IMG_UNCHECKED_RADIOBUTTON[_qualifier]`.
```
from seagull.images import IMG_RADIOBUTTONS
from seagull.checkboxes import RadioButtons

radiobuttons = RadioButtons(IMG_RADIOBUTTONS)
```