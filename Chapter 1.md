## 1.1 Definition and Function
The #shell is a "special program" used as the interface between the user and the "heart" of the UNIX operating system. 
The #kernel is loaded into memory at boot-up time and manages the system by:
* creating and controlling processes
* managing memory, file systems, and communications
**Typing a command**
After Typing a command, the #shell parses the command line and handles the wildcards, redirection, pipes, and job control. It then searches for the command and if it's found, it will execute the command.

### 1.1.1 The Three Major Shells
***Bourne Shell*** (AT&T Shell)
* Standard shell, preferred for scripting due to being simpler and faster
* "concise, compact, and fast"
* Default sign is a '$'
***C Shell*** (Berkeley shell)
* added command line history, built-in arithmetic, filename completion, and job control
* Default sign is a '$'
***Korn Shell*** (superset of the Bourne shell)
* added editable history, aliases, functions, regular expression wild cards, built-in arithmetic, job control, coprocessing, and special debugging features
* Bourne shell is almost fully upward compatible with the Korn shell so the older Bourne shell programs run in Korn shell
* Default sign is '$'

### 1.1.2 History of the Shell
Grew due to usage for automating system administration tasks. 

### 1.1.3 Uses of the Shell
The #shell parses commands into tokens and evaluating them. It interprets line from a shell script and it need not be compiled. 

### 1.1.4 Responsibilities of the shell
1) Reading input and parsing the command line
2) Evaluating special characters
3) Setting up pipes, redirection, and background processing
4) Handling signals
5) Setting up programs for execution

## 1.2 System Startup and the Login Shell
The first process run on startup is called *init*. Each process is given a process identification number, called the PID. 
Process of logging in:
1. */bin/login* program verifies login name and password by checking the *passwd* file
2. If the username is there, it will run your password through an encryption program to check the password
3. If verified, the *login* program sets up an environment consisting of the *HOME, SHELL, USER* and *LOGNAME* variables are assigned values. These values are extracted from the information in the *passwd* file
	1. *HOME* is assigned your home directory
	2. *SHELL* is assigned the name of the login shell (the last entry in the *passwd* file)
	3. *USER* and/or *LOGNAME* are assigned your login name
4. When *login* is finished, it would execute the program found in the last entry of the *passwd* file which is normally the type of #shell. Could be */bin/csh* meaning the C shell. */bin/sh or null*, the Bourne shell starts up. */bin/ksh* the Korn shell would execute. The particular shell is called the *login shell*
The any initialization files are ran

### 1.2.1 Parsing the Command Line
Commands are broken in tokens and executed.
Order of processing is as follows:
1. History substitution (if/a)  "!251"
2. Command line is broken up into tokens
3. History is updated (if/a)
4. Quotes are processed
5. Alias substitution and functions are defined (if/a)
6. Redirection, background, and pipes are set up
7. Variable substitution (*$user, $name,* etc) is performed
8. Command substitution is performed (echo for *today* is '*date*')
9. Filename substitution, aka globbing
10. Program Execution

### 1.2.2 Types of Commands
Commands can either be an alias, a function, a built-in, a program, or an executable program on disk. 
Aliases are abbreviations for existing commands
Functions only apply to the Bourne and Korn shell and are groups of commaned organized as separate routines. Both aliases and functions are defined within the shell's memory. 
Built-in commands are internal routines in the shell
Executable programs reside on disk
The #shell evaluates commands in this order:
1. Alias
2. Built-in commands
3. Functions (Korn and Bourne shell)
4. Executable programs

## 1.3 Processes and the Shell
A #process consists of the executable program, its data and stack, program and stack pointer, registers, and all the information needed for the program to run. Process groups are identified by their group's PID.  
A **system call** is a request to the kernel services and is the only way a process can access the system's hardware

### 1.3.1 Creating Process
**The *fork* System call** creates a duplicate of the calling process. The new process is called  the child and the process that created it is called the parent. The child has a copy of the parent's environment, open files, real and user identifications, unmask, current working directory, and signals. 
**The *wait* system call* will cause the parent to be asleep (wait, suspended) while the child handles things such as redirection, pipes, and background processing. this sys call is to ensure the parent process terminates properly
	If **wait** is successful, the PID of the dead child and it's exit status is returned.
	If the parent doesn't **wait** and the child exits, the child is put into a "zombie state" or suspended animation and will stay like that until the parent called wait or the parent dies. (To remove zombie processes, the system must be rebooted)
	If the parent dies before the child, the *init* process adopts any orphaned zombie process
**The *exec* System Call** executes the command typed. When an executable program is found, the #kernel loads this new program into memory in place of the shell that called it. The child shell is then overlaid with the new program. Like when running an executable, the shell process is still going but the child process is happening first then dies and it goes back to the parent process. 
**The *exit* System Call** allows a program to terminate at any time. When a child terminates, it send a signal *sigchild* and waits for the parent to accept it's exit status. This status is between 0-255. 0 means it executed successfully. Non-zero means the program failed in some way. 