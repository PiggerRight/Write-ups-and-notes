# Linux Commands Notes

This is a small notes about almost Linux commands that I have learnt. Commands are categorized by their functions.

## Navigation & File Management

### cd

It changes your current directory to another directory.

Syntax:

```bash
cd [directory]
```

`[directory]`

- `[directory]` can be absolute or relative path.
- `[directory]` is omited or `[directory] == ~` — home directory.
- `[directory] == ..` — parent directory.
- `[directory] == ../..` — move up two levels and so on.
- `[directory] == /` — root directory.
- `[directory] == -` — previous directory.

### mkdir

Creates directories.

Syntax:

```bash
mkdir [options] [directories_name...]
```

`[options]`

- `-p` — create parent directories if they don't exist.

Examples:

```bash
mkdir projects
```

```bash
mkdir dir1 dir2 dir3
```

```bash
mkdir -p notes/linux/bandit
```

### rmdir

Removes directory. This only works if the directory is empty.

Syntax:

```bash
rmdir [directories...]
```

### rm

Removes file.

Syntax:

```bash
rm [options] [files...]
```

`[options]`

- `-r` — remove directory (can be non-empty), replace `[file]` by `[directory]`.
- `-f` — force to remove anyway.
- `-i` — ask for confirmation.
- `-I` — asf for confirmation each file and directory.

### mv

Rename file or directory.

Syntax:

```bash
mv [old_name] [new_name]
```

Move files.

Syntax:

```bash
mv [file] [directory]
```

### cp

Copy file or directory.

```bash
cp [file] [file]
```

```bash
cp -r [directory] [directory]
```

### touch

Creates an empty file. If the file already exists, `touch` updates its modification timestamp.

Syntax:

```bash
touch [options] [files...]
```

### pwd

Displays the absolute path of the current working directory. Useful for confirming where you are before navigating or accessing files.

Syntax:

```bash
pwd
```

### ls

List files and direcctories in `[path]` directory.

Syntax:

```bash
ls [options] [path]
```

`[path]`

- `[path]` can be absolute or relative path.
- If `[path]` is omitted or `[path] == .`, `[path]` equals to the current directory.
- If `[path] == ..`, `[path]` equals to the parent directory.
- If `[path] == ~`, `[path]` equals to the home directory.
- If `[path] == ../..`, `[path]` equals to the grandparent directory.
- If `[path] == /`, `[path]` equals to the root directory.

`[options]`

- `-l` — show detailed information:
  - File permissions
  - Number of links
  - Owner
  - Group
  - File size
  - Last modified date
  - File name
- `-a` — show hidden files, files beginning with `.` are hidden files.
- `-h` — human readable format for file size.
- `-R` — explore the whole directory tree rooted from `[path]`.
- `-t` — sort by last modified time.
- `-S` — sort by size, largest first.
- It is able to apply more than one option. (Eg: `-la`,...)

### chmod

Change the permissions of files and directories.

Syntax:
```bash
chmod [permission_number] [files...]
```

`[permission_number]` — a 3-digits integer and each digit defines the permission (read, write and execute) of user, group and others.

- Permission value:
  - Read    = 4
  - Write   = 2
  - Execute = 1
- Permission number: (Eg: 600)
  - 6 = Read (4) + Write (2)  = rw-  → Owner:  read and write
  - 0 = No permissions        = ---  → Group:  no permissions
  - 0 = No permissions        = ---  → Others: no permissions
  - Permission: -rw------- (we can see this in the `ls -l` permission collumn)
 
### Setuid

**Setuid** (Set User ID) is a special permission bit for executable files in Linux. When a setuid executable is run, the program runs with the **effective user ID (EUID) of the file owner** instead of the user who executed it.

For example:

```text
-rwsr-xr-x 1 alice alice program
```

The `s` in the owner's execute permission indicates that the **setuid bit** is enabled.

If `bob` executes the program:

```bash
./program
```

the process has:

```text
Real user ID (UID):      bob
Effective user ID (EUID): alice
```

Now, the program can perform actions with `alice`'s effective privileges.

Setuid itself does **not** define what actions the user can perform. It only causes the executable to run with the file owner's effective privileges. The **program's implementation** determines what can actually be done with those privileges.

For example, a setuid program can:

* Perform a fixed privileged action.
* Accept specific arguments and perform corresponding actions.
* Accept a command from the user and execute it with the owner's effective privileges.

