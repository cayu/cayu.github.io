---
title: "An Introduction to Linux Shell Scripting for DBAs"
date: 2014-05-09T11:42:02-03:00
---

## An Introduction to Linux Shell Scripting for DBAs

by Casimir Saternos

**Learn some basic bash shell scripts for installing, running, and maintaining Oracle databases on Linux**.

Published November 2005

About seven years ago, Oracle released the first commercial database on Linux. Since then, Oracle, Red Hat, and Novell/SUSE have been steadily collaborating on changes to Linux kernel as they relate to database and application performance. For that reason, Oracle Database 10g for Linux includes enhancements that are closely related to the operating system. Now more than ever, DBAs need to have knowledge of and experience on this platform to best administer the systems under their watch.

There is a traditional division of responsibilities between sysadmins and DBAs. However, in practice, the distinction is not always clear. Many IT shops employ individuals who address concerns at the database as well as the operating system levels. And of course the Oracle Database itself uses operating system resources and is designed to interact closely with its environment.

Furthermore, many sysadmins and DBAs find it necessary or convenient to automate tasks related to their work. The installation of software, monitoring of system resources, and management of systems involve repetitive and error-prone tasks are better addressed through automated processes than manual procedures.

One method that is used to automate such tasks is shell scripting. Shell scripts play a significant role in the Linux System from the time it is installed. Various scripts are called when the system is started up and shut down. Utilities by Oracle and other third-party vendors are invoked through shell scripts. Because they can be developed quickly, they have historically been used for prototyping applications. System Administrators have taken advantage of the functionality available through shell scripting to provide solutions that are tailored for the particular requirements and idiosyncrasies of the systems under their watch.

In this article, I will introduce functionality available through “bash” shell scripting that is relevant to installing, running, and maintaining Oracle databases on a Linux platform. Note that this article is designed for Linux scripting beginners or DBAs who are relatively new to Linux; most experienced Linux sysadmins will not find it helpful.

What Is a Shell Script?

A shell script is simply a text file containing a sequence of commands. When you run the file—or script—it executes the commands contained in the file. The term shell simply refers to the particular command-line user interface you use to communicate with the Linux kernel. Several different shells are available, including the C shell (csh), Korn shell (ksh), Bourne shell (sh), and Bourne-Again shell (bash). The shell itself is a command that reads lines from either a file or the terminal, interprets them, and generally executes other commands. The Bourne-Again shell incorporates features of the other shells mentioned and is the one that was used for the scripts this article demonstrates.

The first line in the script file can be used to dictate which shell will be used to run the script. This is the meaning of the first line found in all of the script examples:

```
#!/bin/bash
```

Why Use Shell Scripts?

Depending on your background, you may not see any immediate value to shell scripting as it relates to the DBA’s work. If you do not have experience with UNIX or UNIX-like systems, the myriad of cryptic commands might be cause for concern. Besides, in addition to being a relational database, Oracle 10g provides a robust platform for processing data within the database as well as several methods of interacting with the OS outside of the database.

However, there are several reasons you might find yourself delving into the world of shell scripting, including the following:

- You are in a situation in which you must support already existing scripts.
- You want to automate system setup that occurs prior to the installation of the Oracle software. For instance, you could write a script to check the initial state of the OS and report any prerequisites that must be met before installation of the software. The script might also create relevant OS users and groups and set environmental variables for the users.
- A running Oracle database can be used to execute manual or scheduled tasks. However, some tasks need to be run when the database is not running. You can use a script to stop or start a database (as well as a listener or a related database process). Such an action cannot be initiated from within the database itself.
- You need a mechanism for monitoring the state of a database (i.e. if it is running and available to process queries). Such a script could also monitor other processes and resources that are not Oracle-specific to provide a fuller picture of what is occurring in the system.
- You need to automate backups. Oracle Recovery Manager (RMAN) is a utility that allows you to develop backup scripts that can run on any platform. You can call Oracle Recovery Manager from a shell script and use it to perform a wide range of backup and recovery activities.
- You might have a requirement that is not specific to a single database. Perhaps you have several databases on a single machine. It may not be advisable to fulfill the requirement by using a single database, and it introduces potential security issues as well. Under such circumstances, shell scripting provides a means for fulfilling the requirement in a manner that does not associate the process with a single database.

When Not to Use Shell Scripts

Oracle Database includes functionality that extends beyond the traditional definition of an RDBMS. Like any other piece of software, it uses resources that are provided by the operating system, but it can “see” and “change” its environment to a much greater degree than other software. SQL and Oracle’s fixed views provide a picture of the system from inside the database, whereas shell scripting provides a view of the system from outside of the database. Shell scripting is not the solution for every problem.

