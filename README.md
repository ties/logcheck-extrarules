## Testing rules
When developing a regex you probably need to test it more ofthen than you want.

### Console
Use the following line to test the logcheck regular expressions.
Relevant logfiles (which logcheck checks) are /var/log/auth.log and /var/log/syslog

sed -e 's/[[:space:]]*$//' /var/log/syslog | egrep     ''

The documentation at [bluelightav.org](<http://wiki.bluelightav.org/display/BLUE/How+to+administer+logcheck) uses a different method. The regex is stored in a file to prevent possible mangling by the shell;
  * save the regex in [file]
  * `egrep --file [file] /var/log/syslog`
