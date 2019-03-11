---
layout: default
sidebar: yes
permalink: /docs/studio/Scripts/
---


For more advanced dynamic runtime behaviour, we can write scripts (actually Rules are a thin layer on top of scripts).
With scripts we can write arbitrary logic that can dynamically manipulate just about any combination of properties for
a widget.

Yamcs Studio supports two dynamic languages: JavaScript and Python. Both languages can be used to the same effect, and
are available without any external dependencies. As of now, there is no advanced editor support bundled with Yamcs
Studio though, so scripts are edited with a plain text editor.

Additional, both from JavaScript and Python scripts, Java code can called from within the script by importing the
desired Java packages.


## Create a script for a widget


1. Edit the **Scripts** property to pop up this dialog.

![Attach a Script](/assets/studio/attach-scripts.png){: .center-image }

1. Clicking the plus icon gives allows to select one or more scripts that will be executed. A script definition can be
embedded in the display which is an acceptable solution for simple scripts. Else scripts are defined in their own
javascript or python file, stored in the project workspace.

1. In the In the right **Input PVs** table add your input PV(s). The script(s) will be triggered when the PV receives
an udpate. At least one PV must be set as **Trigger** for the script to be executed.

1. The **Options** tab provides some options for specific cases:
- **Skip executes triggered by PVs' first value.** will prevent the execution of the sript on the first update of the
PVs.
- **Execute anyway even if some PVs are diconnected**: by default the scripts are not executed if some of the input PVs
are disconnected.
- **Do not execute the script if erro was detected**: by default if an execution of the script fails, the scrip is
executed again when a PV update is received.





## Access Widgets
To access and control the widgets of the OPI displays, two special objects are defined by the CSStudio layer:
**widget** and **display**.
This section contains the related documentation.

**widget object**

The widget to which a script is attached can be accessed in the script via widget object. Widget object provides the
methods to get or set any of its properties, store external objects or provide special methods for a particular widget.

**display object**

The widget controller of the display is accessible in all scripts as display object. To get the controller of any
widget in the display, you can call its method getWidget(name). For example:

<pre><code>display.getWidget("myLabel").setPropertyValue("x", 20); //set x position of the widget myLabel to 20.</code></pre>


**Common methods to all widgets**

- getPropertyValue(prop_id)
- setPropertyValue(prop_id, value)
- setPropertyValue(prop_id, value, forceFire)
- getPVByName(pvName)
- setVar(varName, varValue)
- getVar(name)
- getMacroValue(macroName)
- executeAction(index)
- setEnabled(enable)
- setVisible(visible)
- setX(x)
- setY(y)
- setWidth(width)
- setHeight(height)

**Common methods to all container widgets**

Container widgets includes Display, Grouping Container, Linking Container and Tabbed Container.

- getChild(name)
- getWidget(name)
- addChild(model)
- addChildToRight(model)
- addChildToBottom(model)
- removeChildByName(name)
- removeChild(widget)
- removeChild(index)
- removeAllChildren()
- performAutosize()
- getValue()
- setValue()


**Common methods to all PV widgets**

Any widget that has PV Name property is PV widget. For example, Text Update, Combo Box, XY Graph and so on.

- getPV()
- getPV(pvPropId)
- getValue()
- setValue(value)
- setValueInUIThread(value)



## Access PV

**pvs object**

The input PVs for a script can be accessed in script via pvs object. The order of the input PVs in the configuation
list is preserved in pvs. pvs[0] is the first input pv. If you have N input PVs, pvs[N-1] is the last input PV.

You may also able to get the PV attached to a PV widget via widget.getPV().

In script, you can read/write PV or get its timestamp or severity via the utility APIs provided in PVUtil

**triggerPV object**

The PV that triggers the execution of the script can be accessed via triggerPV object. When there are more than one
trigger PV for a script and you need to know this execution is triggered by which PV, you can use this object. For
example,

<pre><code>
if(triggerPV == pvs[1])
    ConsoleUtil.writeInfo("I'm triggered by the second input PV.");
</code></pre>

**Examples:**

1. Get double value from PV