It important to recognize that many aspects of the operating system can be monitored and modified from within the database. Oracle’s fixed views (the views with a v\$ prefix) can be used to determine the host name of the machine (v\$instance) or the platform name on which the database is running (v\$database). The location and other attributes of the files associated with the database can be determined in this manner as well. The location and other attributes of datafiles (v\$datafile, dba_data_files), temp files (v\$tempfile, dba_temp_files), redo logs (v\$logfile), archive logs (v\$archived_log), and control files (v\$controlfile) can be queried directly from the database. You can determine information about the flash recovery area (\$recovery_file_dest) from this view, as well as by looking at some init.ora parameters (db_recovery_file_dest, db_recovery_file_dest_size). The status of processes (v\$process) and memory (v\$sga, v\$sgastat, and so on) can be queried as well. There are various built-in PL/SQL packages as well as the ability to create Java and C database objects that allow for additional access to the underlying OS.

If you are considering scripting for a task that requires a good deal of database access, scripting is probably not your best option. Later in this article, there is a description of how to access the database by using SQL\*Plus but in many cases, you are better off approaching the problem by using another language.

The charts below summarize information accessible from within the database itself:

**Server/OS Information**

|  |  |  |
|----|----|----|
| **Server identification** | **Representative Query** | **Notes** |
| Host name where the instance is running | `select host_namefrom v$instance;` | You can also obtain this information by running the following from bash: `hostname` or `uname --n` |
| Operating system platform | `select platform_name from v$database ---(10g)` | Similar information is returned if you run `uname --s` |

**File Information**

|  |  |  |
|----|----|----|
| **Oracle file locations** | **Representative Query** | **Notes** |
| Control files | `select namefrom v$controlfile;` | Location of the database control files. The init.ora parameter control_files also contains this information. |
| Datafiles | `select file_name` `from Dba_data_files;` | Location of the database datafiles |
| Temp files | `select file_namefrom Dba_temp_files;` | Location of database temporary files |
| Log files | `select memberfrom v$logfile;` | Location of redo logs |
| Archived logs | `select namefrom v$archived_log` | Location of archived redo logs. The init.ora parameters log_archive_dest_n also contain this information. This query will not return results if your database is not in Archivelog mode. |
| Flash recovery area | `select namefrom v$recovery_file_dest` | The directory being used in an Oracle 10g installation for the flash recovery area. The init.ora parameter db_recovery_file_dest also contains this information. |
| Other points of access on the file system indicated by parameters | `select *from v$parameter` `where value like '%/%`’ `or` `value like '%/%';` | The results of this query can vary significantly, depending on your installation and version of Oracle Database. Parameters that may be returned include: spfilestandby_archive_destutl_file_dirbackground_dump_dest user_dump_destcore_dump_destaudit_file_destdg_broker_config_file1dg_broker_config_file2 |
| Programmatic access to the file system | `select directory_path from dba_directories` | The Oracle UTL_FILE_DIR parameter and DIRECTORY database objects can be used to access files that are not a part of standard database functioning. |

**Process Information**

|  |  |  |
|----|----|----|
| **Processor/Processes** | **Representative Query** | **Notes** |
| Session Processes | `select p.spid, s.username, s.program` `from v$process p, v$session s` `where p.addr=s.paddr` `order by 2, 3, 1` | The spid can be correlated with ps –ef results to compare the information available within the database with OS information for a given process. |
| Processes related to parallelism | `select slave_name, statusfrom v$PQ_SLAVE` | Many aspects of Oracle Database, such as loading, querying, object creation, recovery, and replication, can take advantage of parallelism to speed up activities that can be broken down. The parameter parallel_threads_per_cpu sets an instance’s default degree of parallelism. |

**Memory Information**

|  |  |  |
|----|----|----|
| **Memory** | **Representative Query** | **Notes** |
| Program Global Area | `select * from V$PGASTAT` | The parameter pga_aggregate_target is used to configure memory for all dedicated server connections. Linux utilities, including vmstat and top, can be used to monitor memory usage. |
| System Global Area | `select * from v$sga` | The parameters SGA_MAX_SIZE and SGA_TARGET are used to configure dynamic memory allocation features of Oracle Database 10 g. Other parameters can be used to manually allocate memory for particular purposes. Again, various Linux utilities are available to monitor memory allocation. |

BASH Scripts

