---
layout: default
permalink: /docs/server/YAML_AuthModule/
sidebar: yes
---

The YAML AuthModule supports authentication and authorization of users via YAML files available directly in the <tt>etc/</tt> folder.

### Class Name
{% javadoc org/yamcs/security/YamlAuthModule %}

### Configuration Options

<table class="inline">
  <tr>
    <th>Name</th>
    <th>Type</th>
    <th>Description</th>
  </tr>
  <tr>
    <td class="code">hasher</td>
    <td class="code">string</td>
    <td>
      Hasher class that can be used to verify if a password is correct without actually storing the password. When omitted, passwords in <tt>users.yaml</tt> should be defined in clear text. Possible values are:
      <ul>
      <li><a href="https://www.yamcs.org/yamcs/javadoc/index.html?org/yamcs/security/PBKDF2PasswordHasher.html"><tt>org.yamcs.security.PBKDF2PasswordHasher</tt></a></li>
      </ul>
    </td>
  </tr>
  <tr>
    <td class="code">required</td>
    <td class="code">boolean</td>
    <td>
      When set to <tt>true</tt> the YAML AuthModule will veto the login process if it does not know the user. This may be of interest in situations where the YAML AuthModule does not authenticate the user, yet still some control is required via configuration files over which users can login. Default is <tt>false</tt>.
    </td>
  </tr>
</table>

The YAML AuthModule reads further configuration from two additional YAML files: <tt>users.yaml</tt> and <tt>roles.yaml</tt>.

### users.yaml

This file defines users, passwords and user roles. Note that Yamcs itself does not use roles, it is however used as a convenience in the YAML AuthModule to reduce the verbosity of user-specific privilege assignments.

{% yaml users.yaml %}
admin:
  password: somepassword
  superuser: true

someuser:
  password: somepassword
  roles: [ Operator ]
{% endyaml %}

The <tt>password</tt> key may be omitted if the YAML AuthModule is not used for authentication.

If you do use YAML AuthModule for authentication, consider hashing the passwords for better security. Password hashes can be obtained via the command line:

    yamcsadmin password-hash

This command prompts for the password and outputs a randomly salted PBKDF2 hash. This output can be assigned to the <tt>password</tt> key, replacing the clear password.

### roles.yaml

This file defines which privileges belong to which roles.

{% yaml roles.yaml %}
Operator:
  ReadParameter: [".*"]
  WriteParameter: []
  ReadPacket: [".*"]
  Command: [".*"]
  InsertCommandQueue: ["ops"]
  System:
    - ControlProcessor
    - ModifyCommandHistory
    - ControlCommandQueue
    - Command
    - GetMissionDatabase
    - ControlArchiving
{% endyaml %}

This example specifies one role <tt>Operator</tt>. It also demonstrates the use of regular expressions to grant a set of object privileges.

System privileges must be defined under the key <tt>System</tt>. System privileges may not use regular expressions.

All keys are optional so the simplest role definition is simply:

{% yaml roles.yaml %}
EmptyRole:
{% endyaml %}