<pre><code>
var value = PVUtil.getDouble(pvs[0]);
widget.setPropertyValue("start_angle", value);
</code></pre>

2. Write PV Value
If writing a PV is forbidden by PV security, an exception will be thrown and shown in console. The method
PV.setValue(data) accepts Double, Double[], Integer, String.

<pre><code>
pvs[0].setValue(0);
</code></pre>


3. Get severity of PV


<pre><code>
                var RED = ColorFontUtil.RED;
                var ORANGE = ColorFontUtil.getColorFromRGB(255,255,0);
                var GREEN = ColorFontUtil.getColorFromHSB(120.0,1.0,1.0);
                var PINK = ColorFontUtil.PINK;

                var severity = PVUtil.getSeverity(pvs[0]);
                var color;

                switch(severity){
                    case 0:  //OK
                        color = GREEN;
                        break;
                    case 1:  //MAJOR
                        color = RED;
                        break;
                    case 2:  //MINOR
                        color = ORANGE;
                        break;
                    case -1:  //INVALID
                    default:
                        color = PINK;
                }

                widget.setPropertyValue("foreground_color",color);
</code></pre>



## Scripts Default Packages

To ease the implementation of scripts, a number of APIs are provided in the packages <em>org.csstudio.opibuilder.scriptUtil</em>
and <em>org.yamcs.studio.script</em>. Those packages are imported by default in each stript and there is no
need to import them at the begining of a script.

This section lists the methods available in the package <em>org.yamcs.studio.script</em> via the following class:
- Yamcs

via the package <em>org.csstudio.opibuilder.scriptUtil</em>, provided by the CSStudio layer, in the following classes:
- PVUtil
- ColorFontUtil
- DataUtil
- ScriptUtil
- FileUtil
- WidgetUtil
- GUIUtil
- ConsoleUtil


### Yamcs

<strong>Method Summary</strong>

<table class="memberSummary" border="1" cellpadding="3" cellspacing="0">
    <tbody>
        <tr>
            <th class="colFirst" scope="col">Modifier and Type</th>
            <th class="colLast" scope="col">Method and Description</th>
        </tr>
        <tr id="i18" class="altColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><span class="memberNameLink">issueCommand</span>(java.lang.String&nbsp;commandSource)</code></pre>
                <div class="block">Issue a telecommand via the current Yamcs instance.<br />
                    Usage example: <em>Yamcs.issueCommand('/YSS/SIMULATOR/SWITCH_VOLTAGE_ON(voltage_num: 1)')</em>
                </div>
            </td>
        </tr>
    </tbody>
</table>

### PVUtil
The utility class to facilitate Javascript programming for PV operation.

<strong>Method Summary</strong>