Scripts are either called as part of an automated process (with no human intervention) or run interactively, with a user responding to prompts. As long as you have executable permission for a file, you can run it from the command line by typing its name. If you do not have executable permission for the file but do have read permission, you can run the script by preceding it with `sh`.

If a script is designed to be run without user input, several options are available for calling it. You can run a script in the background and continue even if you disconnect, by entering a command in the following form:

```
nohup /path_to_dir/myscript_here.sh &
```

This can be useful for running scripts that take a long time to complete. The `at` command can be used to execute a script in the future, and `cron` can be used to schedule scripts to execute on a recurring basis.

The following examples cover the essential aspects of providing output to view (using `echo`), looping, conditional logic, and variable assignment.

**print_args.sh.** Arguments are words to the right of the command name that are passed into the script. To access the first parameter, you use the `$1` variable. The `$0` variable contains the name of the script itself. The `$#` variable contains the number of arguments in the script. A handy way to iterate through all of the parameters passed involves the use of a `while` loop and the shift command. This command is what lets you iterate through all the arguments in the argument list (rather than remaining in an infinite loop).

```
while [ $# -ne 0  ]
do
        echo $1
        shift
done
```

If a script takes a filename as an argument (or prompts a user for a filename) and the file will be read later in the script, it is advisable to check whether it is accessible and readable. For example, a recovery script that involves the selection of a backed-up control file might prompt the user to make a selection that will be used later in the script to restore the file.

```
if [ ! -r $1 ]; then # not exists and is readable
                        echo "File $1 does not exist or is not readable."
                exit;
fi
```

The sequence of characters

```
   
if [ ! -r $1 ];
```

is what actually performs the test. If the contents between the brackets evaluate to true, the commands that appear between if and fi will be executed. The actual test appears between the brackets. The exclamation point serves to negate the test you are doing. The `-r` option checks to see if the file is readable. What is being tested in this particular case is the first argument being passed to the script. By using a different test ( `-d` ), you can check to find out if a given entry is a directory (see is_a_directory.sh).

**do_continue.sh.** This example is a simple representative sequence of commands that can be used to read user input for various purposes. Before running a process that can result in data loss or other undesirable results under certain conditions that are not determinable from within the script, it is advisable to include a prompt asking if the user actually wants the script to execute the next command or commands. The following example asks if the user wants to continue, reads a variable named `doContinue` from the command line, and evaluates what the user entered. If the user enters anything other than “y,” that person is informed that the script is “quitting” and it exits without executing the remainder of the script following the end of the if block ( `fi`).

```
doContinue=n
echo -n "Do you really want to continue? (y/n) " 
read doContinue

if [ "$doContinue" != "y" ]; then
      echo "Quitting..."
      exit
fi
```

It is imperative that only users with the correct permissions and environment run a given script. A useful check in a script tests the user who is attempting to run the script. If you enclose a command within back-quote ( `‘`) characters, the results of the command can be returned to the script. The following example retrieves the currently logged-on user, by using `whoami`, and displays the date, by using the date command later in the script.

```
       
echo "You are logged in as ‘whoami‘";

if [ ‘whoami‘ != "oracle" ]; then
  echo "Must be logged on as oracle to run this script."
  exit
fi

echo "Running script at ‘date‘"
```

Scripts written to interact with Oracle Database sometimes require the entry of sensitive information such as a database password. The `stty --echo` command turns off the screen echo, so that the information entered for the subsequent read command will not appear on the screen. After the sensitive information has been read and stored in a variable ( `pw` in the example below), the display can be turned back on with `stty echo`.

```
       
stty -echo    
        echo -n "Enter the database system password:  "
        read pw
stty echo
```

Oracle Scripts

Some files reside in a fixed location for a given Oracle install. You can determine the Oracle inventory by viewing the /etc/oraInst.loc file. The /etc/oratab file identifies databases (and other Oracle programs) installed on the server.

**get_inv_location.sh.** This script is a bit less intuitive than the previous examples. By breaking down the script into its component commands, you will get a better understanding of what is being accomplished.

To determine inventory location, you are going to pipe the results of the `cat` command (which displays the contents of the file) to `grep` (a utility that prints lines that match a given pattern). You are searching for lines that contain the literal `inventory_loc` .

```
cat /etc/oraInst.loc | grep inventory_loc
```

If there is more than one inventory location due to multiple installs, you want to exclude lines commented out with a `#` . The `--v` option excludes lines that contain the given pattern.

```
       
cat /etc/oraInst.loc |grep -v "#"|grep inventory_loc
```

The result of this command will look something like this:

```
inventory_loc=/u01/oraInventory
```

You can redirect standard out to a file, using the \> redirection. If the file does not exist, it is created. If it does exist, it is overwritten.

