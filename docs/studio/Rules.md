---
layout: default
sidebar: yes
permalink: /docs/studio/Rules/
---

Making OPI displays is flexible using the [Palette](/docs/studio/Palette/) and the [Properties](/docs/studio/Properties/), but the resulting displays are still fairly static. But what if we want to make the position of a widget dynamic based on a PV? Or if we want to dynamically change widget colors?

### Rules
You may have noticed that every widget has a **Rules** property. Rules are a user-friendly way for adding dynamic behaviour to widgets. They are most often used for changing the widget's properties at runtime.

#### Example
This is best explained with an example. Suppose we have the bright idea to make an LED square when it is off, and round when it is on. The static properties would not allow for such a scenario, we therefore add a rule.

1. Edit the **Rules** property to pop up this dialog. 

    ![Attach a Rule](/assets/studio/attach-rules.png){: .center-image }

1. Clicking the plus icon gives you this dialog:

    ![Edit Rule](/assets/studio/edit-rule.png){: .center-image }

1. The first thing to choose is the rule's target **Property**. So select **Square LED**.

1. In the right **Input PVs** table add your input PV. In this example we chose to generate an alternating 0/1 value using a simulated PV. Notice the sequential number in the <tt>#</tt> column. The first PV is numbered <tt>0</tt>. Make sure to check the <tt>Trigger</tt> checkbox as this will then trigger the execution of the rule whenever the PV's value is updated.

1. Now in the **Expressions** table, fill in your conditions in the **Boolean Expression** column, and add the desired value of the rule's property in the **Output Value** column. The double value of the top-most right PV is available as the variable <tt>pv0</tt>. The next PV in the list (if applicable) is available as the variable <tt>pv1</tt>, etc.

    ![Example Rule](/assets/studio/rule-example.png){: .center-image }

1. Confirm your dialogs, save your display and refresh a runtime view of it. You should see the LED's shape now alternating between square and ground.

One can see that this example could be made arbitrarily complex by adding more rules and/or expressions.

#### Boolean Expression
This input field needs to be expressed in JavaScript. The **Input PVs** are available in different formats:

<table class="inline">
    <tr>
        <th width="1">Type</th>
        <th>Example</th>
    </tr>
    <tr>
        <td style="white-space: nowrap;">Double Value</td>
        <td class="code">pv0 == 2.2</td>
    </tr>
    <tr>
        <td style="white-space: nowrap;">String Value</td>
        <td class="code">pvStr0 != 'abc'</td>
    </tr>
    <tr>
        <td style="white-space: nowrap;">Integer Value</td>
        <td class="code">pvInt0 &gt;= 2</td>
    </tr>
</table> 

In addition, you can access the numeric alarm state of an input PV.

<table class="inline">
    <tr>
        <th width="1">Alarm</th>
        <th>Example</th>
    </tr>
    <tr>
        <td>Invalid</td>
        <td class="code">pvSev0 == -1</td>
    </tr>
    <tr>
        <td style="white-space: nowrap">No Alarm</td>
        <td class="code">pvSev0 == 0</td>
    </tr>
    <tr>
        <td style="white-space: nowrap">Minor Alarm</td>
        <td class="code">pvSev0 == 1</td>
    </tr>
    <tr>
        <td style="white-space: nowrap">Major Alarm</td>
        <td class="code">pvSev0 == 2</td>
    </tr>
</table>

{% hint %}
  If you wish to set a property value that always applies, use <tt>true</tt> (or <tt>1==1</tt>) as the Boolean Expression.
{% endhint %}

#### Output Value
The exact form that the **Output Value** column adopts depends on the type of the property. Some properties are colors, so you would see a color picker, other properties expect text, and the above example was a boolean yes/no, so we got a checkbox.

