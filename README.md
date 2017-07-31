# fail2ban-whitelist

whitelist a given ip in all jails.

## Scenario

Sometimes there are networks behind a single public ip(WAN-Device for a customer network).

In this network there are a number of users.  If one of those users is operating a device with wrong credentials(e. g. wrong e-mail password) the whole network will get blocked.

So this script is for whitelisting those ip addresses, where successful logins had taken place. So those devices with wrong credentials are ignored as long as there are successful logins from that ip address.

## Installation

 * Copy directories action.d, jail.d and filter.d into your fail2ban-config-directory
 * Copy the f2b\_whitelist script into an executable location(e. g. /usr/local/bin), and chmod +rx
 * Enable the desired whitelist-jails in jail.d (set enabled=true)

## Why not use iptables?

iptables can slow down the networking, if there are too many rules. So I decided to use dynamic fail2ban whitelisting.

## ToDo

  * The script is somewhat slow. It takes about 1 Sek to add/remove the address from/to the list.
  * Every whitelister has does additional parsing of mail.log. This can be done in a single instance

## Caveats

Beware that there are solutions around that enable rising ban times upon repeated Ban-Entries in fail2ban.log, so that repeating violations get higher ban times. Make sure, if you have such measures in place to modify the detection expression, so the whitelisting does not lead to the opposite of the expected and the whitelisted ips get blocked instead.

Something like that:

  * http://blog.shanock.com/fail2ban-increased-ban-times-for-repeat-offenders/ or
  * https://wiki.meurisse.org/wiki/Fail2Ban#Recidive
