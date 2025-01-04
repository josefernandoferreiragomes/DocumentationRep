# Linux Bash Commands Cheat Sheet

### Once inside the Linux environment, you can use Linux commands just like you would in a regular Linux terminal. For example:
- `cd /mnt/c`  # Access Windows files from /mnt directory

## **1. File and Directory Management**
- `ls` – List directory contents.
  ```bash
  ls -l       # Long format
  ls -a       # Include hidden files
  ```
- `cd` – Change directory.
  ```bash
  cd /path/to/directory
  ```
- `pwd` – Print the current working directory.
- `mkdir` – Create a new directory.
  ```bash
  mkdir myfolder
  ```
- `rmdir` – Remove an empty directory.
- `rm` – Remove files or directories.
  ```bash
  rm file.txt             # Delete file
  rm -r myfolder          # Delete folder and its contents
  ```
- `cp` – Copy files or directories.
  ```bash
  cp file.txt /path/to/destination
  ```
- `mv` – Move or rename files or directories.
  ```bash
  mv oldname.txt newname.txt
  ```
- `find` – Search for files and directories.
  ```bash
  find /path -name "file.txt"
  ```

## **2. File Viewing and Editing**
- `cat` – View file contents.
  ```bash
  cat file.txt
  ```
- `less` – View large files one screen at a time.
  ```bash
  less file.txt
  ```
- `head` – View the first few lines of a file.
  ```bash
  head -n 10 file.txt
  ```
- `tail` – View the last few lines of a file.
  ```bash
  tail -n 10 file.txt
  ```
- `nano` / `vim` – Command-line text editors.

## **3. File Permissions and Ownership**
- `chmod` – Change file permissions.
  ```bash
  chmod 755 file.txt
  ```
- `chown` – Change file owner.
  ```bash
  chown user:group file.txt
  ```
- `ls -l` – View file permissions.

## **4. Process Management**
- `ps` – List running processes.
  ```bash
  ps aux       # Detailed list
  ```
- `top` / `htop` – Interactive process viewer.
- `kill` – Kill a process by its PID.
  ```bash
  kill 1234
  ```
- `killall` – Kill processes by name.
  ```bash
  killall firefox
  ```

## **5. Networking**
- `ping` – Check network connectivity.
  ```bash
  ping google.com
  ```
- `curl` – Fetch content from a URL.
  ```bash
  curl http://example.com
  ```
- `wget` – Download files from the internet.
  ```bash
  wget http://example.com/file.zip
  ```
- `ifconfig` / `ip` – Show or configure network interfaces.
  ```bash
  ip a
  ```

## **6. Disk and System Information**
- `df` – Show disk space usage.
  ```bash
  df -h
  ```
- `du` – Show directory size.
  ```bash
  du -sh /path/to/directory
  ```
- `free` – Display memory usage.
  ```bash
  free -h
  ```
- `uname` – Show system information.
  ```bash
  uname -a
  ```
- `uptime` – Show how long the system has been running.
- `whoami` – Display the current user.
- `hostname` – Show or set the hostname.

## **7. Searching and Text Processing**
- `grep` – Search text in files.
  ```bash
  grep "pattern" file.txt
  ```
- `awk` – Process text data.
  ```bash
  awk '{print $1}' file.txt
  ```
- `sed` – Stream editor for modifying text.
  ```bash
  sed 's/old/new/g' file.txt
  ```

## **8. Package Management (Ubuntu/Debian-based Systems)**
- `apt` – Manage packages.
  ```bash
  sudo apt update           # Update package lists
  sudo apt install package  # Install a package
  sudo apt remove package   # Remove a package
  ```

## **9. Archiving and Compression**
- `tar` – Archive files.
  ```bash
  tar -cvf archive.tar file1 file2
  ```
- `zip` / `unzip` – Compress or extract files.
  ```bash
  zip archive.zip file.txt
  unzip archive.zip
  ```
- `gzip` / `gunzip` – Compress or decompress files.
  ```bash
  gzip file.txt
  gunzip file.txt.gz
  ```

