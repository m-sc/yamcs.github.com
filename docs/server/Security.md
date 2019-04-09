---
layout: default
sidebar: yes
chapter: yes
permalink: /docs/server/Security/
---

Yamcs includes a security subsystem which allows authenticating and authorizing users. Authentication is the act of identifying the user, whereas authorization involves determining what privileges this user has.

Once authorized, the user may be assigned one or more privileges that determine what actions the user can perform. Yamcs distinguishes between system privileges and object privileges.

### System Privileges
A system privilege is the right to perform a particular action or to perform an action on any object of a particular type.

<table class="inline">
  <tr>
    <th class="code">ControlProcessor</th>
    <td>Allows to control any processor</td>
  </tr>
  <tr>
    <th class="code">CreateInstances</th>
    <td>Allows to create instances</td>
  </tr>
  <tr>
    <th class="code">ModifyCommandHistory</th>
    <td>Allows to modify command history</td>
  </tr>
  <tr>
    <th class="code">ControlCommandQueue</th>
    <td>Allows to manage command queues</td>
  </tr>
  <tr>
    <th class="code">Command</th>
    <td>Allows to issue any command</td>
  </tr>
  <tr>
    <th class="code">GetMissionDatabase</th>
    <td>Allows to read the entire MDB</td>
  </tr>
  <tr>
    <th class="code">ChangeMissionDatabase</th>
    <td>Allows online changes to the MDB</td>
  </tr>
  <tr>
    <th class="code">ControlArchiving</th>
    <td>Allows to manage archiving properties of Yamcs</td>
  </tr>
  <tr>
    <th class="code">ControlLinks</th>
    <td>Allows to control the lifecycle of any data link</td>
  </tr>
  <tr>
    <th class="code">ControlServices</th>
    <td>Allows to manage the lifecycle of services.</td>
  </tr>
  <tr>
    <th class="code">ManageAnyBucket</th>
    <td>Provides full control over any bucket (including user buckets)</td>
  </tr>
  <tr>
    <th class="code">ReadEvents</th>
    <td>Allows to read any event</td>
  </tr>
  <tr>
    <th class="code">WriteEvents</th>
    <td>Allows to manually post events</td>
  </tr>
  <tr>
    <th class="code">WriteTables</th>
    <td>Allows to manually add records to tables</td>
  </tr>
  <tr>
    <th class="code">ReadTables</th>
    <td>Allows to read tables</td>
  </tr>
</table>

{% hint %}
	Yamcs extensions may support additional system privileges.
{% endhint %}

### Object Privileges
An object privilege is the right to perform a particular action on an object. The object is assumed to be identifiable by a single string. The object may also be expressed as a regular expression, in which case Yamcs will perform pattern matching when doing authorization checks.

<table class="inline">
  <tr>
    <th class="code">Command</th>
    <td>Allows to issue a particular command</td>
  </tr>
  <tr>
    <th class="code">CommandHistory</th>
    <td>Allows access to the command history of a particular command</td>
  </tr>
  <tr>
    <th class="code">InsertCommandQueue</th>
    <td>Allows to insert commands to a particular queue</td>
  </tr>
  <tr>
    <th class="code">ManageBucket</th>
    <td>Allow control over a specific bucket</td>
  </tr>
  <tr>
    <th class="code">ReadBucket</th>
    <td>Allow readonly access to a specific bucket</td>
  </tr>
  <tr>
    <th class="code">ReadPacket</th>
    <td>Allows to read a particular packet</td>
  </tr>
  <tr>
    <th class="code">ReadParameter</th>
    <td>Allows to read a particular parameter</td>
  </tr>
  <tr>
    <th class="code">Stream</th>
    <td>Allow to read and emit to a specific stream</td>
  </tr>
  <tr>
    <th class="code">WriteParameter</th>
    <td>Allows to set the value of a particular parameter</td>
  </tr>
</table>

{% hint %}
	Yamcs extensions may support additional object privileges.
{% endhint %}

### Superuser
A user may have the attribute <tt>superuser</tt>. Such a user is not subject to privilege checking. Any check of any kind will automatically pass. An example of such a user is the <tt>System</tt> user which is used internally by Yamcs on some actions that cannot be tied to a specific user. The <tt>superuser</tt> attribute may also be assigned to end users if the AuthModule supports it.

### AuthModules
The security subsystem is modular by design and allows combining different AuthModules together. This allows for scenarios where for example you want to authenticate via LDAP, but determine privileges via YAML files.

The default set of AuthModules include:

<dl>
  <dt>YAML AuthModule</dt>
  <dd>
    Reads Yaml files to verify the credentials of the user, or assign privileges.
  </dd>
  <dt>LDAP AuthModule</dt>
  <dd>
    Attempts to bind to LDAP with the provided credentials. Also capable of reading privileges for the user.
  </dd>
  <dt>SPNEGO AuthModule</dt>
  <dd>
    Supports authenticating against a Kerberos server. This module includes extra support for Single-Sign-On via Yamcs web interface.
  </dd>
</dl>

AuthModules have an order. When a login attempt is made, AuthModules are iterated a first time in this order. Each AuthModule is asked if it can authenticate with the provided credentials. The first matching AuthModule contributes the user principal. A second iteration is done to then contribute privileges to the identified user. During both iterations, AuthModules reserve the right to halt the global login process for any reason.

{% hint %}
  A special note on <i>roles</i>. Yamcs itself does not require roles nor does it keep track of roles on the [User](https://www.yamcs.org/yamcs/javadoc/index.html?org/yamcs/security/User.html) object. Permissions are always verified via user privileges. Specific AuthModules may however introduce roles as a convenience to group sets of privileges together.
{% endhint %}

### Configuration

Example from a typical deployment:

{% yaml security.yaml %}
enabled: true

# Implicit user when security is _not_ enabled
unauthenticatedUser:
  username: admin
  superuser: true

authModules:
  - class: org.yamcs.security.YamlAuthModule
    config:
      hasher: org.yamcs.security.PBKDF2PasswordHasher
{% endyaml %}

These options are supported:

<dl>
  <dt>enabled</dt>
  <dd>Whether security is enabled. If <tt>false</tt> then Yamcs will not require users to login and will assume that everybody shares a single account defined under the <tt>unauthenticatedUser</tt> property.</dd>

  <dt>unauthenticatedUser</dt>
  <dd>Configures the user details of the default user. This property is only considered when <tt>enabled</tt> is set to <tt>false</tt></dd>

  <dt>authModules</dt>
  <dd>List of AuthModules that particpate in the login process. Each AuthModule may support custom configuration options which can be defined under the <tt>config</tt> key.</dd>
</dl>