Therefore, a setuid program that allows arbitrary command execution can be dangerous if the file owner has higher privileges.



---

## File Inspection & Text Processing

### cat

Display the contents of file(s).

Syntax:

```bash
cat [options] [files...]
```

`[files...]`

- Can be absolute or relative path.
- Can display multiple files.
- Use `*` to read all files in current directory.
- With `[files...]` have spaces:
  - Before the space we need to add `\`.
  - put them inside single quotes or double quotes. Eg: `'./--spaces in this filename--'`.
- If `[files...]` is provided, `stdin` reads from them.
- When `[files...]` is omitted, `stdin` reads from keyboard by default.

`[options]`

- `-n` — add line numbers.
- `-b` — add line numbers except blank lines.
- `-E` — show end of each line, the `$` marks the end of each line.
- `-T` — Tabs are displayed as `^I`, making them visible.
- `-A` — This combines several display options, making tabs, line endings, and other non-printing characters visible.

### head

Displays the first part of file(s) with separation part.

Syntax:

```bash
head [options] [files...]
```

`[options]`

- `-n [n]` or `-[n]` — read from the first to n-th line.
- `-c [n]` — read the first n bytes.

### sed

A stream editor to read and transform text.

Syntax:

```bash
sed [options] '[sed_command]' [files...]
```

`[options] '[sed_command]'`

- `-n 'np'` — print line `n` (`$` represent last line)
- `-n 'n,mp'` — print lines `n-m`
- `'nd'` — delete line `n` from the output
- `'nd;md'` — delete line `n` and `m` from the output
- `'s/old/new/'` — replace the first occurence of `old` with `new` on each line
- `'s/old/new/g'` — replace all occurences of `old` with `new` on each line

`[options]`

- `-i` — directly modify the file(s)

**Notes**:

- Line-oriented processing: `\n` is treated as a separator between lines and is often removed from the line being processed.
- Byte-oriented processing: `\n` remains an actual byte in the input.

### find

Searches for files and directories based on conditions such as name, size, type, owner, or permissions.

Syntax:

```bash
find [path] [options]
```

`[options]`

- `-name "[name]"` — search by exact name:
  - `[name]`: `*.txt` — search by file name extension.
  - `[name]`: `.*` — search hidden files.
  - `[name]`: `*abc*` - search file name containing "abc".
- `-iname "[name]"` — search by name, only accept different in upper of lower case.
- `-type [type]` — search by file type:
  - `[type]`: `f` — regular files
  - `[type]`: `d` — directories
  - `[type]`: `l` — symbolic links
- `-size [size]` — search by size:
  - `[size]`: `100c` — exact 100 bytes
  - `[size]`: `+10k` — more than 1KB
  - `[size]`: `-1M` — less than 1MB
  - `[size]`: `1G` — exact 1GB
- `-user [user_name]` - search by user name.
- `-group [group_name]` - search by group name.
- `-perm [permission_number]` - search by permission. (`[permission_number]` contains 3 digits and each digit defines the permission - read, write, execute - of user, group and others)
- `-exec [excecuted_command]` - execute `[excecuted_command]` on each result.

`[excecuted_command]`

```bash
[command] \;
```

- `{}` — represent each result.
- `\;` — end the command.

Example:

```bash
find -size 1033c -exec cat {} \;
```

### grep

Searches for lines that match a given text patterns in files or command output.

Syntax:

```bash
grep [options] "[pattern]" [files...]
```

`[options]`

- `-i` — accept different in upper of lower case.
- `-n` — show line numbers in the origin file.
- `-v` — show lines that do not match.
- `-r` — search all files under given directory (replace `[files...]` by `[path]`).
- `-l` — only display filename of files that contain the pattern.
- `-c` — count the matching lines.
- `-w` — only match the whole word.

### diff

Compare the contents of two files line by line and show their differences.

Syntax:

```bash
diff [options] [file] [file]
```

`[options]`

- `-u` — unified format, easier for humans to read, and commonly used in patches
- `-q` — Only reports whether the files differ.
- `-s` — Reports when the files are identical.

### sort

Sorts lines of text alphabetically, numerically, or according to other criteria. Note that `sort` does not change the input files.

Syntax:

```bash
sort [options] [files...]
```

`[options]`

- `-r` — sort in reverse order.
- `-n` — numeric sort.
- `-u` — removes duplicate lines while sorting.
- `-k[n]` —  sort by a field, the n-th column.
- `-o [output_file]` — store output on `[output_file]` rather than print on Terminal.

### uniq

Removes or reports consecutive duplicate lines. Since `uniq` only compares adjacent (consecutive) lines, it is often used with `sort`.

Syntax:

```bash
uniq [options] [input_file] [output_file]
```

`input_file` — if no file provided, `uniq` reads from `stdin`.

`output_file` — if no file provided, `stdout` print on Terminal by default.

`[options]`

- `-c` — count consecutive duplicates.
- `-d` — show duplicated lines only.
- `-u` — show unique lines only.
- `-i` — accept case differences.

### strings

Extracts printable text strings from binary files or non-text file, which may contain readable texts.

Syntax:

```bash
strings [options] [files...]
```

`[options]`

- `-n [n]` — only display strings with at least `[n]` printable characters, default is 4.
- `-a` — scan the entire file, mostly default on many Linux system.

### tr

Replace, delete, or squeeze characters from text.

Syntax:

```bash
tr [options] '[SET1]' '[SET2]'
```

`[SET1]` — characters to be searched for.

`[SET2]` — characters to be replaced.

`[options]`

- `-d` — delete characters in given set, just need `[SET 1]`.
- `-s` — squeeze repeated characters in given set, just need `[SET 1]`.
- `-c` — take the complement of `[SET1]`.

**Important**: `tr` reads from `stdin` and writes to `stdout`, it does not support reading files.

Example: Replace one character

```bash
echo "banana" | tr 'a' 'x'
```

```text
bxnxnx
```

Example: Replace multiple characters

```bash
echo "hello world" | tr 'a-z' 'A-Z'
```

```text
HELLO WORLD
```



---

## Binary Files & Compression

### file

Determine the type of a file by examining its contents, not its filename or extension. This command mostly rely on magic number or the signatures of the file content to examine the file type.

Syntax:

```bash
file [options] [files...]
```

### base64

**Base64** is a way to represent binary data as printable text, because many protocols and file formats expect printable text instead of arbitrary bytes. It is much more compact than writing the binary as a string of 0s and 1s, although it is still larger than the original binary data.

In Linux, `base64` is a command-line utility used to encode binary (or text) data into Base64 and decode Base64 back to its original form.

Syntax:

```bash
base64 [options] [files...]
```

`[options]`

- `-d` or `--decode` — decode Base64, the default is encoding.

### xxd

**Hexdump** is a way to display the raw bytes of a binary file in hexadecimal (base 16) so human can inspect binary data.

Example:

```text
00000000: 1f8b 0808 a6f0 3b6a 0203 6461 7461 322e  ......;j..data2.
00000010: 6269 6e00 0144 02bb fd42 5a68 3931 4159  bin..D...BZh91AY
00000020: 2653 5904 ab91 e100 001c 7fff fffb bebf  &SY.............
...
```
**Unprintable characters** are presented by `.`.

**Magic number** (file signature) is a sequence of bytes at the beginning of the hexdump, it helps to examine the file type. Sometimes it can be sequence of characters instead of numbers. Plain text files usually don't have a magic number, while the others have. (Eg: `.png`, `.gz`,...)

In Linux, `xxd` is a command that creates a hexdump of a binary file. The hexdump is stored in a plain text file.

Syntax:

```bash
xxd [options] [files...]
```

`[options]`

- `-r` — recontruct original binary file of a hexdump.



---

## Shell & Scripting

### Terminal, Shell, and Bash

**Terminal** is an application that provides an interface for interacting with a shell.

**Shell** is a program that accepts your commands, interprets them, and runs them on the operating system kernel. Many shells, including Bash, provide their own scripting language, allowing you to automate commands and write scripts.

**Bash** stands for **Bourne Again SHell**, which is a shell that was originally developed for Unix-like operating systems. Today, it can also run on Windows through environments such as WSL, Git Bash, or Cygwin.

```text
Command → Terminal → Shell (Bash,...) → Linux Kernel → Hardware
```

### Commands

**Commands** are instructions given by the user to the shell to perform specific actions. When a command refers to an external program, the shell finds and executes that program.

Example:

```bash
cat file1.txt file2.txt file3.txt
```

The shell interprets the command as:

```text
cat file1.txt file2.txt file3.txt
│   │         │         │
│   └─────────┴─────────┴── arguments
└────────────────────────── program/command
```

The shell searches for the `cat` program in the directories listed in `$PATH` and executes it. The files `file1.txt`, `file2.txt`, and `file3.txt` are passed to `cat` as arguments.

If the command does not contain a path, the shell searches for the program in `$PATH`:

```bash
cat file.txt
```

We can also specify the program's path explicitly:

```bash
./program
```

Here, `./` specifies that `program` is located in the current directory.

Or:

```bash
/usr/bin/cat file.txt
```

Here, `/usr/bin/cat` is an absolute path to the program.

Therefore, when executing a program, we can use a relative path, an absolute path, or no path at all if the program can be found through `$PATH`.

### Streams

Every Linux program has three standard streams:

- `0` — `stdin`  : input (keyboard by default)
- `1` — `stdout` : normal output (terminal by default)
- `2` — `stderr` : error messages (terminal by default)

Some commands are designed to read input(s) (Eg: `[file]`,`[files...]`), if input(s) is provided, `stdin` take the content from the input(s), else `stdin` take from keyboard by default.

### Shell Redirection

We can rediect these streams as we want. For example, instead of print normal output on the terminal, we can redirect it to a file.

- `<`  — redirect content of a file to `stdin`.
- `>`  — redirect `stdout` to a file, overwriting its previous contents. If the file doesn't exist, it is created.
- `>>` — redirect `stdout` to a file, append `stdout` to the end of the file.
- `2>` — redirect `stderr` to a file, overwriting its previous contents.
- `/dev/null` — is a special file that anything written to it disappears forever, `stderr` are often redirected to here to filter error messages.

Example:

```bash
find / > output.txt 2> errors.txt
```

```text
stdout → output.txt
stderr → errors.txt
```

- `|` — this is called pipe and it sends the `stdout` of `[command_1]` (not `stderr`) into the `stdin` of `[command_2]`.

```bash
[command_1] | [command_2]
```

### Scripting

**Script** is a text file containing a sequence of commands that are executed automatically by an interpreter (such as **Bash**).

Instead of typing commands line by line, we can save it into a file and execute that file. This is useful when we have to solve repetitive tasks and we can re-use that script many times.

### Scripting Basics

**Script structure**

  - `#!/bin/bash` tells Linux to execute the script using Bash.
  - Everything below is ordinary Bash commands.

