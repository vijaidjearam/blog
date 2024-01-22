---
layout: post
date: 2024-01-22 10:57:39
title: Flexlm command line switches
category: flexlm
tags: flexlm lmutil
---
# License Administration Tools

FLEXlm provides utilities for the license administrator to help manage the licensing activities on the network. These utilities are:

lmcksum (v2.4 or later) - prints license checksums. (page 42)
lmdiag (v4.0 or later) - diagnoses license checkout problems. (page 42)
lmdown - gracefully shuts down all license daemons (both lmgrd and all vendor daemons) on the license server node (or on all three nodes in the case of redundant servers). (page 43)
lmgrd - the main daemon program for FLEXlm. (page 44)
lmhostid - reports the hostid of a system. (page 45)
lminstall - install a decimal format license. (page 45)
lmremove - removes a single user's license for a specified feature. (page 46)
lmreread - causes the license daemon to reread the license file and start any new vendor daemons. (page 47)
lmstat - helps you monitor the status of all network licensing activities. (page 47)
lmswitch (VMS only) -switches the debug logfile. (page 48)
lmswitchr - switches the report log file. (page 48)
lmver - reports the FLEXlm version of a library or binary file. (page 49)
Beginning in FLEXlm v2.4, all FLEXlm utility programs (except lmgrd) are packaged as a single executable called lmutil. lmutil can either be installed as the individual commands (either by creating links to the individual command names, or making copies of lmutil as the individual command names), or the commands can be run as `lmutil command', e.g. `lmutil lmstat', or `lmutil lmdown'. On Windows or Windows/NT systems, the `lmutil command_name' form of the commands are available. There is also a Windows version of these commands - see Section 6.13.1, `License Administration Tools - LMTOOLS for Windows,' on page 49.

Arguments valid for all lmutil utilities
-c license_file
Most lmutil utilities need to know the path to the license file. This can be specified with a `-c license_file' argument, or by setting the LM_LICENSE_FILE environment variable. Otherwise, the default location is used.
-verbose
Prints longer description for all errors found. The output from the utilities may be harder to read with this option, but is useful for diagnostics. (version 6+ only)
## 6.1 lmcksum
The lmcksum program (FLEXlm v2.4 or later) will perform a checksum of a license file. This is useful to verify data entry errors at your location. lmcksum will print a line-by-line checksum for the file as well as an overall file checksum. lmcksum takes the `-k' switch to force the encryption key checksum to be case-sensitive.

lmcksum will ignore all fields that do not enter into the encryption key computation; thus the server node name and port number, as well as the daemon pathname and options file names are not checksummed. In addition, lmcksum will treat non-case sensitive fields correctly (in general, lmcksum is not case-sensitive).

lmcksum takes an optional daemon name; if specified, only license file lines for the selected daemon are used to compute the checksums.

For FEATURE lines that contain ck=nnn, lmcksum prints simply OK or BAD.

Usage is:

lmcksum [-c license_file]
-c license_file
path to the file to checksum. By default lmcksum uses `license.dat' in the current directory (unlike other lmutil commands).
Example output is:

lmcksum - Copyright (C) 1989, 1997 GLOBEtrotter Software, Inc.
lmcksum: using license file"/usr/local/flexlm/licenses/license.dat"

	189: SERVER speedy 08002b32b161 2837
	166: DAEMON xyzd C:\flexlm\xyzd.exe 
	  8: FEATURE f1 xyzd 1.000 01-jan-99 0 3B2BC33CE4E1B8F3A0BF""
OK:  	231: FEATURE f2 xyzd 1.0 01-jan-0 1 8B1C30015351B7737F5E \
		DUP_GROUP=HD ck=231
	109: (overall file checksum)
## 6.2 lmdiag
lmdiag (FLEXlm v4.0 or later) allows you to diagnose problems when you cannot check out a license.

Usage is:

lmdiag [-c license_file] [-n] [feature]
-c license_file
path to the file to diagnose.
-n
run in non-interactive mode; lmdiag will not prompt for any input in this mode. In this mode, extended connection diagnostics are not available.
feature
diagnose this feature only.
If no feature is specified, lmdiag will operate on all features in the license file(s) in your path. lmdiag will first print information about the license, then attempt to check out each license. If the checkout succeeds, lmdiag will indicate this. If the checkout fails, lmdiag will give you the reason for the failure. If the checkout fails because lmdiag cannot connect to the license server, then you have the option of running `extended connection diagnostics'.

These extended diagnostics attempt to connect to each port on the license server node, and can detect if the port number in the license file is incorrect. lmdiag will indicate each port number that is listening, and if it is an lmgrd process, lmdiag will indicate this as well. If lmdiag finds the vendor daemon for the feature being tested, then it will indicate the correct port number for the license file to correct the problem.

