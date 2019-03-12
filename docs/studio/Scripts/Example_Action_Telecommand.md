---
layout: default
sidebar: yes
permalink: /docs/studio/Scripts/Example_Action_Telecommand/
---



These section presents two examples showing how to send a Yamcs telecommand from an Action button in an OPI display.

The first example shows how to send a simple predefined telecommand, which would have been generated from the command stack.

The second example shows how to retrieve a telecommand argument from another widget in the display and to send the telecommand.


## Simple Telecommand

1. Create a telecommand in the view "Command Stack". Right click and select "Copy". The telecommand source is saved in the clipboard and will be used when creating the action button.

    ![Retreive Telecommand Source](/assets/studio/command-source.png){: .center-image }

1. In the display builder, create an Action button. Add an action of type **Javascript**, activate the property **Embedded**.

1. In the window **Edit Script Text**, enter the instruction 'Yamcs.issueCommand("")', and paste the content of the clipboard insert the command source as a string. The result should be similart to:

    ![Action Telecommand](/assets/studio/action-telecommand.png){: .center-image }

1. Save the display. In the display runner, refresh the display and try the new action button. A command is inserted in the view "Command History" with the defined command source content.



## Retrieve Telcommand argument from Another Widget

1. Create a dropdown widget in the display. Give it a name (e.g. *ComboVoltageNumber*) and a local pv name (e.g. *loc://voltage_number*) to store the selection.

    ![Telecommand Combo](/assets/studio/telecommand-combo-argument.png){: .center-image }

1. Create the action button as in example 1, and add the following script to retreive the pv value from the combo box, and append it to the telecommand source:
    <pre>
    <code>
    var voltage_number = parseInt(display.getWidget('ComboVoltageNumber').getValue())
    Yamcs.issueCommand("/YSS/SIMULATOR/SWITCH_VOLTAGE_ON(voltage_num: "+voltage_number+")")
    </code>
    </pre>

    **Note**: when the scripts get more complex, it is recommended to defined them in their own file to ease their edition and maintenance.


1. Save the display. In the display runner, refresh the display, select a value in the combo widget and try the action button. A command is inserted in the view "Command History" with the selected voltage as argument.