```bash
#!/bin/bash

echo "Hello World"
```

**Run script**

  - `[file]` — have `.sh` extension.

```bash
bash [file]
```

**Comments**

```bash
# This is a comment

echo "Hello"
```

**Variables**

```bash
name="Alice"
echo "$name"
```

**Get command output**

```bash
current=$(pwd)
echo "$current"
```

**Script Arguments**

  - `$1` — first argument
  - `$2` — second argument
  - `$@` — all arguments
  - `$#` — number of arguments

**`$?`** — stores the exit status of the previous command.

  - `0` → success
  - non-zero → failure

  - `true` is a command with exit status is `0` → use for infinity loop or always executed `if` statement.
  - `false` is a command with exit status is non-zero.

**Wildcards**

Wildcards (also called globs) are special characters that let the shell match multiple filenames or patterns.

They are expanded by the shell before the command runs.

  - `*` — Match zero or more characters. (Eg: `*.txt` refers to all files end with `.txt` extention; `*abc*` refers to all file names contain `abc`;...)
  - `?` — Match exactly one character. (Eg: `?.txt` refers to `a.txt`, `b.txt` but not `ab.txt`;...)
  - `[]` — Match one character from a set. (Eg: `[bc]at.txt` refers to `bat.txt` and `cat.txt`;...)

