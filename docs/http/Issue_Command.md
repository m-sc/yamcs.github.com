---
layout: default
permalink: /docs/http/Issue_Command/
sidebar: yes
---

Issue a new command of the given type:

    POST /api/processors/:instance/:processor/commands/:namespace/:name
    
  
After validating the input parameters, the command will be added to the appropriate command queue for further dispatch.

### Parameters

<table class="inline">
  <tr>
    <th>Name</th>
    <th>Type</th>
    <th>Description</th>
  </tr>
  <tr>
    <td class="code">origin</td>
    <td class="code">string</td>
    <td>The origin of the command. Typically a hostname.</td>
  </tr>
  <tr>
    <td class="code">sequenceNumber</td>
    <td class="code">integer</td>
    <td>The sequence number as specified by the origin. This gets communicated back in command history and command queue entries, thereby allowing clients to map local with remote command identities.</td>
  </tr>
  <tr>
    <td class="code">dryRun</td>
    <td class="code">bool</td>
    <td>Whether a response will be returned without actually issuing the command. This is useful when debugging commands. Default <tt>no</tt></td>
  </tr>
  <tr>
    <td class="code">assignment</td>
    <td class="code">array&nbsp;of<br>string&nbsp;pairs</td>
    <td>
      The name/value assignments for this command.
    </td>
  </tr>
  <tr>
    <td class="code">comment</td>
    <td class="code">string</td>
    <td>Comment attached to this command.</td>
  </tr>
</table>


### Example

```json
{
  "sequenceNumber" : 1,
  "origin" : "my-machine",
  "assignment" : [ {
    "name": "voltage_num",
    "value": "3"
  } ],
  "dryRun" : true
}
```

### Response

<pre class="header">Status: 200 OK</pre>
```json
{
  "queue" : "default",
  "source" : "SWITCH_VOLTAGE_ON(voltage_num: 3)",
  "hex" : "1864C000000000000000006A0000000103",
  "binary" : "GGTAAAAAAAAAAABqAAAAAQM="
}
```

The binary is encoded in Base64 format.

### Alternative Media Types

#### Protobuf

Use these HTTP headers:

    Content-Type: application/protobuf
    Accept: application/protobuf
    
Request is of type:

{% proto rest/rest.proto %}
message IssueCommandRequest {
  message Assignment {
    optional string name = 1;
    optional string value = 2;
  }
  repeated Assignment assignment = 1;
  optional string origin = 2;
  optional int32 sequenceNumber = 3;
  optional bool dryRun = 4;
}
{% endproto %}

#### Response

{% proto rest/rest.proto %}
message IssueCommandResponse {
  optional string queue = 1;
  optional string source = 2;
  optional string hex = 3;
  optional bytes binary = 4;
}
{% endproto %}
