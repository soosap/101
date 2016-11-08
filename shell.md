# Shell Scripting

## Interpreter

```sh
#!/bin/bash
```
There are a number of interpreters available to execute a shell script. bash, zsh, ksh, etc. Using shebang we specify which interpreter to use.


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

Tests start with `[` are followed by the condition to test for and end with `]`.

```sh
[ -e /etc/passwd ]
``` 