**Double Quotes** — Preserve spaces while expanding variables. → Whenever using a variable, put it in double quotes.

**Single Quotes** — Treat everything as a string. → Whenever writing a string, put it in single quotes.

### Control Flow

1. `if`-`else` statements — checks whether the command succeeds.

```bash
if grep "password" data.txt; then
    echo "Found"
else
    echo "Not found"
fi
```

2. `case` statements — checks different conditions.

```bash
type=$(file data)

case "$type" in
    *gzip*)
        echo "gzip"
        ;;
    *bzip2*)
        echo "bzip2"
        ;;
    *tar*)
        echo "tar"
        ;;
    *)
        echo "Unknown"
        ;;
esac
```

3. `for` loop

```bash
for file in *.txt
do
    echo "$file"
done
```

4. `while` loop

```bash
count=1

while [ $count -le 5 ]
do
    echo "$count"
    count=$((count + 1))
done
```

5. Functions

```bash
hello() {
    echo "Hello!"
}

hello
```

### Logical Expressions

In Bash, conditional expressions are usually written inside `[[ ... ]]` and has exit status.
(`[[ ... ]]` is Bash-specific and is generally preferred over the older POSIX `[ ... ]`.)

Example:

```bash
if [[ "$type" == *gzip* ]]; then
    echo "gzip"
fi
```

