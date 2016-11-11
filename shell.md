# Shell Scripting

## Interpreter

```sh
#!/bin/bash
```
There are a number of interpreters available to execute a shell script. bash, zsh, ksh, etc. Using shebang we specify which interpreter to use. If you do not supply a shebang and specify an interpreter on the first line of the script the commands in the script will be executed using your current shell.


## Variables

```sh
MY_VARIABLE="SARONIA"
echo "I like the $MY_VARIABLE project!"

# Optionally one could use curly braces to access variables.
echo "I like the ${MY_VARIABLE} project!"

# Assign the output of a command to a variable.
SERVER_NAME=$(hostname)
```

## Tests

Tests start with `[`, are followed by the condition to test for, and end with `]`.

```sh
# The below test checks whether the file /etc/passwd exists.
# If it does, the test returns an exit code of 0 (success).
# If it does not, the test returns an exit code of 1 (failure).
[ -e /etc/passwd ]
```

### File
* `-d FILE`: True if file is a directory
* `-e FILE`: True if file exists
* `-f FILE`: True if file exists and is a regular file
* `-r FILE`: True if file is readable by you
* `-s FILE`: True if file exists and is not empty
* `-w FILE`: True if file is writable by you
* `-x FILE`: True if file is executable by you

### String
* `-z STRING`: True if string is empty
* `-n STRING`: True if string is not empty
* `STRING1 = STRING2`: True if the strings are equal
* `STRING1 != STRING2`: True if the strings are not equal

### Arithmetic operators
* `arg1 -eq arg2`: True if arg1 is equal to arg2
* `arg1 -ne arg2`: True if arg1 is not equal to arg2
* `arg1 -lt arg2`: True if arg1 is less than arg2
* `arg1 -le arg2`: True if arg1 is less than or equal to arg2
* `arg1 -gt arg2`: True if arg1 is greater than arg2
* `arg1 -ge arg2`: True if arg1 is greater than or equal to arg2

## If-statement

```sh
MY_SHELL="bash"

if [ "$MY_SHELL" = "bash" ]
then
	echo "You seem to be using the bash shell script interpreter."
elif [ "$MY_SHELL" = "zsh" ]
then
	echo "You seem to be using the zsh shell script interpreter."
else
	echo "You seem to be using an exotic shell script interpreter."
fi
```
Note that `else` and `elif` are optional constructs of the if-statement.

## For-loop

```sh
for TECHNOLOGY in phoenix elixir react graphql relay
do
	echo "In 2017, you better be learning ${TECHNOLOGY}!"
done

# Alternatively, extract the list into a separate variable.
TECHNOLOGIES="phoenix elixir react graphql relay"

for TECHNOLOGY in $TECHNOLOGIES
do
	echo "In 2017, you better be learning ${TECHNOLOGY}!"
done
```

```sh
# Let's prepend today's date to all jpg files located in the root of the script.

PICTURES=$(ls *jpg)
DATE=$(date +%F)

for PICTURE in $PICTURES
do
	echo "Renaming ${PICTURE} to ${DATE}-${PICTURE}"
	mv ${PICTURE} ${DATE}-${PICTURE}
done
```

## Positional parameters

```sh
script.sh parameter1 parameter2 parameter3
```
Positional parameters are variables that contain the contents of the command line. The variables are `$0` through `$9`.

* `$0`: "script.sh"
* `$1`: "parameter1"
* `$2`: "parameter2"
* `$3`: "parameter3"

```sh
#!/bin/bash

USER=$1 # The first parameter is the user.

echo "Executing script: $0"
echo "Archiving user: $USER"
```
Assign more meaningful variable names to positional parameters.

```sh
#!/bin/bash

echo "Executing script: $0"
for USER in $@
do
	echo "Archiving user: ${USER}"
done
```
```sh
./archive_user.sh seetha dugorim

# >> Executing script: ./archive_user.sh
# >> Archiving user: seetha
# >> Archiving user: dugorim
```
Access all positional parameters starting from `$1` (not `$0`) in a for-loop.


## Accepting User Input (STDIN)

```sh
#!/bin/bash

read -p "Enter a user name: " USER
echo "Archiving user: ${USER}"
```

## Exit Status / Return Code

* Every command returns an exit status
* Range from 0 to 255
* 0 = success
* Other than 0 = error condition
* Use this for error checking

### Checking the Exit Status

```sh
ls /not/here
echo "$?"

# >> 2
```
`$?` contains the return code of the previously executed command.


