---
layout: default
permalink: /docs/server/Process_Runner/
sidebar: yes
---

Runs an external process. If this process goes down, a new process instance is started.

### Class Name
{% javadoc org/yamcs/ProcessRunner %}

### Configuration

This is a global service defined in <tt>etc/yamcs.yaml</tt>. Example from a typical deployment:

{% yaml yamcs.yaml %}
services:
  - class: org.yamcs.ProcessRunner
    args:
      command: "bin/simulator.sh"
{% endyaml %}

### Configuration Options

<table class="inline">
  <tr>
    <th>Name</th>
    <th>Type</th>
    <th>Description</th>
  </tr>
  <tr>
    <td class="code">command</td>
    <td class="code">string or string[]</td>
    <td><strong>Required. </strong> Command (and optional arguments) to run.</td>
  </tr>
  <tr>
    <td class="code">directory</td>
    <td class="code">string</td>
    <td>Set the working directory of the started subprocess. If unspecified, this defaults to the cwd of Yamcs.</td>
  </tr>
  <tr>
    <td class="code">logLevel</td>
    <td class="code">string</td>
    <td>Level at which to log stdout/stderr output. One of <tt>INFO</tt>, <tt>DEBUG</tt>, <tt>TRACE</tt>, <tt>WARN</tt>, <tt>ERROR</tt>. Default: <tt>INFO</tt></td>
  </tr>
  <tr>
    <td class="code">logPrefix</td>
    <td class="code">string</td>
    <td>Prefix to prepend to all logged process output. If unspecified this defaults to '<tt>[COMMAND] </tt>'.</td>
  </tr>
</table>