Logical Operators:

- `==` and `!=`: string comparision or pattern matching.
- `[[ ! ... ]]`: NOT
- `[[ ... && ... ]]`: AND
- `[[ ... || ... ]]`: OR

### Arithmetic Expressions

Arithmetic expressions are written inside `(( ... ))` and has exit status.

Inside `(( ... ))`:

- Variables do **not** need `$`.
- Numbers do **not** need quotes.

Example:

```bash
count=5

if (( count >= 5 )); then
    (( count++ ))
fi
```

Operators:

- Comparision: `>`,`<`,`>=`,`<=`,`==`,`!=`
- Arithmetic: `+`,`-`,`*`,`/`,`%`
- Increment and Decrement:`++`,`--`
- Assignment: `+=`,`-=`,`*=`,`/=`,`%=`

### Input & Output

1. `echo`: Prints text or variable values to `stdout`.

```bash
echo [options] [string...]
```

  - `[string...]`
    - The text or variables to print.  
    - To print special characters, put them in quotes.
    - `echo` ignores `stdin`, it only reads `[string...]`.

  - `[options]`
    - `-n` — do not print the trailing newline.
    - `-e` — interpret escape sequences. (`\n`,`\t`,`\\`,`\"`)

2. `read`: Reads input from `stdin` and stores in variable.

```bash
read [variable]
```



---

## System & Networking

### Must-know Directories

  - `/` — The root directory. Everything in Linux starts here.
  - `/home` — Users' home directories. `~` represent current user home directory. The same as `/home/[current_user]`.
  - `/root` — Home directory of the root user. Normal users usually cannot access it. This is not the same as `/`.
  - `/tmp` — Temporary files. Programs often store temporary data here. Many Linux systems automatically clean `/tmp` up.
  - `/etc` — System configuration. Contains configuration files. Cybersecurity people spend a lot of time here.
  - `/bin` — Essential user commands.
  - `/usr` — User programs and libraries. Contains most installed software.
  - `/var` — Variable data. Things that change while the system runs. System logs are stored in `/var/log`.
  - `/dev` — Device files. Linux represents hardware as files.
  - `/proc` — Contains information about the running kernel and processes.
  - `/mnt` — Mount point. Used for mounted filesystems.
  - `/media` — Removable devices.
  - `/opt` — Optional software. Programs installed manually often go here.
  - `/boot` — Files needed to boot Linux. Contains the kernel and bootloader files. Normally you don't modify these.
  - `/lib` — Shared libraries. Similar to DLL files on Windows. Programs depend on these libraries to run.
  - `/srv` — Data served by services.

### Must-know Files

  - `.profile` — startup file commonly read when a **login shell** starts, set up the environment for that user's login shell.
  - `.bashrc` — startup file commonly read when an **interactive Bash shell** starts, set up the interactive Bash environment.
  - `.bash_logout` — a file commonly executed when an **interactive login Bash shell** exits.

### whoami

Show current user

Syntax:

```bash
whoami
```

### uname

Show system information.

Syntax:

```bash
uname [options]
```

`[options]`

- `-a` — show extended information.
  - `Linux`: The system is running the Linux kernel.
  - `DESKTOP-4PGST7Q`: The hostname (the computer’s name).
  - `6.6.87.2-microsoft-standard-WSL2`: The kernel version installed on the machine.
  - `x86_64`: The hardware platform (also 64-bit).
  - `GNU/Linux`: The operating system type (Linux kernel + GNU tools)

Example:

```text
Linux DESKTOP-4PGST7Q 6.6.87.2-microsoft-standard-WSL2 #1 SMP PREEMPT_DYNAMIC Thu Jun  5 18:30:46 UTC 2025 x86_64 GNU/Linux
```

### df

Check disk and storage info.

Syntax:

```bash
df [options]
```

`[options]`