See Also: Appendix B, `FLEXLM_DIAGNOSTICS' on page 56.


## 6.3 lmdown
The lmdown utility allows for the graceful shutdown of all license daemons (both lmgrd and all vendor daemons) on all nodes.

Usage is:

lmdown [-c license_file] [-vendor name] [-q]
-c license_file
Use the specified license file.
-vendor name
Only shutdown this one vendor daemon. lmgrd will always continue running if this option is specified. Requires v6.0 lmdown and lmgrd (the vendor daemon can be any version).
-q
Don't prompt or print a header. Otherwise lmdown asks `Are you sure? [y/n]: `.
You may want to protect the execution of lmdown, since shutting down the servers causes users to lose their licenses. See the `-p' or the `-x' options in Section 6.4, `lmgrd,' on page 44 for details about securing access to lmdown.

If lmdown encounters more than one server (for example if -c specifies a directory with many *.lic files) a choice of servers to shutdown is presented.

To stop and restart a single vendor daemon, use lmdown -vendor name, then, use lmreread -vendor name, which restarts the vendor daemon.

When shutting down redundant servers, there is a one-minute delay before the servers shut down. Do not use `kill -9' to shut down the license servers.

See Also: Section 6.8, `lmreread,' on page 47.


## 6.4 lmgrd
lmgrd is the main daemon program for FLEXlm. When you invoke lmgrd, it looks for the license file which contains the information about vendors and features. On Unix systems, it is strongly recommended that lmgrd be run as a non-privileged user (not root).

Usage is:

lmgrd [ -app ] [ -c license_file ] [ -t timeout_interval ] [ -l logfile ]
[ -s timestamp_interval ] [ -2 -p ] [ -v ] [ -x lmdown ] [ -x lmremove ]
-app
Required for Windows/NT systems when run as a command, but not used when run as a service.
-c license_file
Use the license file named.
-t timeout_interval
Sets a timeout interval, in seconds, during which redundant daemons must complete their connections to each other. The default value is 10 seconds. A larger value may be desirable if the daemons are being run on busy systems or a very heavily loaded network.
-l logfile
Write the debug log to logfile.
-s timestamp_interval
Specifies the logfile timestamp interval, in minutes. The default is 360 minutes.
-2 -p
Restricts usage of lmdown, lmreread, and lmremove to a FLEXlm administrator who is by default root. If there a Unix group called `lmadmin' then use is restricted to only members of that group. If root is not a member of this group, then root does not have permission to use any of the above utilities. The `-p' option is available in FLEXlm v2.4 and later.
-v
Prints lmgrd's version number and copyright and exits.
-x lmdown
Disallow the lmdown command (no user can run lmdown). If lmdown is disabled, you will need to stop lmgrd via `kill pid' (Unix) or CTRL-ALT-DEL and stop the lmgrd and vendor daemon processes (Windows 95). On Unix, be sure the kill command does not have a -9 argument.
-x lmremove
Disallow the lmremove command (no user can run lmremove)
The -x lmdown and -x lmremove options are available in FLEXlm v4.0 and later.


## 6.5 lmhostid
The lmhostid utility reports the hostid of a system.

Usage is:

lmhostid [-n]
The output of this command looks as follows:

lmhostid - Copyright (c) 1989, 1997 Globetrotter Software, Inc.
The FLEXlm hostid of this machine is"69021c89"
With the `-n' argument, no header is printed; only the hostid.

See Appendix A, `Hostids for FLEXlm-Supported Machines'.


## 6.6 lminstall
New in version 6, lminstall is designed primarily for typing in decimal format licenses to generate a readable format license file.

Usage is:

	lminstall [-i {infile | -}] [-o outfile] \
		[-overfmt {2 | 3 | 4 | 5 | 5.1 | 6}] [-odecimal]
Normally, lminstall is used with no arguments; you are prompted for the name of the output license file. The default name is today's date in yyyyddmm.lic format. The file should be moved to the application's default license file directory, if specified by the software vendor. Otherwise, use LM_LICENSE_FILE or VENDOR_LICENSE_FILE to specify the directory where the *.lic files are located.

Decimal format input is verified by checksum of each line.

To finish entering, type `q' on a line by itself, or enter 2 blank lines.

If infile is a dash ('-'), it takes input from stdin.When '-i' is used, default output is stdout; otherwise if -o is not specified, lminstall prompts the user for an output file name.

lminstall As A Conversion Tool:
lminstall can alternatively be used to convert licenses between decimal and readable format, and between different versions of FLEXlm license formats.

To convert from readable to decimal:

	% lminstall -i infile -o outfile -odecimal
To convert to FLEXlm Version 2 format:

	% lminstall -i infile -o outfile -verfmt 2
Conversion errors are reported as necessary. lminstall has a limit of 1000 lines of input.