<table class="memberSummary" border="1" cellpadding="3" cellspacing="0">
    <tbody>
        <tr>
            <th class="colFirst" scope="col">Modifier and Type</th>
            <th class="colLast" scope="col">Method and Description</th>
        </tr>
        <tr id="i0" class="altColor">
            <td class="colFirst">
                <pre><code>static org.csstudio.simplepv.IPV</code></pre>
            </td>
            <td class="colLast">
                <pre><code><span class="memberNameLink"><a href="#">createPV</a></span>(java.lang.String&nbsp;name,
        org.csstudio.opibuilder.editparts.AbstractBaseEditPart&nbsp;widget)</code></pre>
                <div class="block">Create a PV and start it.</div>
            </td>
        </tr>
        <tr id="i1" class="rowColor">
            <td class="colFirst">
                <pre><code>static double</code></pre>
            </td>
            <td class="colLast">
                <pre><code><span class="memberNameLink"><a href="#">getDouble</a></span>(org.csstudio.simplepv.IPV&nbsp;pv)</code></pre>
                <div class="block">Try to get a double number from the PV.</div>
            </td>
        </tr>
        <tr id="i2" class="altColor">
            <td class="colFirst">
                <pre><code>static double</code></pre>
            </td>
            <td class="colLast">
                <pre><code><span class="memberNameLink"><a href="#">getDouble</a></span>(org.csstudio.simplepv.IPV&nbsp;pv,
         int&nbsp;index)</code></pre>
                <div class="block">Try to get a double-typed array element from the Value.</div>
            </td>
        </tr>
        <tr id="i3" class="rowColor">
            <td class="colFirst">
                <pre><code>static double[]</code></pre>
            </td>
            <td class="colLast">
                <pre><code><span class="memberNameLink"><a href="#">getDoubleArray</a></span>(org.csstudio.simplepv.IPV&nbsp;pv)</code></pre>
                <div class="block">Try to get a double-typed array from the pv.</div>
            </td>
        </tr>
        <tr id="i4" class="altColor">
            <td class="colFirst">
                <pre><code>static java.lang.String</code></pre>
            </td>
            <td class="colLast">
                <pre><code><span class="memberNameLink"><a href="#">getFullString</a></span>(org.csstudio.simplepv.IPV&nbsp;pv)</code></pre>
                <div class="block">Get the full info from the pv in this format</div>
            </td>
        </tr>
        <tr id="i5" class="rowColor">
            <td class="colFirst">
                <pre><code>static java.lang.String[]</code></pre>
            </td>
            <td class="colLast">
                <pre><code><span class="memberNameLink"><a href="#">getLabels</a></span>(org.csstudio.simplepv.IPV&nbsp;pv)</code></pre>
                <div class="block">Get the list of Enum values</div>
            </td>
        </tr>
        <tr id="i6" class="altColor">
            <td class="colFirst">
                <pre><code>static java.lang.Long</code></pre>
            </td>
            <td class="colLast">
                <pre><code><span class="memberNameLink"><a href="#">getLong</a></span>(org.csstudio.simplepv.IPV&nbsp;pv)</code></pre>
                <div class="block">Try to get a long integer number from the PV.</div>
            </td>
        </tr>
        <tr id="i7" class="rowColor">
            <td class="colFirst">
                <pre><code>static long[]</code></pre>
            </td>
            <td class="colLast">
                <pre><code><span class="memberNameLink"><a href="#">getLongArray</a></span>(org.csstudio.simplepv.IPV&nbsp;pv)</code></pre>
                <div class="block">Try to get an integer-typed array from the pv.</div>
            </td>
        </tr>
        <tr id="i8" class="altColor">
            <td class="colFirst">
                <pre><code>static int</code></pre>
            </td>
            <td class="colLast">
                <pre><code><span class="memberNameLink"><a href="#">getSeverity</a></span>(org.csstudio.simplepv.IPV&nbsp;pv)</code></pre>
                <div class="block">Get severity of the pv as an integer value.</div>
            </td>
        </tr>
        <tr id="i9" class="rowColor">
            <td class="colFirst">
                <pre><code>static java.lang.String</code></pre>
            </td>
            <td class="colLast">
                <pre><code><span class="memberNameLink"><a href="#">getSeverityString</a></span>(org.csstudio.simplepv.IPV&nbsp;pv)</code></pre>
                <div class="block">Get severity of the PV as a string.</div>
            </td>
        </tr>
        <tr id="i10" class="altColor">
            <td class="colFirst">
                <pre><code>static double</code></pre>
            </td>
            <td class="colLast">
                <pre><code><span class="memberNameLink"><a href="#">getSize</a></span>(org.csstudio.simplepv.IPV&nbsp;pv)</code></pre>
                <div class="block">Get the size of the pv's value</div>
            </td>
        </tr>
        <tr id="i11" class="rowColor">
            <td class="colFirst">
                <pre><code>static java.lang.String</code></pre>
            </td>
            <td class="colLast">
                <pre><code><span class="memberNameLink"><a href="#">getStatus</a></span>(org.csstudio.simplepv.IPV&nbsp;pv)</code></pre>
                <div class="block">Get the status text that might describe the severity.</div>
            </td>
        </tr>
        <tr id="i12" class="altColor">
            <td class="colFirst">
                <pre><code>static java.lang.String</code></pre>
            </td>
            <td class="colLast">
                <pre><code><span class="memberNameLink"><a href="#">getString</a></span>(org.csstudio.simplepv.IPV&nbsp;pv)</code></pre>
                <div class="block">Converts the given pv's value into a string representation.</div>
            </td>
        </tr>
        <tr id="i13" class="rowColor">
            <td class="colFirst">
                <pre><code>static java.lang.String[]</code></pre>
            </td>
            <td class="colLast">
                <pre><code><span class="memberNameLink"><a href="#">getStringArray</a></span>(org.csstudio.simplepv.IPV&nbsp;pv)</code></pre>
                <div class="block">Get string array from pv.</div>
            </td>
        </tr>
        <tr id="i14" class="altColor">
            <td class="colFirst">
                <pre><code>static double</code></pre>
            </td>
            <td class="colLast">
                <pre><code><span class="memberNameLink"><a href="#">getTimeInMilliseconds</a></span>(org.csstudio.simplepv.IPV&nbsp;pv)</code></pre>
                <div class="block">Get milliseconds since epoch, i.e.</div>
            </td>
        </tr>
        <tr id="i15" class="rowColor">
            <td class="colFirst">
                <pre><code>static java.lang.String</code></pre>
            </td>
            <td class="colLast">
                <pre><code><span class="memberNameLink"><a href="#">getTimeString</a></span>(org.csstudio.simplepv.IPV&nbsp;pv)</code></pre>
                <div class="block">Get the timestamp string of the pv</div>
            </td>
        </tr>
        <tr id="i16" class="altColor">
            <td class="colFirst">
                <pre><code>static java.lang.String</code></pre>
            </td>
            <td class="colLast">
                <pre><code><span class="memberNameLink"><a href="#">getTimeString</a></span>(org.csstudio.simplepv.IPV&nbsp;pv,
             java.lang.String&nbsp;formatPattern)</code></pre>
                <div class="block">Get the timestamp string of the pv</div>
            </td>
        </tr>
        <tr id="i17" class="rowColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><span class="memberNameLink"><a href="#">writePV</a></span>(java.lang.String&nbsp;pvName,
       java.lang.Object&nbsp;value)</code></pre>
                <div class="block">Write a PV in a background job.</div>
            </td>
        </tr>
        <tr id="i18" class="altColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><span class="memberNameLink"><a href="#">writePV</a></span>(java.lang.String&nbsp;pvName,
       java.lang.Object&nbsp;value,
       int&nbsp;timeout)</code></pre>
                <div class="block">Write a PV in a background job.</div>
            </td>
        </tr>
    </tbody>