## **10. Miscellaneous**
- `history` – Show command history.
- `alias` – Create shortcuts for commands.
  ```bash
  alias ll='ls -la'
  ```
- `echo` – Print messages or variables.
  ```bash
  echo "Hello, World!"
  ```
- `env` – Show environment variables.
- `clear` – Clear the terminal screen.

# Windows CMD Commands Cheat Sheet

## **1. File and Directory Management**
- `dir` – List directory contents.
  ```cmd
  dir          # Basic list
  dir /a       # Include hidden files
  ```
- `cd` – Change directory.
  ```cmd
  cd \path\to\directory
  cd ..        # Move up one directory
  ```
- `echo %cd%` – Print the current working directory.
- `mkdir` – Create a new directory.
  ```cmd
  mkdir myfolder
  ```
- `rmdir` – Remove an empty directory.
  ```cmd
  rmdir myfolder
  ```
- `del` – Remove files or directories.
  ```cmd
  del file.txt            # Delete file
  del /s /q myfolder      # Delete folder and its contents
  ```
- `copy` – Copy files or directories.
  ```cmd
  copy file.txt \path\to\destination
  ```
- `move` – Move or rename files or directories.
  ```cmd
  move oldname.txt newname.txt
  ```
- `forfiles` – Search for files matching criteria.
  ```cmd
  forfiles /p \path /m "*.txt"
  ```

## **2. File Viewing and Editing**
- `type` – View file contents.
  ```cmd
  type file.txt
  ```
- `more` – View large files one screen at a time.
  ```cmd
  type file.txt | more
  ```
- `findstr` – Search text in files.
  ```cmd
  findstr "pattern" file.txt
  ```

## **3. File Permissions and Ownership**
- `icacls` – Change file permissions.
  ```cmd
  icacls file.txt /grant User:F
  ```
- `takeown` – Take ownership of a file.
  ```cmd
  takeown /f file.txt
  ```

## **4. Process Management**
- `tasklist` – List running processes.
- `taskkill` – Kill a process by its PID or name.
  ```cmd
  taskkill /PID 1234
  taskkill /IM notepad.exe
  ```

## **5. Networking**
- `ping` – Check network connectivity.
  ```cmd
  ping google.com
  ```
- `curl` – Fetch content from a URL (available in modern Windows).
  ```cmd
  curl http://example.com
  ```
- `netstat` – Show network connections.
  ```cmd
  netstat -an
  ```
- `ipconfig` – Show network configuration.
  ```cmd
  ipconfig
  ipconfig /all         # Detailed info
  ipconfig /flushdns    # Clear DNS cache
  ```

## **6. Disk and System Information**
- `dir` – Show disk space usage of a directory.
- `fsutil` – Manage disk usage.
  ```cmd
  fsutil volume diskfree C:
  ```
- `systeminfo` – Display detailed system information.
- `hostname` – Show the hostname.
  ```cmd
  hostname
  ```

## **7. Searching and Text Processing**
- `findstr` – Search for text in files.
  ```cmd
  findstr "pattern" file.txt
  ```
- `find` – Search for a specific string in a file.
  ```cmd
  find "pattern" file.txt
  ```

## **8. Package Management**
(Windows does not have a native package manager like `apt`, but you can use tools like [Chocolatey](https://chocolatey.org/) or [winget].)
- `winget` – Manage packages (Windows Package Manager).
  ```cmd
  winget search package   # Search for a package
  winget install package  # Install a package
  winget uninstall package
  ```

## **9. Archiving and Compression**
- `compact` – Compress files or folders.
  ```cmd
  compact /c file.txt
  ```
- `expand` – Decompress files.
  ```cmd
  expand file.txt.gz
  ```
- Use external tools like `tar` or `7zip` for advanced compression.

## **10. Miscellaneous**
- `doskey` – Create command aliases.
  ```cmd
  doskey ll=dir /a
  ```
- `echo` – Print messages or variables.
  ```cmd
  echo Hello, World!
  ```
- `set` – Show or set environment variables.
  ```cmd
  set PATH
  set NEW_VAR=value
  ```
- `cls` – Clear the terminal screen.
- `history` – View command history (use the up arrow key to browse).