- `-h` — human readable for disk sizes.

### Localhost

**Localhost** is a hostname that represents the local host (the machine or environment on which the program is running). It allows a computer to make a connection to itself, which is known as loopback.

The standard loopback addresses for localhost are:

- IPv4 — `127.0.0.1`
- IPv6 — `::1`

On Linux, we can use the hostname `localhost` to refer to the loopback IP address. Depending on the system's configuration, `localhost` can resolve to `127.0.0.1`, `::1`, or both.

Commands to look up the localhost IP address and what `localhost` resolves to:

```bash
cat /etc/hosts
```

### Ports

A **port** is a numbered endpoint used by network services to receive network connections. A service can listen on a specific port, and clients can connect to that port to communicate with the service. Firewall rules can control which connections are allowed.

Linux traditionally restricts normal users from binding to ports below `1024`:

- `0–1023`       → privileged ports
- `1024–65535`   → generally available to normal users

This restriction helps prevent unprivileged users from binding to ports traditionally associated with important system services.

Also a port can fail for other reasons. For example:

- `Permission denied`       → the current user does not have permission to bind to the port.
- `Address already in use`  → another process is already using the address and port combination.

Therefore, when creating a listener, the port should be both:

- Usable by the current user.
- Not already being used by another process.

### SSL/TLS

**SSL/TLS** are protocols used to securely communicate over a network.

- **SSL** (Secure Sockets Layer) → older protocol, now obsolete/insecure.
- **TLS** (Transport Layer Security) → the modern protocol used today, the successor to **SSL**.

**TLS** provides:

- Encryption: Data is encrypted so someone intercepting the traffic cannot easily read it.
- Authentication: TLS can help verify that your browser is actually communicating with the intended server by using TLS certificates.
- Integrity: TLS helps detect whether data was modified while traveling across the network.

**TLS** usually runs on top of TCP and is commonly associated with the Presentation and Session layers in the OSI model. After 3-way handshake, TCP connection is established on Transport layer. Then, TLS relies on the TCP connection to establish a TLS connection, and TLS also perform a "handshake" first.

**TLS handshake** is performed to exchange required information that allows both sides to communicate sercurely. The process is:

1. `Client request`:
  - Supported TLS versions
  - Supported cipher suites
  - Client ECDHE public key

2. `Server response`:
  - Selected TLS version
  - Selected cipher suite
  - Sever ECDHE public key

3. Private calculation of Server and Client to get symmetric traffic key set:
  - Server: Server ECDHE private key + Client ECDHE public key ── ECDHE algorithm + Key derivation ──► Traffic key set
  - Client: Server ECDHE public key + Client ECDHE private key ── ECDHE algorithm + Key derivation ──► The same traffic key set
  - The traffic key set is not sent across the network, Client and Server calculate the key set by themself.
  - The key set have four main keys, each of them is used as a symmetric key for encryption of messages. After this step, hanshake traffic keys are used to encrypt **TLS handshake** messages.
    - Server handshake key (Derived at this step)
    - Client handshake key (Derived at this step)
    - Server application key (Derived during **TLS handshake**)
    - Client application key (Derived during **TLS handshake**)
  - An attacker who only observes the two ECDHE public keys cannot feasibly derive the key set without breaking the underlying cryptographic assumption.

4. Server send `Certificate`:
  - `Certificate` includes:
    - Domain: `example.com`
    - Certificate public key
    - CA's digital signature
  - The `Certificate` binds a domain name to a public key, called certificate public key. A trusted third party, called a Certificate Authority (CA), digitally signs the `Certificate` to attest to this binding. The Client verifies the CA's signature to make sure the binding. Now the Client just need to confirm does the communicating server posses the certificate private key or not, because IP address is not enough for authentication.

5. Server send `CertificateVerify`:
  - `CertificateVerify` is a digital signature created by using certificate private key to sign data derived from the TLS handshake transcript.
  - The Client uses certificate public key to verify the `CertificateVerify` against data derived from its TLS handshake transcript. Therefore, the Client can verify that the communicating server posses the certificate private key or not.
  - The TLS handshake transcript contains connection-specific values that depend on different TLS connections, so attacker cannot use `CertificateVerify` of a TLS connection for another TLS connection. This prevent the attackers to copy the `Certificate` or `CertificateVerify` to mimic the Server.