</table>

### ColorFontUtil
Utility class to facilitate Javascript programming for color operation.

<strong>Field Summary</strong>
<table class="overviewSummary" border="1" cellpadding="3" cellspacing="0">
    <tbody>
        <tr>
            <th class="colFirst" scope="col">Modifier and Type</th>
            <th class="colLast" scope="col">Field and Description</th>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static org.eclipse.swt.graphics.RGB</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">BLACK</a></strong></code></pre>
                <div class="block">the color of black</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static org.eclipse.swt.graphics.RGB</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">BLUE</a></strong></code></pre>
                <div class="block">the color of blue</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static org.eclipse.swt.graphics.RGB</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">CYAN</a></strong></code></pre>
                <div class="block">the color of cyan</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static org.eclipse.swt.graphics.RGB</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">DARK_GRAY</a></strong></code></pre>
                <div class="block">the color of dark gray</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static org.eclipse.swt.graphics.RGB</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">GRAY</a></strong></code></pre>
                <div class="block">the color of gray</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static org.eclipse.swt.graphics.RGB</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">GREEN</a></strong></code></pre>
                <div class="block">the color of green</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static org.eclipse.swt.graphics.RGB</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">LIGHT_BLUE</a></strong></code></pre>
                <div class="block">the color of light blue</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static org.eclipse.swt.graphics.RGB</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">ORANGE</a></strong></code></pre>
                <div class="block">the color of orange</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static org.eclipse.swt.graphics.RGB</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">PINK</a></strong></code></pre>
                <div class="block">the color of pink</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static org.eclipse.swt.graphics.RGB</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">PURPLE</a></strong></code></pre>
                <div class="block">the color of orange</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static org.eclipse.swt.graphics.RGB</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">RED</a></strong></code></pre>
                <div class="block">the color of red</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static org.eclipse.swt.graphics.RGB</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">WHITE</a></strong></code></pre>
                <div class="block">the color of white</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static org.eclipse.swt.graphics.RGB</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">YELLOW</a></strong></code></pre>
                <div class="block">the color of yellow</div>
            </td>
        </tr>
    </tbody>
