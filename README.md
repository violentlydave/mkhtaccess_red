# mkhtaccess_red

 This is done via some User-Agent matching, pulling networks advertised by ASNs of known sandbox companies, and "MISC" sources that were found from previous real-world phishing attempts.    The original idea came up during some phishing campaigns a while ago, later someone pointed me to @curi0usJack's htaccess file which provided the user-agents.
 
 Thanks to @curi0usJack for some data source/info, and thanks to @imoorhouse904 for testing and data to add!
 
__TLDR; edit DESTINATION to point to benign version of payload, put output into .htaccess, hopefully enjoy phishing again!__

### Apache Config
Make sure your apache configs AllowOverride, so the htaccess will work.   Example:

       <Directory "/var/www/html/test">
          Options Indexes FollowSymLinks
          AllowOverride All
        </Directory>
        
If you need to debug how it is matching, add "LogLevel alert rewrite:trace6" to your main configuration -- but keep in mind that each connection attempt will log EACH regex to your logs.   This can fill logs/drives quickly if alot of attempts occur!

### mkhtaccess_red Config
Simply edit a few variables within the program, all at the top:

**WORKINGFILE=/tmp/redhtaccess** ; this will be the final file, you can leave this\
**TMPFILE=/tmp/tmptargets** ; temp working file, you can leave this\
**JACKTMP=/tmp/jacktmp** ; temp working file for curi0usJack's file, you can leave this\
**CURLOPTIONS="--connect-timeout 10"** ; add additional curl options here, such as proxies, you can leave this\
**DESTINATION="http://funkytown.com/DIR/file.doc"** ; this is where to redirect sandboxes -- make it look as similar to the original as possible, but make it redirect to a clean (malware-free) version of your payload!

#### Adding Useragents / ASNs / Misc networks to mkaccess_red
Similar to the section above, all ASN/useragent/misc configs are kept internal to keep the file portable/easy to deal with.

#### AGENTS
Simply add additional regexs to match agents inside of this variable, example:

       AGENTS="^.*cloudfront.*$ ^curl.*$ ^Python-urllib.*$ ^Wget.*$ ^Lynx.*$ ^Slackbot-LinkExpanding.*$"

#### ASNs
Add additional ASNs to this variable in the form of CompanyName_ASN#, example:

       ASNS="zScaler_AS22616 DigitalOcean_AS46652 ForcePoint_AS13448 CiscoMeraki_AS395831"

#### Misc Nets/Addresses:
Add additional lines per "misc" source, starting with a "#" !
Add in the form of:\
   #MISC-NetworkOrIP-CompanyName-ReasonAdded\
Example:

       #MISC-195.47.249.0/24-Bayer-Seen in phish
       #MISC-40.96.0.0/12-MicrosoftCorp-40.107.242.0 seen in previous phish
       #MISC-40.125.0.0/17-MicrosoftCorp-
