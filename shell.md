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