</table>

<strong>Method Summary</strong>
<table class="overviewSummary" border="1" cellpadding="3" cellspacing="0">
    <tbody>
        <tr>
            <th class="colFirst" scope="col">Modifier and Type</th>
            <th class="colLast" scope="col">Method and Description</th>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static org.eclipse.swt.graphics.RGB</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">getColorFromHSB</a></strong>(float&nbsp;hue,
               float&nbsp;saturation,
               float&nbsp;brightness)</code></pre>
                <div class="block">Get a color with the given
                    hue, saturation, and brightness.</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static org.eclipse.swt.graphics.RGB</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">getColorFromRGB</a></strong>(int&nbsp;red,
               int&nbsp;green,
               int&nbsp;blue)</code></pre>
                <div class="block">Get a color with the given
                    red, green and blue values.</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static org.eclipse.swt.graphics.FontData</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">getFont</a></strong>(java.lang.String&nbsp;name,
       int&nbsp;height,
       int&nbsp;style)</code></pre>
                <div class="block">Get a new font data given a font name,
                    the height of the desired font in points,
                    and a font style.</div>
            </td>
        </tr>
    </tbody>
</table>


### DataUtil
Utility class to facilitate Javascript programming for data operation.

<strong>Method Summary</strong>
<table class="overviewSummary" border="1" cellpadding="3" cellspacing="0">
    <tbody>
        <tr>
            <th class="colFirst" scope="col">Modifier and Type</th>
            <th class="colLast" scope="col">Method and Description</th>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static double[]</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">createDoubleArray</a></strong>(int&nbsp;size)</code></pre>
                <div class="block">Create a new double array with given size.</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static int[]</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">createIntArray</a></strong>(int&nbsp;size)</code></pre>
                <div class="block">Create a new int array with given size.</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static org.csstudio.opibuilder.util.MacrosInput</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">createMacrosInput</a></strong>(boolean&nbsp;include_parent_macros)</code></pre>
                <div class="block">Create a MacrosInput, which can be used as the macros input for a container widget
                    or display.</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static double[]</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">toJavaDoubleArray</a></strong>(org.mozilla.javascript.NativeArray&nbsp;jsArray)</code></pre>
                <div class="block">Convert JavaScript array to Java double array.</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static int[]</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">toJavaIntArray</a></strong>(org.mozilla.javascript.NativeArray&nbsp;jsArray)</code></pre>
                <div class="block">Convert JavaScript array to Java int array.</div>
            </td>
        </tr>
    </tbody>
</table>

### ScriptUtil
The utility class to facilitate OPI script programming.

