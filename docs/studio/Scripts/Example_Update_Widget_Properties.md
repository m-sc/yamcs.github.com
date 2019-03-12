---
layout: default
sidebar: yes
permalink: /docs/studio/Scripts/Example_Update_Widget_Properties/
---

The script below is triggered from a PV that provide Strings with value *DISABLED*, *OK* or not *NOK*.
The script retreived the value of the input PV as a string and set the LED value accordingly.
The tooltip value is also udpated.

<pre>
<code>
var v = PVUtil.getString(pvs[0]);
if (v == "DISABLED") {
    widget.setValue(1.0);
} else if (v == "OK") {
    widget.setValue(2.0);
} else {
    widget.setValue(3.0);
}

widget.setPropertyValue("tooltip", v);
</code>
</pre>


With the LED widget defined as followed:

![Action Telecommand](/assets/studio/Example-script-led.png){: .center-image }
