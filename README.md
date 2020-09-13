logcheck-extrarules
-------------------

A set of [logcheck][0] rules that are
opinionated and clean up my inbox.

## Contributing

This repository contains rules for a mix of Debian and Ubuntu packages and accepts
contributions for both of these.

If you have rules that are applicable to a Debian stable package and that meet
the following requirements, please consider to contribute them [upstream][0] so they
reach more users:

  * Rules must be as specific as possible (i.e. no `.*` matching).
  * Rules for debug messages are not added.
  * the ignored messages should be related to package versions in Debian unstable.
  * The ignored messages are not temporary (e.g. due to a bug in the packages).
  * The ignored messages are not related to startups/shutdowns/restarts.
  * Error/warning/notice messages are usually not ignored.
  * The rule request should contain example messages and an explanation why the
    messages can be ignored in general.
 
[0]: https://salsa.debian.org/debian/logcheck

## Testing rules
When developing a regex you probably need to test it more often than you want.
The logcheck distribution contains the `logcheck-test` script that can be used
to test rules.

You can also use [`rgxg`][1] to help generate regular expressions for numbers or
to escape text.

[1]: https://rgxg.github.io/

#### Example:
```
System Events
=-=-=-=-=-=-=
May 15 06:25:47 host systemd[1]: Stopped Daily apt upgrade and clean activities.
May 15 06:25:47 host systemd[1]: Stopped Daily apt download activities.
```

This line was in the syslog file of the previous day, so I use `logcheck-test -l /var/log/syslog.1`
to test against this file. After getting the initial output I iteratively refine
until it matches just the new lines that need to be ignored.

```
user@host:~$ logcheck-test -l /var/log/syslog.1 -e "systemd\\[[0-9]+\\].*"
May 15 03:13:15 host systemd[1]: Stopping User Manager for UID 1001...
...
================================================================================
parsed file: /var/log/syslog.1
used rule: '^[[:alpha:]]{3} [ :[:digit:]]{11} [._[:alnum:]-]+ systemd\[[0-9]+\].*Sto.*$'
user@host:~$ logcheck-test -l /var/log/syslog.1 -e "systemd\\[[0-9]+\\]: Stopped Daily.*"
May 15 06:25:47 host systemd[1]: Stopped Daily apt upgrade and clean activities.
May 15 06:25:47 host systemd[1]: Stopped Daily apt download activities.
================================================================================
parsed file: /var/log/syslog.1
used rule: '^[[:alpha:]]{3} [ :[:digit:]]{11} [._[:alnum:]-]+ systemd\[[0-9]+\]: Stopped Daily.*$'
user@host:~$ logcheck-test -l /var/log/syslog.1 -e "systemd\\[[0-9]+\\]: Stopped Daily apt (download|upgrade and clean) activities\."
May 15 06:25:47 host systemd[1]: Stopped Daily apt upgrade and clean activities.
May 15 06:25:47 host systemd[1]: Stopped Daily apt download activities.
================================================================================
parsed file: /var/log/syslog.1
used rule: '^[[:alpha:]]{3} [ :[:digit:]]{11} [._[:alnum:]-]+ systemd\[[0-9]+\]: Stopped Daily apt (download|upgrade and clean) activities\.$'
```

The documentation at [bluelightav.org](http://wiki.bluelightav.org/display/BLUE/Logcheck+administration)
uses a different method. The regex is stored in a file to prevent possible
mangling by the shell;

  * save the regex in `[file]`
  * `egrep --file [file] /var/log/syslog`

## Contributions
This repository contains rules contributed by others. If you want a rule to be
added or consider some rules too lenient, please let me know. These logcheck
rules keep my inbox (a bit) quieter.

Contributions by:
  * asgh
  * felixvictor
  * [TheCreeper](https://github.com/TheCreeper/logcheck-extrarules) (pull request, and his fork have been merged into this repo) 
  * ties