<strong>Method Summary</strong>
<table class="overviewSummary" border="1" cellpadding="3" cellspacing="0">
    <tbody>
        <tr>
            <th class="colFirst" scope="col">Modifier and Type</th>
            <th class="colLast" scope="col">Method and Description</th>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><a href="#">closeCurrentOPI</a></strong>()</code></pre>
                <div class="block">Close current active OPI.</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><a href="#">execInUI</a></strong>(java.lang.Runnable&nbsp;runnable,org.csstudio.opibuilder.editparts.AbstractBaseEditPart&nbsp;widget)</code></pre>
                <div class="block">Execute a runnable in UI thread.</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><a href="#">executeEclipseCommand</a></strong>(java.lang.String&nbsp;commandId)</code></pre>
                <div class="block">Execute an Eclipse command.</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><a href="#">executeSystemCommand</a></strong>(java.lang.String&nbsp;command,int&nbsp;wait)</code></pre>
                <div class="block">Executing a system or shell command.</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static org.osgi.framework.Version</code></pre>
            </td>
            <td class="colLast">
                <pre><code><a href="#">getBOYVersion</a></strong>()</code></pre>&nbsp;</td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static boolean</code></pre>
            </td>
            <td class="colLast">
                <pre><code><a href="#">isMobile</a></strong>()</code></pre>
                <div class="block">If the current OPI is running on Mobile device.</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static boolean</code></pre>
            </td>
            <td class="colLast">
                <pre><code><a href="#">isMobile</a></strong>(org.csstudio.opibuilder.editparts.AbstractBaseEditPart&nbsp;widget)</code></pre>
                <div class="block">If the current OPI is running on
                    Mobile device.</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static boolean</code></pre>
            </td>
            <td class="colLast">
                <pre><code><a href="#">isWebOPI</a></strong>()</code></pre>&nbsp;</td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><a href="#">makeElogEntry</a></strong>(java.lang.String&nbsp;filePath)
                                        </code></pre>
                <div class="block">
                    <pre><code>Deprecated</code></pre>
                    see <a href="#">
                        <pre><code>makeLogbookEntry(String, String)</code></pre></a>
                </div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><a href="#">makeLogbookEntry</a></strong>(java.lang.String&nbsp;text, java.lang.String&nbsp;filePath)</code></pre>
                <div class="block">Pop up a logbook dialog to make a
                    logbook entry.</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><a href="#">openOPI</a></strong>(org.csstudio.opibuilder.editparts.AbstractBaseEditPart&nbsp;widget,
                                            java.lang.String&nbsp;opiPath, int&nbsp;target,
                                            org.csstudio.opibuilder.util.MacrosInput&nbsp;macrosInput)</code></pre>
                <div class="block">Open an OPI.</div>
            </td>
        </tr>
    </tbody>
</table>

### FileUtil
The Utility class to help file operating.

<strong>Method Summary</strong>
<table class="overviewSummary" border="1" cellpadding="3" cellspacing="0">
    <tbody>
        <tr>
            <th class="colFirst" scope="col">Modifier and Type</th>
            <th class="colLast" scope="col">Method and Description</th>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static java.io.InputStream</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">getInputStreamFromFile</a></strong>(java.lang.String&nbsp;filePath,
                      org.csstudio.opibuilder.editparts.AbstractBaseEditPart&nbsp;widget)</code></pre>
                <div class="block">Return an
                    <pre><code>InputStream</code></pre> of the file on the specified path.</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static org.jdom.Element</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">loadXMLFile</a></strong>(java.lang.String&nbsp;filePath)</code></pre>
                <div class="block">Load the root element of an XML file.</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static org.jdom.Element</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">loadXMLFile</a></strong>(java.lang.String&nbsp;filePath,
           org.csstudio.opibuilder.editparts.AbstractBaseEditPart&nbsp;widget)</code></pre>
                <div class="block">Load the root element of an XML file.</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">openFile</a></strong>(java.lang.String&nbsp;filePath,
        org.csstudio.opibuilder.editparts.AbstractBaseEditPart&nbsp;widget)</code></pre>
                <div class="block">Open a file in default editor.</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static java.lang.String</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">openFileDialog</a></strong>(boolean&nbsp;inWorkspace)</code></pre>
                <div class="block">Open a file select dialog.</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">openWebPage</a></strong>(java.lang.String&nbsp;link)</code></pre>
                <div class="block">Open a web page.</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">playWavFile</a></strong>(java.lang.String&nbsp;filePath,
           org.csstudio.opibuilder.editparts.AbstractBaseEditPart&nbsp;widget)</code></pre>
                <div class="block">Play a .wav file.</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static java.lang.String</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">readTextFile</a></strong>(java.lang.String&nbsp;filePath)</code></pre>
                <div class="block">Read a text file.</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static java.lang.String</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">readTextFile</a></strong>(java.lang.String&nbsp;filePath,
            org.csstudio.opibuilder.editparts.AbstractBaseEditPart&nbsp;widget)</code></pre>
                <div class="block">Read a text file.</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static java.lang.String</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">saveFileDialog</a></strong>(boolean&nbsp;inWorkspace)</code></pre>
                <div class="block">Open a file save dialog.</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static java.lang.String</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">workspacePathToSysPath</a></strong>(java.lang.String&nbsp;workspacePath)</code></pre>
                <div class="block">Convert a workspace path to system path.</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">writeTextFile</a></strong>(java.lang.String&nbsp;filePath,
             boolean&nbsp;inWorkspace,
             org.csstudio.opibuilder.editparts.AbstractBaseEditPart&nbsp;widget,
             java.lang.String&nbsp;text,
             boolean&nbsp;append)</code></pre>
                <div class="block">Write a text file.</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">writeTextFile</a></strong>(java.lang.String&nbsp;filePath,
             boolean&nbsp;inWorkspace,
             java.lang.String&nbsp;text,
             boolean&nbsp;append)</code></pre>
                <div class="block">Write a text file.</div>
            </td>
        </tr>
    </tbody>