**Notes**: There are three relevant key pairs in this simplified explanation:

  - Client's ephemeral ECDHE key pair → key exchange
  - Server's ephemeral ECDHE key pair → key exchange
  - Server's certificate key pair → **authentication**

6. The Server and the Client respectively send `Finished message` to verify the integrity of the **TLS handshake**,...

After the **TLS handshake**, the Server and Client use their derived application traffic keys to protect application data. TLS uses AEAD algorithms to provide both **encryption** and **integrity**/**authentication** of each TLS record.

### nmap

Scans a target and reports reachable ports and their services.

Syntax:

```bash
nmap [options] [IP_address/domain]
```

Example output:

```text
PORT     STATE     SERVICE
22/tcp   open      ssh
80/tcp   filtered  http
```

- `open` — `nmap` determined that a service is listening and reachable on that port
- `closed` — `nmap` reached the host, but no service is listening on that port
- `filtered` — `nmap` cannot determine the state because filtering interferes on that port

`[options]`

- `-p [ports...]` — scan specific port(s)
- `-p-` — scan all TCP ports (1–65535)
- `-sV` — detect service/version

### nc

Creates a TCP (default) or UDP connection and sends/receives data.

Syntax:

```bash
nc [options] [IP_address/domain] [port]
```

`[options]`

- `-l` — listen for incoming connections
- `-u` — create UDP connection

**Notes**: Unlike `ssh` or `openssl`, which requires the connected port to support many protocols, `nc` just requires connected port to establish a connection, and exchange data without any required protocols. Therefore, `nc` is useful when investigating an unknown service.

### openssl

A command-line toolkit and software library that implements cryptographic protocols and algorithms, especially SSL/TLS.

Syntax:

```bash
openssl [subcommand] [options]
```

**`[subcommand]`**

1. `s_client` — Connect to a SSL/TLS server

`[options]`

- `-connect [IP_address/domain]:[port]` — specify the server and port to connect to.
- `-state` — show detailed TLS handshake information
- `-showcerts` — show certificates
- `-servername [SNI]` — specify the server name for Server Name Indication (SNI)
- `-quiet` — changes `s_client` interactive behavior and output by suppressing most connection information and disabling interactive command processing, allowing user input to be sent as application data over the TLS connection.
- `-nocommands` — disables interactive command processing, allowing user input to be sent as application data over the TLS connection.

**Notes**: When using `openssl s_client` to connect to a TLS server over TCP, a TCP connection is established first. Then, the TLS handshake occurs over that TCP connection.

2. `s_server` — Create a simple TLS server

3. `version` — Check the OpenSSL version and build information

### ssh

Sercurely connects to a remote server by applying several protocols, including password authentication and public-key authentication.

- **Password authentication:** The user proves their identity by providing a password.
- **Public-key authentication:** The user proves possession of a private key corresponding to a public key stored by the server.

Syntax:

```bash
ssh [options] [host_name]
```

`[options]`

- `-p [port_number]` — specify the port number. The default port is 22.
- `-l [user_name]` — specify the login username. It is equivalent to writing the username before the host name using the `[user_name]@[host_name]` syntax.
- `-i [key_file]` — specifies the private key used for authentication.

**Notes**: `ssh` does not send the private key to the server. The private key is used locally to produce cryptographic proof of possession. The server verifies that proof using the corresponding public key.

**Notes**: If no command is provided, after the SSH authentication process, the remote machine normally starts an **interactive login shell**, allowing us to enter commands through the remote terminal (`ssh user@host` → **interactive login shell** → you type commands). We can also provide a command to SSH. In this case, SSH executes the command on the remote machine without starting an **interactive login shell** (ssh user@host "command" → execute command remotely → return output → exit).

Syntax:

```bash
ssh [options] [host_name] "[commands]"
```

### scp

`scp` stands for **Secure Copy Protocol**. It is a command-line tool for securely copying files between computers over an SSH connection.

Syntax:

```bash
scp [options] [source_path] [destination_path]
```

`[options]`

- `-P [port]` — specifies the SSH port to connect to.

`[source_path]`: is the path of the file or directory to copy.

- Remote server: `server:[path]`
- Normal machine: `[path]`

`[destination_path]`: is where the copied file or directory should be stored.

- Remote server: `server:[path]`
- Normal machine: `[path]`

**Notes**: `scp` uses SSH connection as its underlying transport for securely transferring files, so we need to provide `scp` with connection information similar to what we provide when using `ssh`.