## 6.7 lmremove
The lmremove utility allows you to remove a single user's license for a specified feature. This is only needed when a client node crashes, since that's the only condition where a license is not automatically freed. If the application is active, it will re-checkout the license after it is freed by lmremove.

Usage is:

lmremove [ -c file ] feature user host display
	or
lmremove [ -c file ] -h feature host port handle
-c license_file
license file
feature
name of the feature checked out by the user.
user
name of the user whose license you are removing (from lmstat -a).
host
name of the host the user is logged in to (from lmstat -a).
display
name of the display where the user is working (from lmstat -a).
port
port, as reported by lmstat -a
handle
handle, as reported by lmstat -a
The user host display port and handle information must be obtained from the output of lmstat -a.

lmremove removes all instances of user on host on display from usage of feature. If the optional `-c file' is specified, the indicated file is used as the license file.You should protect the execution of lmremove, since removing a user's license can be disruptive. See the `-p' or the `-x' options in Section 6.4, `lmgrd,' on page 44 for details about securing access to lmremove.

The -h variation uses the serverhost, port, and license handle, as reported by lmstat -a. Consider this example lmstat -a output:

joe cloud7 /dev/ttyp5 (v1.000) (cloud9/7654 102), start Fri 10/29 18:40
In this example, the serverhost is `cloud9', the port is `7654' and the license handle is 102. To remove this license, issue the following command:

lmremove -h f1 cloud9 7654 102
or

lmremove f1 joe cloud7 /dev/ttyp5
When removing by handle, if licenses are grouped as duplicates, all duplicate licenses will also be removed.


## 6.8 lmreread
The lmreread utility causes the license daemon to reread the license file and start any new vendor daemons that have been added. In addition, all running daemons will be signaled to reread the license file for changes in feature licensing information.

Usage is:

lmreread [-c license_file] [-vendor name]
-c license_file
Use the specified license file.
-vendor name
Only this one vendor daemon should reread the license file. lmgrd will restart the vendor daemon if necessary. Requires v6.0 lmreread and lmgrd (the vendor daemon can be any version).
The license administrator may want to protect the execution of lmreread. See the `-p' and `-x' options in Section 6.4, `lmgrd,' on page 44 for details about securing access to lmreread.

To stop and restart a single vendor daemon, use lmdown -vendor name, then use lmreread -vendor name, which restarts the vendor daemon.

If you use the `-c' option, the license file specified will be read by lmreread, not by lmgrd; lmgrd rereads the file it read originally. Also, lmreread cannot be used to change server node names or port numbers. Vendor daemons will not reread their option files as a result of lmreread.

lmreread does not cause the vendor daemon to reread the options file. For options file changes to take effect, lmgrd must be stopped (lmdown) and restarted.

See Also: Section 6.8, `lmreread,' on page 47.


## 6.9 lmstat
The lmstat utility helps you monitor the status of all network licensing activities.

Usage is:

lmstat 	[-a] [ -A ] [-c license_file] [-f feature] [-i [feature]]
	[-S vendor] [-s hostname] [-t value]
-a
Display all information
-A
List all active licenses
-c license_file
Use the license file named.
-f feature_name
List users of feature_name.
-i [feature_name]
Print information about the named feature, or all features if no feature_name is given. This option is usually not recommended, since the information does not come from the license server, and may not reflect what the server actually supports.
-S [vendor]
List all users of vendor's features.
-s hostname
Display status of clients running on hostname.
-t value
Set lmstat timeout to `value'.

## 6.10 lmswitch

The lmswitch command is available on VMS only.

The lmswitch utility switches the debug log file for the daemon serving the specified feature while the daemon is running.

Usage is:

lmswitch feature new-file
feature
any feature this daemon supports.
new-file
the new file path.
Of course, for this syntax to work, lmswitch needs to be installed as a foreign command.

The new logfile will be opened for write, rather than append, so it is possible to `switch' to the same filename in order to be able to view the old log file.


## 6.11 lmswitchr
The lmswitchr utility switches the report writer (REPORTLOG) log file. It will also start a new REPORTLOG file if one does not already exist.

Usage is:

lmswitchr [-c license_file] feature new-file
lmswitchr [-c license_file] vendor new-file [v5.0+ only]
-c
license file path
feature
any feature this daemon supports
new-file
new file path
lmswitchr does not work with FLEXlm v3.0 vendor daemons. Ask your vendor for a later version of their vendor daemon.


## 6.12 lmver
The lmver utility reports the FLEXlm version of a library or binary file.

Usage is:

lmver filename
filename
name of the executable of the product.
For example if you have an application called `spell' type:

	% lmver spell
Alternatively, on Unix systems, you can use the following commands to get the FLEXlm version of a binary:

	strings file | grep Copy
