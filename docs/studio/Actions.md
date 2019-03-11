---
layout: default
sidebar: yes
permalink: /docs/studio/Actions/
---

Widgets have an **Actions** property which is used to trigger actions upon user interaction. The common case is to associate Actions with [Control Widgets](/docs/studio/Control_Widgets/), but in principle it can be made to work with other widgets as well.

![Sample Action](/assets/studio/open-opi-action.png){: .center-image }

The list of available actions currently includes:

<table class="inline">
    <tr>
        <th>Open OPI</th>
        <td>
            <p>Often used in combination with <a href="/docs/studio/Control_Widgets/#action-button">Action Buttons</a> to organize displays hierarchically.</p>
            <p>Indicate the workspace path to the OPI with <tt>File Path</tt>.</p>
            <p>Use the <tt>Mode</tt> to select whether the OPI should by default open in the same tab. Note that the runtime user can override this default behaviour by right-clicking the button.</p>
        </td>
    </tr>
    <tr>
        <th>Write PV</th>
        <td>Writes the specified value to a PV. The variable <tt>$(pv_name)</tt> is automatically substituted with the PV attached to the widget.</td>
    </tr>
    <tr>
        <th>Execute Command</th>
        <td>This executes a command on your operating system. It does not execute a telecommand. To execute a telecommand, select "Execute JavaScript" or "Execute Python Script", an example is provided in the script section. <strong>TODO</strong> add the reference to the example</td>
    </tr>
    <tr>
        <th>Execute JavaScript</th>
        <td>Execute a JavaScript. Link to a script file in your workspace, or alternatively embed it into the Action</td>
    </tr>
    <tr>
        <th>Execute Python Script</th>
        <td>Execute some Python script. Link to a script file in your workspace, or alternatively embed it into the Action</td>
    </tr>
    <tr>
        <th>Play WAV File</th>
        <td>Plays the specified sound file</td>
    </tr>
    <tr>
        <th>Open File</th>
        <td>Opens a workspace file with the default handler</td>
    </tr>
    <tr>
        <th>Open Webpage</th>
        <td>Open the specified web page with the integrated web browser</td>
    </tr>
</table>
