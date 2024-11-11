# Linux Shell

### Shell vs Environment variables

_Shell variables_ are only present in the shell in which they were defined. They are not automatically available to the programs or scripts that are started by the shell. To create or modify a shell variable, you simply assign a value to a name without spaces:

    VARIABLE_NAME=value

Here, `VARIABLE_NAME` is a shell variable. Notably, this variable will not be recognized in any programs or scripts launched from the current shell session.

_Environment variables_ are a special type of shell variable that are inherited by any child processes started from the shell. Environment variables are set using the `export` command.

### export

This command is a built-in utility of Linux Bash shell, used to set _environment variables_ for the current shell session and its child processes. When you run `export VARIABLE_NAME=value` you are creating or modifying an environment variable in your current shell session and ensuring that any processes started from this session inherit this environment variable.
  
For example, if you run
      
    export PATH=/new/directory:$PATH
      
you are modifying the `PATH` environment variable to include a new directory. Any program or script you run from this shell session will have access to this updated `PATH` variable. This is crucial for configuring the runtime environment of software, managing paths, and setting up variables that programs depend on.

To print a list of all exported environment variables in the current shell session run

    export -p

where the `-p` option stands for "print".