```
       
cat /etc/oraInst.loc|grep -v "#"|grep inventory_loc > tmp
```

Once you have the record that indicates the inventory location, you want to remove the portion of the record prior to the equal sign. This time you pipe the results of the cat command to awk (a pattern-scanning and processing language often used to split up variable-length fields), which essentially tokenizes the string. The `--F` option directs awk to use the equal sign as the delimiter. You then print the second token of this string ( `$2` ), which is everything to the right of the equal sign. The result is our inventory location `(/`u01/oraInventory).

```
cat tmp | awk -F= '{print $2}'
```

There is no particular reason to allow the temporary file (tmp) to remain, so it can be removed.

```
rm tmp
```

**list_oracle_homes.sh.** If you would like to determine the ORACLE_HOME for a given database, you have several options. You can log on as the database user and `echo` the \$ORACLE_HOME variable. You can also search through the /etc/oratab file and select the name associated with a given instance. Database entries in this file are of the form

```
   
$ORACLE_SID:$ORACLE_HOME:<N|Y>:
```

The following one-liner prints out the ORACLE_HOME of an entry with an ORACLE_SID of TESTDB:

```
cat /etc/oratab | awk -F: '{if ($1=="TESTDB") print $2 }'
```

However, what if you have a requirement that an operation needs to be performed on each ORACLE_HOME listed in the /etc/orainst file? You can iterate through such a list by utilizing the following code snippet.

```
dblist=‘cat /etc/oratab | grep -v "#" | awk -F: '{print $2 }'‘

for ohome in $dblist ; do
  echo $ohome
done
```

The `dblist` variable is being used as an array. All ORACLE_HOME paths are held by this variable. A `for` loop is used to iterate through this list, and each entry is assigned to the variable `ohome` and then echoed to standard out.

**search_log.sh.** A variety of logs are generated by Oracle products, and you might be interested in monitoring them. The database alert log contains messages that are critical to database operations. Log files are also generated when products are installed or deinstalled and when patches are applied. The following script iterates over a file passed to it as an argument. If any lines are found that contain ORA-, an e-mail message is sent to a designated recipient.

```
   
cat $1 | grep ORA- > alert.err

if [ ‘cat alert.err|wc -l‘ -gt 0 ]
then
        mail -s "$0 $1 Errors" administrator@yourcompany.com < alert.err
fi
```

The specific test being performed is a count of the number of words that exist in the file alert.err, which is written when you redirect to alert.err. If the word count ( `wc`) is greater than ( `-gt`) zero, the contents of the if block will execute. In this case, you are using `mail` ( `send mail` might also be used) to send a message. The title of the message contains the script being executed ( `$0`), the name of the log being searched ( `$1`), and the lines that matched our initial search ( `ORA-`) as the body of the message.

Environmental variables such as ORACLE_HOME, ORACLE_BASE, and ORACLE_SID can be used to locate resources that are not in a fixed location in the Linux environment. If you are administering an Oracle E-Business Suite 11i application instance, you have numerous other environmental variables that can be used to locate resources. These include APPL_TOP, TWO_TASK, CONTEXT_NAME, and CONTEXT_FILE, to name a few. To see a complete list in your environment, execute the following command and examine the resulting file (myenv.txt):

```
env > myenv.txt
```

Various combinations of these environmental variables can be used as the location of a file being searched. For example, an alert log location might be designated as

```
   
$ORACLE_BASE/admin/$ORACLE_SID/bdump/alert_$ORACLE_SID.log
```

Based on the principles introduced in this script, a larger one can be written and scheduled to execute at periodic intervals that will search the contents of the alert log (or another file of interest) and send an e-mail if any errors exist. Then the contents of the log can be moved to another file, so that only the most recent error messages will be sent via e-mail.

**Oracle Recovery Manager Scripts.** Oracle Recovery Manager (RMAN) is a utility that can be used to administer database backup and recovery. This greatly simplifies administration on multiple platforms, because all backup scripts can be written to be run by RMAN, reducing the amount of platform-specific code. RMAN can be called by the underlying operating system and passed a script. For example, a cold (cold.sh) backup might consist of the following script:

```
   
#!/bin/bash
rman target / <<EOF
shutdown immediate;
startup mount;
backup spfile;
backup database;
alter database open;
delete noprompt obsolete;
quit;
EOF
```

