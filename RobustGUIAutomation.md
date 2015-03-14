#summary Discusses challenges in robust GUI automation

# Introduction #

This document explains some of the challenges that GUI automation with Sikuli, and GUI automation in general, present. If you are not familiar with Sikuli, read the SikuliOverview first.

# GUI automation challenges #

Sikuli's visual scripting promises to make GUI automation very easy. However, in reality, robust GUI automation is much more difficult to achieve. The following is a list of challenges that will be considered in more detail below:
  * the same GUI element can look different, depending on its state or the application's state
  * different GUI elements can look similar
  * missed matches and spurious matches
  * lack of synchronization between the application and the GUI automation
  * lack of a window concept in Sikuli

## Identifying GUI elements correctly ##

Let's say you are automating some wizard with three buttons labeled "Next", "Back" and "Cancel" and you want to click the Next button:

![http://rguils.googlecode.com/svn/wiki/images/installerwindow.png](http://rguils.googlecode.com/svn/wiki/images/installerwindow.png)

The most naive approach would be to take an image of the Next button (using the Sikuli IDE) and call the click function:
```
click('next_button.png')
```
But when you run the script, Sikuli clicks on the Back button. Why is this? Remember that Sikuli finds the best match, i.e. the region with the same size as the image that maximizes the similarity between the region's visual content and the image. Apparently the Back button looks more similar to your image than the Next button.

The problem is that buttons look different depending on their state, i.e. whether the button has focus or not, the window that contains the button has focus or not, the mouse is over the button or not, the button is enabled or disabled, etc. For example, in Windows XP the border color of a standard button changes depending on focus and mouse over:
  * <img src='http://rguils.googlecode.com/svn/wiki/images/next.png' align='middle'>
<ul><li><img src='http://rguils.googlecode.com/svn/wiki/images/next_focus.png' align='middle'> (button has focus)<br>
</li><li><img src='http://rguils.googlecode.com/svn/wiki/images/next_mouse_over.png' align='middle'> (mouse over button)<br>
</li><li><img src='http://rguils.googlecode.com/svn/wiki/images/next_disabled.png' align='middle'> (button is disabled)<br>
If the Next button in the wizard has focus but in your image it doesn't, then the Back button might look more similar to your image than the Next button. A human user would most likely not make that mistake because as humans we use the button label to identify the correct button and abstract from the border color, but Sikuli (or the image matching algorithm it uses) has no concept of characters; it just sees a rectangular area of pixels.</li></ul>

Then why not use multiple images of the Next button, one with a thin black border, one with a blue border and one with an orange border. We let Sikuli find the best match for each image and then pick the match with the highest score:<br>
<pre><code>BUTTON_IMAGES = ['next.png', 'next_focus.png', 'next_mouse_over.png']<br>
<br>
best_match = None<br>
for image in BUTTON_IMAGES:<br>
    try:<br>
        match = find(image)<br>
        if best_match is None or match.getScore() &gt; best_match.getScore():<br>
            best_match = match<br>
    except FindFailed:<br>
        pass<br>
<br>
if best_match is None:<br>
    raise FindFailed<br>
click(best_match)<br>
</code></pre>
The problem is that this approach is invalid, because it compares match scores that are incomparable. In order for the scores of two matches to be comparable, they must have a common base, which is either the image or the match region:<br>
<ul><li>If the same image is matched with two different regions, the scores are comparable on the basis that both matches use a common image, so we can select the region that better resembles the image.<br>
</li><li>Similarly, if two images are matched with the same region, the scores are comparable on the basis that the matches are performed on a common section of the screen, so we can select the image that better represents the region.<br>
</li><li>However, if one image is matched with one region and another image is matched with another region, there is no common base to compare the match scores.<br>
Match scores without a common base are incomparable because a match score is not some absolute measure of similarity. For example, a score of 0.7 cannot be interpreted as 70% similarity between the image and the region.</li></ul>

As an illustration, imagine you want to make apple cake and you send an alien from a different planet to the grocery to buy apples. Unfortunately, the alien doesn't know apples, so you give him two pictures, one with green apples and one with yellow apples. The grocery has green apples and yellow mangoes. The alien looks at the apples and mangoes and compares them to the apples on his two pictures and concludes that the green apples look like green apples and the mangoes look like yellow apples. For some reason the alien thinks that the mangoes look more like yellow apples than the green apples look like the green apples on his picture (maybe they are smaller than those on the picture), and he decides to buy the mangoes. When he comes back with the mangoes, you are angry because you can't make apple cake with mangoes.<br>
<br>
Identifying buttons (and other GUI elements for that matter) using fuzzy image matching can be regarded as a classification problem: Find each button on the screen and determine the most likely button type (i.e. Back, Next or Cancel). An algorithm that compares match scores does not work, as argued above, even with the additional assumption that one of the buttons is the target (i.e. Next) button and all other buttons are of a different type. Applying thresholds to the match scores does not work either, for the same reason (i.e. match scores are not absolute measures of similarity).<br>
<br>
In order to classify buttons, we need knowledge (i.e. images) of all buttons on the screen, not just the button we are interested in. Then we can find all buttons by searching for matches, and determine the type of each button by comparing the scores of matches that cover the same region. In practice, we make an additional assumption: that there is at most one button of each type. With this assumption we can simplify the first step by considering only the best match for each image. The algorithm then looks as follows:<br>
<ol><li>For each image, we find the best match. Some images will match the wrong button, for example the Next image with the thin black border will match the Back button, and the Back image with blue border will match the Next button.<br>
</li><li>For each button we select the match from step 1 with the highest score. This eliminates the wrong matches from step 1 because the Back image with thin black border has a higher match score on the Back button than the Next image with thin black border, and the Next image with blue border has a higher match score than the Back image with blue score on the Next button.<br>
Note that in step 1 we are only comparing matches that use the same image in different regions, while in step 2 we only compare matches that use the same region but different images, so all comparisons are valid. Given that we have an image of each button in each state, the algorithm finds all buttons and correctly identifies the type of each button.</li></ol>

The following code shows an implementation of the algorithm. Here we assume a function 'is_similar_region' that returns True if two regions overlap significantly:<br>
<pre><code>def find_similar_match(match, matches):<br>
    for i, m in enumerate(matches):<br>
        if is_similar_region(match, m):<br>
            return i<br>
    return None<br>
<br>
IMAGES = {<br>
    'back' : ['back.png', 'back_focus.png', 'back_mouse_over.png'],<br>
    'next' : ['next.png', 'next_focus.png', 'next_mouse_over.png'],<br>
    'cancel' : ['cancel.png', 'cancel_focus.png', 'cancel_mouse_over.png']<br>
}<br>
<br>
best_matches = []<br>
button_types = []<br>
<br>
for key in IMAGES.keys():<br>
    for image in IMAGES[key]:<br>
        # find best match for this image<br>
        match = find(image)<br>
<br>
        # is there already a match for the same region?<br>
        index = find_similar_match(match, best_matches)<br>
        if index is not None:<br>
            # save the best match for each region<br>
            if match.getScore() &gt; best_matches[index].getScore():<br>
                best_matches[index] = match<br>
                button_types[index] = key<br>
        else:<br>
            # new region<br>
            best_matches.append(match)<br>
            button_types.append(key)<br>
<br>
# store matches in dictionary object with button type as key<br>
buttons = {}<br>
for key, match in zip(button_types, best_matches):<br>
    if key in buttons:<br>
        raise Exception("button '%s' found twice" % key)<br>
    buttons[key] = match<br>
<br>
# click Next button<br>
if 'next' not in buttons:<br>
    raise Exception('Next button not found')<br>
click(buttons['next'])<br>
</code></pre>
This algorithm is implemented in the <a href='Buttons.md'>Buttons</a> class, with some additional features, such as detecting disabled buttons and waiting for buttons to become enabled.<br>
<br>
<h2>Improving match accuracy and speed</h2>

Experience with Sikuli so far has shown that<br>
<ul><li>larger images are found faster and more reliably than smaller images,<br>
</li><li>restricting the region in which Sikuli searches for matches improves both speed and accuracy,<br>
</li><li>searching for very small images (small icons, checkboxes, radio buttons) in a large area (e.g. on the entire screen) can result in both missed matches (not all occurrences are found) and spurious matches (false matches that look similar to the target image).<br>
Missed matches and spurious matches may be artifacts of the image matching algorithm used by Sikuli. In order to avoid wrong matches and to speed up image search, it is therefore desirable to restrict the search region as much as possible.</li></ul>

Sometimes the target image is in a fixed region on a screen, for example when clicking on an icon in the Windows taskbar. Suppose a script wants to open the Windows start menu:<br>
<br>
<img src='http://rguils.googlecode.com/svn/wiki/images/taskbar.png' />

<pre><code>taskbar_region = Region(0, SCREEN.getH() - 31, SCREEN.getW(), 31)<br>
taskbar_region.click('windows_start.png')<br>
</code></pre>

If the target element is part of an application window, it may not be possible (or desirable) to specify a region beforehand without knowing the location of the application window. In this case the window location has to be determined first, and then the region for finding the target element can be specified relative to the window location.<br>
<br>
Note that Sikuli itself has no concept of an application window. Sikuli treats the screen, or a region of the screen, as an image, but it knows nothing about other applications and their window hierarchies. When Sikuli clicks on a button in a window, it does not actually know anything about the button or the window, or even that a button exists at the point of the click. Here's what happens:<br>
<ol><li>Sikuli creates a mouse click event with the position of the match.<br>
</li><li>The event, along with the position of the click on the screen, is received by the OS (i.e. Windows), which knows the window (if any) that is currently visible on the screen at the click position, and the application that owns the window.<br>
</li><li>The OS propagates the event, along with the window id, to the application.<br>
</li><li>An event listener in the application receives the event and calls an event handler that updates the window state and/or processes the event according to the application logic.<br>
For the purpose of automating an application, this means we cannot rely on Sikuli to obtain information about the (location and size of the) current application window, but we can use Sikuli to find the window on the screen. If the application window contains a logo or some other identifiable image of sufficient size, its location on the screen can be determined relatively reliably by searching for that image. Assuming that the window size is fixed and known, a region can then be created with the size of the window and a location relative to the logo or other image.</li></ol>

<img src='http://rguils.googlecode.com/svn/wiki/images/installerwindow_anchor.png' />

<pre><code>anchor_match = find('applogo.png')<br>
window_region = Region(anchor_match.getX() - 2, anchor_match.getY() - 30, 499, 392)<br>
</code></pre>
If the target element is known to be within a specific part of the application window (e.g. in the title bar or in the lower half of the window), we can create another region as a subregion of the window region and with a location relative to the location of the window region.<br>
<br>
<img src='http://rguils.googlecode.com/svn/wiki/images/installerwindow_regions.png' />

<pre><code># title bar is 30 pixels from the top of the window<br>
titlebar_region = Region(window_region.getX(), window_region.getY(),<br>
                         window_region.getW(), 30)<br>
<br>
# minimize/maximize/close buttons are in an 83 pixel wide region at the far<br>
# right of the title bar<br>
window_button_region = Region(titlebar_region.getX() + titlebar_region.getW()<br>
                   - 83, titlebar_region.getY(), 83, title_bar_region.getH())<br>
<br>
# buttons are in a 48 pixel high region at the bottom of the window<br>
button_region = Region(window_region.getX(), window_region.getY()<br>
                     + window_region.getH() - 48, window_region.getW(), 48)<br>
<br>
# click Next button<br>
button_region.click('next.png')<br>
<br>
# close window<br>
window_button_region.click('window_close_button.png')<br>
</code></pre>
The AnchoredRegion class in the <a href='Util.md'>Util</a> module and the Window and AnchoredWindow classes in the <a href='Window.md'>Window</a> module implement these ideas.<br>
<br>
<h2>Synchronization</h2>

GUI automation with Sikuli involves two applications: the target application that owns the GUI that is being automated, and the automation script that controls the application through its GUI. The automation script acts as a controller and observer:<br>
<ul><li>it creates mouse and keyboard events that are sent to the application by the OS (Windows), to control the application, and<br>
</li><li>it observes changes in the GUI (windows being opened and closed, buttons being enabled or disabled, checkboxes and radio buttons changing their state, etc.), for example to determine the application's current state or to make assertions in automated tests.<br>
Note that the relationship between the target application and the automation script is one-sided: the application does not know (and should not know!) that it is being automated. It cannot (and should not) know whether the input it receives (in the form of mouse and keyboard events as well as other input) was generated by a human handling the mouse and typing on the keyboard, or by an automation script calling some API methods.</li></ul>

This adds an additional challenge, besides that of finding GUI elements on the screen: synchronizing the actions of the automation script with changes in the target application state. Some examples are:<br>
<ul><li>waiting for the application window to appear on the screen after launching an application<br>
</li><li>waiting for the application window to disappear after exiting an application<br>
</li><li>waiting for buttons to become enabled (and thus responsive) after opening a new window<br>
</li><li>waiting for checkboxes and radio buttons to change their state after clicking on them<br>
</li><li>waiting for the application to complete an action after clicking a button<br>
Because of the one-sided relationship, there is no way for the automation script to be notified of a state change by the application. Instead, the automation script has to actively monitor the application.</li></ul>

Since both the target application and the automation script are running on the same machine, they compete for system resources such as CPU time, memory and disk access. If the automation script consumes a lot of resources, this can affect the target application by slowing it down. A human user would not have this impact because a human user does not consume system resources. In addition, the OS also consumes some resources for process and display management.<br>
<br>
All this can make the application's GUI less responsive than it would be in normal (i.e. non automated) use. Running target applications and/or automation on remote machines (via <a href='http://msdn.microsoft.com/en-us/library/aa383015(VS.85).aspx'>RDP</a> sessions) can increase slowness even further.<br>
<br>
Experience has shown that there can be a considerable time lag between Sikuli generating a mouse click event and the application receiving the event and updating its GUI. Even in such simple instances as clicking a checkbox it may take several seconds before the checkbox is actually checked. The delay can become arbitrarily longer if other applications running simultaneously increase the CPU load on the machine, or RDP is used over a slow network connection. Putting the automation script to sleep for a fixed amount of time to allow the application to update its GUI after a mouse click is therefore not robust; increasing the wait time to make it more robust would slow down automated tests unnecessarily by wasting time.<br>
<br>
When acting on a GUI element, it is therefore mandatory to monitor the GUI element and verify that its state has changed as expected by the action, before proceeding with the automation sequence. For example, an automation script that clicks on a checkbox that was previously unchecked must verify that the checkbox is actually checked after generating the click event. It can do so by defining a narrow region around the checkbox and searching in that area using an image of a checked checkbox and another of an unchecked checkbox. If the unchecked image yields a better match (i.e. a higher match score) or the checked image is not found at all, the checkbox is still unchecked. Only when the checked image yields a better match can the script assume that the click event has been propagated to the application and the checkbox has been updated.<br>
<pre><code># click on the checkbox<br>
checkbox = find('checkbox_unchecked.png')<br>
click(checkbox)<br>
<br>
# verify that the checkbox has been checked<br>
<br>
# create a region around the checkbox<br>
checkbox_region = checkbox.nearby(10)<br>
<br>
checked = False<br>
waited = 0<br>
while not checked and waited &lt; TIMEOUT:<br>
<br>
    # get match score for checked checkbox image<br>
    try:<br>
        m_checked = checkbox_region.find('checkbox_checked.png')<br>
        s_checked = m_checked.getScore()<br>
    except FindFailed:<br>
        s_checked = 0<br>
<br>
    # get match score for unchecked checkbox image<br>
    try:<br>
        m_unchecked = checkbox_region.find('checkbox_unchecked.png')<br>
        s_unchecked = m_unchecked.getScore()<br>
    except FindFailed:<br>
        s_unchecked = 0<br>
<br>
    checked = s_checked &gt; s_unchecked<br>
<br>
    # wait for 1 second if checkbox has not updated its state, then try again<br>
    if not checked:<br>
        t = min(1, TIMEOUT - waited)<br>
        sleep(t)<br>
        waited += t<br>
<br>
if not checked:<br>
    raise Exception('checkbox was not checked after %d seconds' % TIMEOUT)<br>
</code></pre>
The <a href='Checkboxes.md'>Checkboxes</a> module supports verification for checkboxes and radio buttons.