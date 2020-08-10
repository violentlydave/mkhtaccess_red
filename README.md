# mkhtaccess_red

Sandboxing makes phishing/infection hard.  If a target can hand off your payload to a third party to "detonate", how can you get them to run your payload?!  Simple:  by handing sandboxes innocuous payloads to detonate.

How do we do that?  By dynamically creating and deduping a list based on:
- Sandbox/Security company networks by ASN
- Current Tor Exit Nodes
- Cloud Provider networks (AWS, GCP, Azure, IBM, etc)
- UserAgents of known security scanners/possible blue team tools
- Networks seen in previous engagements

The original idea came during some client engagements and getting annoyed while watching sandboxes grab samples.  I created the initial version of the code, and later I found @curi0usJack's static htaccess example, which is grabbed and included.

Thanks to:
- **@curi0usJack** for his example htaccess
- **@imoorhouse904** for testing and data
- **aconite33** for adding Cisco ScanSafe data
- **Jacqueline**, because.. well, she deals w/ me

-------------------------------------
### Usage
```
./mkhtaccess_red -- Dynamically generate an htaccess file to redirect sandbox/blueteam to a benign sample.
twitter.com/violentlydave / www.insomniacsecurity.com

Command line arguments: [you have to use one option, even just -v or -z, otherwise all are optional]

-d DESTINATION_URL (add full url in quotes, "http://someurl.com/mybenignsample.docx")
   Note: This can be specified as a static variable $DESTINATION
   If this command line variable is used, it over-rides the $DESTINATION variable.
-a ASNs (add single or multiple ASNs in quotes, "NetworkName1_ASN1234 	NetworkName2_ASN4321")
   Note: This can be specified as a static variable $DEFAULTASN, if this command line is used,
   ASN will be added to any ASNs in the $DEFAULTASN variable.
-u USERAGENTS (add single or multiple user agents in regex format in quotes, "^.*SomeScrapingBot.*$")
   Note: UserAgents can be added to the static variable $DEFAULTAGENTS, and if this command
   line is used any specified user agents will be added to the $AGENTS variable.
-e ExtraIPs-or-Nets (add single or multiple ips or nets in quotes, "127.0.0.1,10.6.5.0/24".)
   Note: These can be added statically as MISC sources in the code.
-o OUTPUT (lets you set the path/name of the output, or it will default to /tmp/redhtaccess)
-v VERBOSE MODE (adds more info about behind the scenes/deduping)

-z I DONT CARE, JUST RUN! (will run w/ default static variables/info, and generate an htaccess)
```

-------------------------------------
### Apache Config

Make sure your Apache configs AllowOverride, so the htaccess will work. 

Example:

<Directory "/var/www/html/test">
   Options Indexes FollowSymLinks
   AllowOverride All
<\/Directory>  

If you need to debug how it is matching, add "LogLevel alert rewrite:trace6" to your main configuration -- but keep in mind that each connection attempt will log EACH regex to your logs. ***This can fill logs/drives quickly if many attempts occur!***