```sh
# Use ping command to check the network connectivity to saronia.com.
# The -c flag tells ping to only send one packet.

HOST="saronia.com"
ping -c 1 $HOST
if [ "$?" -eq "0" ]
then
	echo "${HOST} reachable."
else
	echo "${HOST} unreachable."
fi


# Alternatively, assign the value of the return code to a variable.
HOST="saronia.io"
ping -c 1 $HOST
RETURN_CODE=$?

if [ "$RETURN_CODE" -ne "0" ]
then
	echo "$HOST unreachable."
fi
```
Write logic that depends on the return code of a particular command.


### && and ||

* `&&` = AND => `mkdir /tmp/bak && cp test.txt /tmp/bak`

* `||` = OR => `cp test.txt /tmp/bak/ || cp test.txt /tmp`

The logical `AND` and `OR` both rely on the concept of the return code. You can chain together multiple commands.

### $_

The `$_` returns the output of the previously executed command.

```sh
git clone https://github.com/soosap/101.git 101 && cd $_
```


### Exit command

```sh
#!/bin/bash

HOST="saronia.com"
ping -c 1 ${HOST}
if [ "$?" -ne "0" ]
then
	echo "$HOST unreachable."
	exit 1
fi
exit 0
```

## Functions

```sh
function hello() {
	echo "Hello!"
}

# Call the function. Note that we are not putting parenthesis.
hello
```
```sh
# Functions may also call other functions

function hello() {
	echo "hello"
	now
}

function now() {
	echo "It's $(date +%r)"
}

hello
```

```sh
# Functions can also accept parameters and return Exit Codes.

function hello() {
	echo "Hello $1"
}

hello Seetha
hello Dugorim
# >> Hello Seetha
# >> Hello Dugorim

function hi() {
	for NAME in $@
	do
		echo "Hi ${NAME}!"
	done
	return 0
}

hi Seetha Dugorim Sapiras
# >> Hi Seetha
# >> Hi Dugorim
# >> Hi Sapiras
```
### Example: function backup_file

```sh
function backup_file() {
	if [ -f $1 ]
	then
		local BACK="/tmp/$(basename ${1}).$(date +%F).$$"
		echo "Backing up $1 to ${BACK}"

		cp $1 $BACK
		# Note: that the exit status of the function will be the exit status of the cp command.
	else
		# The file does not exist. Therefore return a non-zero exit status.
		return 1
	fi
}

# Execute backup_file function
backup_file /etc/hosts
if [ $? -eq 0 ]
then
	echo "Backup succeeded!"
fi

```
* By default variables specified anywhere are **global** unless you add the `local` keyword. The keyword can only be added inside functions. It's added in front of the `BACK` variable's first occurence.
* The `[ -f $1 ]` test checks whether the first argument is a file and also whether it exists.
* The `basename` command removes any leading directory components and returns just the filename. The basename of `/etc/hosts` is just `hosts`.
* The `date` command is using a nice format of year-month-day all separated by dashes.
* `$$` represents the `pid` (process id) of the currently running shell script. It can be used to protect backups when running the same script multiple times on the same day. Every time the script is run a new unique `pid` will be assigned and thus backups won't be overwritten.


## Wildcards (Globbing)

* `*` - matches zero or more characters
	* `*.txt`
	* `a*`
	* `a*.txt`
* `?` - matches exactly one character
	* `?.txt` - match all files that have exactly one character preceding `.txt`
	* `a?` - match all 2 character files that begin with an `a`
	* `a?.txt`
* `[]` - a character class
	* matches any of the characters included between the brackets
	* matches exactly one character
	* `[aeiou]` - matches exactly one vowel
	* `ca[nt]*`
		* can
		* cat
		* candy
		* catch
* `[!]` - a negative character class
	* matches any of the characters NOT included between the brackets
	* matches exactly one character
	* [!aeiou]* - matches any word that does not start with a vowel
		* baseball
		* cricket
* `[a-g]` - wildcard range
	* a, b, c, d, e, f, or g
* `[3-6]` - another wildcard range
	* 3, 4, 5, or 6
* Named Character Classes
	* `[[:alpha:]]`
	* `[[:alnum:]]`
	* `[[:digit:]]`
	* `[[:lower:]]`
	* `[[:space:]]`
	* `[[:upper:]]`
* `\` - match a wildcard character by escaping it
	* use if you want to match a wildcard character
	* `*\?` - match all files that end with a question mark
		* done?

### Example: Using wildcards in a for-loop

```sh
#!/bin/bash

cd /var/www
for FILE in *.html
do
	echo "Copying ${FILE}"
	cp $FILE /var/www-just-html
done

# >> Copying about.html
# >> Copying contact.html
# >> Copying index.html
```
```sh
#!/bin/bash