</table>

### WidgetUtil
The Utility Class to help managing widgets.

<strong>Method Summary</strong>
<table class="overviewSummary" border="1" cellpadding="3" cellspacing="0">
    <tbody>
        <tr>
            <th class="colFirst" scope="col">Modifier and Type</th>
            <th class="colLast" scope="col">Method and Description</th>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static org.csstudio.opibuilder.model.AbstractWidgetModel</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">createWidgetModel</a></strong>(java.lang.String&nbsp;widgetTypeID)</code></pre>
                <div class="block">Create a new widget model with the give widget type ID.</div>
            </td>
        </tr>
    </tbody>
</table>

### GUIUtil
The utility class to facilitate script programming in GUI operation.

<strong>Method Summary</strong>
<table class="overviewSummary" border="1" cellpadding="3" cellspacing="0">
    <tbody>
        <tr>
            <th class="colFirst" scope="col">Modifier and Type</th>
            <th class="colLast" scope="col">Method and Description</th>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">compactMode</a></strong>()</code></pre>
                <div class="block">Enter or exit compact mode.</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">fullScreen</a></strong>()</code></pre>
                <div class="block">Enter or exit full screen.</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static boolean</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">openConfirmDialog</a></strong>(java.lang.String&nbsp;dialogMessage)</code></pre>
                <div class="block">Open a dialog to ask for confirmation.</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static boolean</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">openPasswordDialog</a></strong>(java.lang.String&nbsp;dialogMessage,
                  java.lang.String&nbsp;password)</code></pre>
                <div class="block">Open a password dialog to allow user to input password.</div>
            </td>
        </tr>
    </tbody>
</table>


### ConsoleUtil
The Utility Class to help write information to CSS console.

<strong>Method Summary</strong>
<table class="overviewSummary" border="1" cellpadding="3" cellspacing="0">
    <tbody>
        <tr>
            <th class="colFirst" scope="col">Modifier and Type</th>
            <th class="colLast" scope="col">Method and Description</th>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">writeError</a></strong>(java.lang.String&nbsp;message)</code></pre>
                <div class="block">Write Error information to CSS console.</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">writeInfo</a></strong>(java.lang.String&nbsp;message)</code></pre>
                <div class="block">Write information to CSS console.</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">writeString</a></strong>(java.lang.String&nbsp;string)</code></pre>
                <div class="block">Write pure string to CSS console without any extra headers in black color.</div>
            </td>
        </tr>
        <tr class="rowColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">writeString</a></strong>(java.lang.String&nbsp;string,
           int&nbsp;red,
           int&nbsp;green,
           int&nbsp;blue)</code></pre>
                <div class="block">Write pure string to CSS console in specified color.</div>
            </td>
        </tr>
        <tr class="altColor">
            <td class="colFirst">
                <pre><code>static void</code></pre>
            </td>
            <td class="colLast">
                <pre><code><strong><a href="#">writeWarning</a></strong>(java.lang.String&nbsp;message)</code></pre>
                <div class="block">Write Warning information to CSS console.</div>
            </td>
        </tr>
    </tbody>
</table>



## Examples

Some examples of display scripts are provided below, covering some common use cases:
- [Action Telecommand](Example_Action_Telecommand/)
- [Update Widget Properties](Example_Update_Widget_Properties/)
- [Java Calls](Example_Java/)
