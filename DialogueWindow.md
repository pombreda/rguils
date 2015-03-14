﻿#summary RGUILS module to model dialogue window hierarchies
#labels Documentation

# Introduction #

The dialoguewindow module contains classes to model hierarchies of dialogue windows. The DialogueWindow class models a dialogue window with an optional parent window and optional child windows. It opens and closes a dialogue window and any dependent window if necessary and keeps track of which windows are open. The Confirm class is a subclass of DialogueWindow that models a simple modal window.

# Module contents #

## Classes ##

### DialogueWindow ###

This class is used to model modal dialogues. Each instance represents a dialogue window. In a modal dialogue, the dialogue windows form a directed graph. For simplicity we assume that the window graph is a tree (i.e. it is acyclic, each window except the root window has exactly one parent window, and the root window has no parent window).

The window graph determines the order in which windows can be opened and closed:
  * The root window is opened first.
  * A window that is not the root window can only be opened after its parent window has been opened.
  * A window can only be closed after all its child windows have been closed.
In a strict modal dialogue, at most one child window can be open. In order to open a different child window, the open child window must be closed first. However, the DialogueWindow class does not currently enforce this rule.

The DialogueWindow class automatically propagates open and close calls on a window to the parent and child windows to ensure that the above conditions are satisfied.

To create a dialogue window instance, you specify the parent window, unless the window is the root window. You must also specify a method to open the window, such as clicking a button in the parent window or hitting a key, and a method to close the window. After creating a dialogue window instance, one or more child windows can be added.
```
from seagull.dialoguewindow import DialogueWindow
from sikuli.Sikuli import SCREEN
from sikuli.Key import KEY_ALT

def open_dialogue(parent_window):
    SCREEN.click('configure_button.png')

def close_dialogue():
    SCREEN.click('ok_button.png')

def open_accounts(parent_window):
    SCREEN.type('a', KEY_ALT)

def close_accounts():
    SCREEN.type('c', KEY_ALT)

configure_window = DialogueWindow('configure', open_method = open_dialogue,
                       close_method = close_dialogue)
accounts_window = DialogueWindow('accounts', parent_window = configure_window,
                      open_method = open_accounts, close_method = close_accounts)
```
To open a dialogue window, call the `open` method on the dialogue window instance. Any parent windows that are not opened will be opened automatically. If the window is already open, the open method does nothing.
```
accounts_window.open()  # opens the configure window, then the accounts window
```
Similarly, to close a window, call the `close` method on the dialogue window instance. Any open child windows will be closed automatically.
```
configure_window.close()  # closes the accounts window, then the configure window
```
You can check whether a window is open:
```
accounts_window.open()

print configure_window.is_open()  # True
print accounts_window.is_open()   # True

accounts_window.close()

print configure_window.is_open()  # True
print accounts_window.is_open()   # False
```
You can pass arguments to the open and close methods:
```
def close_accounts(cancel = False):
    if cancel:
        SCREEN.click('cancel_button.png')
    else:
        SCREEN.click('ok_button.png')

accounts_window = DialogueWindow('accounts', parent_window = configure_window,
                      open_method = open_accounts, close_method = close_accounts)

accounts_window.open()

accounts_window.close()               # clicks "OK"
accounts_window.close(cancel = True)  # clicks "Cancel"
```
Instead of creating instances directly, you can extend DialogueWindow and define open and close methods by overriding the `_open()` and `_close()` methods.
```
from seagull.dialoguewindow import DialogueWindow
from sikuli.Sikuli import SCREEN
from sikuli.Key import KEY_ALT

class AccountsWindow(DialogueWindow):
    def __init__(self, parent_window):
        DialogueWindow.__init__(self, 'accounts', parent_window = parent_window)

    def _open(self, parent_window):
        SCREEN.type('a', KEY_ALT)

    def _close(self, cancel = False):
        if cancel:
            SCREEN.click('cancel_button.png')
        else:
            SCREEN.click('ok_button.png')
```
If a dialogue window also uses [Window](Window.md) as a base class, the default `_close()` method uses `Window.close()` to close the window, which clicks on the close button in the window title.

You can also override additional methods, `opening()`, `opened()`, `closing()` and `closed()`, that are called immediately before and after a window is opened respectively closed. An example where this is useful is to anchor an AnchoredWindow (see [Window](Window.md)) after it was opened, and to set the focus on a window before it is closed. Arguments that you pass to `open()` and `close()` are passed on to these methods as well. The default methods do nothing.
```
from seagull.window import AnchoredWindow
from sikuli.Sikuli import SCREEN
from sikuli.Key import KEY_ALT

class AccountsWindow(DialogueWindow, AnchoredWindow):
    def __init__(self, parent_window):
        DialogueWindow.__init__(self, 'accounts', parent_window = parent_window)
        AnchoredWindow.__init__(self, 'accounts_window.png', name = 'accounts window', title = 'Accounts')

    def _open(self, parent_window):
        SCREEN.type('a', KEY_ALT)

    def _close(self, cancel = False):
        if cancel:
            SCREEN.click('cancel_button.png')
        else:
            SCREEN.click('ok_button.png')

    def opened(self):
        self.anchor()    # inherited from AnchoredWindow

    def closing(self, cancel = False):
        self.setFocus()  # inherited from AnchoredWindow
```

### Confirm ###

This class is a subclass of DialogueWindow that models a modal window with a number of buttons, such as "OK", "Cancel" or "Yes", "No". The window is closed when a button is clicked. An instance is created by specifying the buttons, rather than the close method. Buttons are specified as a dictionary object with a button id as the key and a button image or a list of button images as the value.
```
from seagull.dialoguewindow import Confirm

CONFIRM_BUTTONS = {
    'yes' : ['yes.png', 'yes_focus.png'],
    'no'  : ['no.png', 'no_focus.png']
}

confirm_window = Confirm('Confirm Settings', buttons = CONFIRM_BUTTONS, open_method = open_confirm_window)
```
Instead of clicking a button, you can use keyboard shortcuts to close the window. In this case, the keys argument is a dictionary object that maps button ids to key sequences:
```
from seagull.dialoguewindow import Confirm
from sikuli.Key import Key

confirm_keys = {
    'ok'     : Key.ENTER,
    'cancel' : Key.ESC
}

confirm_window = Confirm('Confirm Settings', keys = confirm_keys, open_method = open_confirm_window)
```
To close the window using one of the buttons, simply pass the button id to the close method. If you don't specify a button id, the default close method in DialogueWindow is called.
```
confirm_window.close('ok')      # clicks OK or hits ENTER
confirm_window.close('cancel')  # clicks Cancel or hits ESC
confirm_window.close()          # clicks the close button in the window title bar
                                # if this window has Window as one of its base classes
```
If you don't specify any buttons or keys, the window uses the default keys `Key.ENTER` and `Key.ESC`:
```
confirm_window = Confirm('Confirm Settings', open_method = open_confirm_window)
```