# fail2ban-whitelist

whitelist a given ip in all jails.

## Scenario

Sometimes there are networks behind a single
public ip(WAN-Device for a customer network).

In this network there are a number of users.
If one of those users is operating a device
with wrong credentials(e. g. wrong e-mail password)
the whole network will get blocked.

So this script is for whitelisting those
ip addresses, where successful logins 
had taken place. So those devices with
wrong credentials are ignored as long
as there are successful logins from that
ip address

## Installation

 * Copy the new action script to your /etc/fail2ban/action.d/
 * Copy the f2b\_whitelist script into an executable location(e. g. /usr/local/bin)
 * Create a new jail in jails.conf called [whitelist-something],
   like these examples:

```
[whitelist-courier]
enabled   = true
filter    = courier-login-ok
maxretry  = 1
action    = whitelist
bantime   = 86400
findtime  = 86400
logpath   = /var/log/mail.log

[whitelist-postfix-sasl]
enabled   = true
filter    = postfix-sasl-login-ok
maxretry  = 1
action    = whitelist
bantime   = 86400
findtime  = 86400
logpath   = /var/log/mail.log
```

  * Copy/Create your filter definitions like these:

Example for successful postfix-sasl authentication:

```
[INCLUDES]

before = common.conf

[Definition]

logpath = /var/log/mail.log
ignoreregex = 

_daemon = postfix/smtpd

failregex = ^%(__prefix_line)s[A-F0-9]+: client=[^[]+\[<HOST>\], sasl_method=[^,]+, sasl_username=

```

Example for successful courier imap/pop3 authentication:

```
[INCLUDES]

before = common.conf

[Definition]
logpath = /var/log/mail.log
_daemon = courier-(imap|pop3)[ds]
failregex = ^%(__prefix_line)sLOGIN, user=[^@]+@[^,]+, ip=\[(.*:)*<HOST>\]
ignoreregex = 
```
  * restart fail2ban 

## Why not use iptables?

iptables can slow down the networking, if
there are too many rules. So I decided to
use dynamic fail2ban whitelisting.

## ToDo

The script is somewhat slow. It takes about
1 Sek to add/remove the address from/to the
list.

## Caveats

Beware that there are solutions around that
enable rising ban times upon repeated Ban-Entries 
in fail2ban.log, so that repeating violations
get higher ban times. Make sure, if you have
such measures in place to modify the detection
expression, so the whitelisting does not lead
to the opposite of the expected and the white-
listed ips get blocked instead.