for FILE in /var/www/*.html
do
	echo "Copying ${FILE}"
	cp $FILE /var/www-just-html
done

# >> Copying /var/www/about.html
# >> Copying /var/www/contact.html
# >> Copying /var/www/index.html
```


## Case statement

### Example 1

```sh
case "$1" in
	start)
		/usr/sbin/sshd
		;;
	stop|STOP)
		kill $(cat /var/run/sshd.pid)
		;;
	*)
		echo "Usage: $0 start|stop" ; exit 1
		;;
esac
```

* This case statement examines the value of `$1`
* `$1` is the first argument provided to the shell script
* Pattern is case-sensitive, i.e. `START` !== `start`
* The `*)` will act as a catch-all and match anything else
* Use the `|` symbol to allow both `STOP` and `stop`
* Once a pattern has been matched, the commands following that pattern are executed until the `;;` is reached.


### Example 2

```sh
read -p "Enter y or n: " ANSWER
case "$ANSWER" in
	[yY]*)
		echo "You voted yes."
		;;
	*)
		echo "You answered something else."
		;;
esac
```
* You can use **wildcards** and **character classes**


## Logging

### syslog

* The syslog standard uses **facilities** and **severities** to categorize messages
	* Facilities: kern, user, mail, deamon, auth, local0, local7
	* Severities: emerg, alert, crit, err, warning, notice, info, debug
* Log file locations are configurable:
	* /var/log/messages
	* /var/log/syslog

### Logging w/ logger

* The logger utility
* By default creates `user.notice` messages
* `logger "Message"`
* `logger -p local0.info "Message"`
* `logger -t myscript -p local0.info "Message"` - use `-t` to additionally print a tag next to the log message
* `logger -t myscript -s -p local0.info "Message"` - use the `-s` option to not only log the message to a file but also to display it on the screen
* `logger -i -t myscript "Message"` - use `-i` to additionally print the process id (pid)
	* `Nov 11 01:22:53 soosap myscript[12986]: Message`

### Custom "logit" function

```sh
function logit() {
	local LOG_LEVEL=$1
	shift
	MSG=$@
	TIMESTAMP=$(date +"%Y-%m-%d %T")

	if [ $LOG_LEVEL = 'ERROR' ] || $VERBOSE
	then
		echo "${TIMESTAMP} ${HOST} ${PROGRAM_NAME} [${PID}]: ${LOG_LEVEL} ${MSG}"
	fi
}
```
```sh
logit INFO "Game over. Something went bad."
```
* The `shift` key word shifts the positional parameters to the left. This means that the special variable `$@` contains everything except the first parameter which has already been used prior calling `shift`. That's how it is possible that `$@` equates to the second argument and everything that follows there after.
* Rather than `echo` we could have used `logger` instead.


## While-loop

### Examples

#### Example: Infinite loop
```sh
while true do
	command N
	sleep 1
done
```
* Make the condition always return true
* This can be used when starting the shell script in daemon mode

#### Example: Repeat X times
```sh
INDEX=1
while [ $INDEX -lt 6 ]; do
	echo "Creating project-${INDEX}"
	mkdir /usr/local/project-${INDEX}
	((INDEX++))
done
```
* Control the number of time how often a script is executed
* Use `((INDEX++))` to increment the iterator

#### Example: Check user input
```sh
while [ "$CORRECT" != "y" ]
do
	read -p "Enter your name: " NAME
	read -p "Is ${NAME} correct? " CORRECT
done
```
* Use while-loop to check user input
* Snippet is repeated until the user confirms with `y`

#### Example: Return Code of Command
```sh
while ping -c 1 app1 > /dev/null; do
	echo "app1 still up..."
	sleep 5
done

echo "app1 down, continuing..."
```
* In the above example we want to execute some type of backup or maintenance tasks on a particular database server. Before we issue our commands though we want to wait until the application server is off the network so that there are no pending write operations.
* We redirect the `STDOUT` of the ping command to `/dev/null` such that it is not displayed on the screen. The reason being that we are not actually interested in the output itself, but rather whether or not the command has been executed with return code zero.
* `-c` flag specifies that only one packet should be sent

#### Example: Read file line-by-line
```sh
LINE_NUM=1
while read LINE
do
	echo "${LINE_NUM}: ${LINE}"
	((LINE_NUM++))
done < /etc/fstab
```
* In this case, `/etc/fstab` is the file we want to parse.
* We use the `LINE_NUM` variable to keep track of the line numbers.

#### Example: Read output of command line-by-line
```sh
grep xfs /etc/fstab | while read LINE
do
	echo "xfs: ${LINE}"
done
```

```sh
FS_NUM=1
grep xfs /etc/fstab | while read FS MP REST
do
	echo "${FS_NUM}: file system: ${FS}"
	echo "${FS_NUM}: mount point: ${MP}"
	((FS_NUM++))
done
```
* We deconstruct a line into three pieces:
	* the first word (FS)
	* the second word (MP)
	* and everything that follows thereafter (REST)
* Keep track of the current line number by incrementing the `FS_NUM` variable


### "break"-statement

```sh
while true
do
	read -p "1: Show disk usage. 2: Show uptime. " CHOICE
	case "$CHOICE" in
		1)
			df -h
			;;
		2) uptime
			;;
		*)
			break
			;;
	esac
done
```
* Use the `break` statement to exit a while-loop before its normal ending
* `break` can be used with any type of loop, i.e. for-loop


### "continue"-statement

```sh
mysql -BNe 'show databases' | while read DB
do
	db-backed-up-recently $DB
	if [ "$?" -eq "0" ]
	then
		continue
	fi

	# Only if not backed up recently, execute this:
	backup $DB
done
```
* Restart the loop at the next iteration before the loop completes using the `continue` statement
* Any commands that follow the `continue` statement in the loop will not be executed
* Execution continues back at the top of the loop and the while condition is examined again


## Debugging

### `-x` trace

#### Entire file
```sh
#!/bin/bash -x

TEST_VAR="test"
echo "$TEST_VAR"

# >> + TEST_VAR=test
# >> + echo test
# >> test
```
* The `#!/bin/bash -x` specifies that `-x`-trace shall be applied to the entire file
* The lines that start with a `+` sign are the commands that have been executed from the script
* Right after follows the actual output from executing a particular command
* Thus, the output is mapped to the respective command producing it

#### Portion of file
```sh
#!/bin/bash

TEST_VAR="test"
set -x
echo $TEST_VAR
set +x
hostname

# >> + echo test
# >> test
# >> + set +x
# >> myhostname
```
* Set `-x` debugging on a portion of the script
	* Use `set -x` to indicate where from where after to enable debugging
	* Use `set +x` to indicate from where after to deactivate debugging


### `-e` exit-on-error

* `#!/bin/bash -ex`
* `#!/bin/bash -xe`
* `#!/bin/bash -e -x`
* `#!/bin/bash -x -e`

The exit-on-error `-e` flag can be combined with other options such as the `-x`-trace.

```sh
#!/bin/bash -ex

FILE_NAME="/not/here"
ls ${FILE_NAME}
echo ${FILE_NAME}

# >> + FILE_NAME=/not/here
# >> + ls /not/here
# >> ls: cannot access /not/here: No such file or directory
```
* `-e` can help to exactly pinpoint where an actual problem may be
* Due to `ls` returning w/ a non-zero return code the `echo` command is never executed

### `-v` trace

```sh
#!/bin/bash -v
TEST_VAR="test"
echo "$TEST_VAR"

# >> #!/bin/bash -v
# >> TEST_VAR="test"
# >> echo "$TEST_VAR"
# >> test
```

```sh
#!/bin/bash -vx
TEST_VAR="test"
echo "$TEST_VAR"

# >> #!/bin/bash -v
# >> TEST_VAR="test"
# >> + TEST_VAR=test 
# >> echo "$TEST_VAR"
# >> + echo test
# >> test
```
* Prints shell input lines as they are read
* Can be combined w/ other options
* `-v` prints before any substitutions or expansions have occurred


### Manual Debugging

* `DEBUG=true`
* `DEBUG=false`

```sh
#!/bin/bash
DEBUG=true
if $DEBUG
then
	echo "Debug mode is ON."
else
	echo "Debug mode is OFF."
fi
```

```sh
#!/bin/bash
DEBUG=true
$DEBUG && echo "Debug mode is ON."
```

```sh
#!/bin/bash
DEBUG=false
$DEBUG || echo "Debug mode is OFF."
```

#### Custom debug function
```sh
#!/bin/bash

function debug() {
	echo "Executing: $@"
	$@
}

debug ls
```
* `echo` name of the command to the screen
* One could use logging instead of printing to the screen

#### PS4 Syntax Highlighting

```sh
#!/bin/bash -x
PS4='+ $BASH_SOURCE : $LINENO :'
TEST_VAR="test"
echo "$TEST_VAR"

# >> + PS4='+ $BASH_SOURCE : $LINENO :'
# >> + ./test.sh : 3 : TEST_VAR=test
# >> + ./test.sh : 4 : echo test
# >> test
```
* Declare `PS4` variable to format the `-x` trace screen output
* By default `x` trace is indicated using the `+` sign in front of commands
* The `-x` trace output format deviates from its default after an explicit `PS4` has been set
* The output may be altered to further expose `$BASH_SOURCE` and `$LINENO`

```sh
#!/bin/bash -x
PS4='+ ${BASH_SOURCE}:${LINENO}:${FUNCNAME[0]}()'

function debug() {
	echo "Executing: $@"
	$@
}

debug ls

# >> + /test.sh:4:debug(): ls
```