Line 1 indicates that you are using the bash shell. Line 2 invokes Oracle Recovery Manager and specifies the OS user login to the target database (specified in the environmental variable \$ORACLE_SID). The `<<EOF` following this indicates that the subsequent commands will be passed into RMAN to be processed. The EOF on the last line indicates that you have reached the end of the series of commands to be passed into RMAN. RMAN is then used to shut down the database, start up and mount the database, and proceed to back up the server parameter file and the contents of the database. The database is then opened. Any backups that are older than those specified in the retention policy are then deleted. See the RMAN documentation to construct a backup that is relevant for your situation.

Nightly backups are commonly scheduled and run automatically. The script above could be called and the contents of standard out sent to an e-mail address with the following command:

```
sh cold.sh | mail -s"Backup ‘date‘" administrator@yourcompany.com
```

Other Oracle utilities can be run from within shell scripts as well. The `tnsping` utility can be used to see if a given Oracle connection identifier can contact the listener. You might run this utility to check for connection problems:

```
tnsping ptch04 |grep TNS-
```

Database exports and imports (traditional and data pump) are also good candidates for the scripting of repeating processes.

**Database Installation.** Many of the steps involved in the setup of a database can be automated. Before you install Oracle 10g on Linux, you need to run various tests to verify the minimum required version of packages and the settings of kernel parameters. You can query the version of a package by using the `rpm` command with the `--q` option.

```
   
rpm -q compat-libstdc++
```

You can determine various aspects of the system by looking at the /proc “virtual” or “pseudo” file system. It contains not real files but, rather, runtime system information that can be viewed as if it resided in files. For instance, /proc/meminfo contains memory information for the system, and grep `MemTotal /proc/meminfo` displays the total memory of the system. By using `awk` as you did earlier, you could isolate the amount of memory in kilobytes, by using:

```
grep MemTotal /proc/meminfo | awk '{print $2}'
```

Such a command could be used in the context of a script that would do comparisons and respond accordingly (even updating the system itself). The sample scripts 10gchecks_kernel.sh and 10gchecks.sh simply display current and recommended versions and settings based on the Oracle documentation.

**Database Monitoring.** The `ps` command can be used to report process status and to check to see if a database, listener, script, or any other process of interest is running. If you want to list all the databases that are currently running on a server, you can run the following command:

```
   
echo "‘ps -ef | grep smon|grep -v grep|awk '{print $8}'| awk -F \"_\" 
'{print$3}'‘"
```

Although this is functional, it is a bit difficult to understand at first glance. The first command, `ps` (using the `-ef` options for a full listing of all processes), finds all of the processes running on the server. The next, `grep`, searches for SMON (the Oracle System Monitor background process), which indicates that the database is running. You want to remove entries that refer to the `grep` command itself, which is being run. You then use `awk` to locate the eighth column in the listing, which contains the system monitor process name in the form ora_smon\_\<oracle_sid\>. The last instance of awk then uses the underscore character as the delimiter to search for and print the database name that owns the SMON process. The underscore character needs to appear in quotes, and the backslash is used before each of the quotes to escape those quotes (because the entire string appears within a set of double quotes).

**exec_sql.sh.** As mentioned earlier, it is possible to query the database from a shell script provided the user has access to sqlplus. The following example returns a (space-delimited) list of machines that are currently maintaining sessions in the database:

```
   
#!/bin/bash

output=‘sqlplus -s "/ as sysdba" <<EOF
       set heading off feedback off verify off
       select distinct machine from v\\$session;
       exit
EOF
‘

echo $output
```

This script is similar to the previous RMAN script in that you are inputting commands into another program. A local OS authenticated connection is made to the database as sysdba. To prevent extraneous messages from being returned, this script turns off the heading, feedback, and verify options of SQL\*Plus. The query is executed and SQL\*Plus is exited.

Note the double-backslash before the dollar sign in the view name. These are required escape sequences within the string: The first slash escapes the second slash, which escapes the dollar sign. Again, not pretty, but functional.

As mentioned previously, if you are going to be writing something that requires extensive database access, shell scripting is not the best option. It might be better to rewrite the script in PL/SQL, Perl (which uses syntax similar to that used in shell scripting), Python, Java, or another language of your choice.

Conclusion

Shell scripting can be an effective tool for quickly automating repetitive and error-prone administration tasks. The examples in this article provide an introduction to the possibilities available but are far from comprehensive. Every system has distinct quirks and foibles and a unique configuration. An administrator will develop unique solutions to meet the needs of the particular system.

---
**Casimir Saternos** is an Oracle Certified DBA, IBM Certified Enterprise Developer, and Sun Certified Java Programmer based in Allentown, Pa.

<http://www.oracle.com/technetwork/articles/linux/saternos-scripting-088882.html>
