## Basic Linux Commands
```sh
ls                # List directory contents
ls -lah           # List with details, including hidden files
ls | wc -l        # Count the number of files and directories in the current directory usefull with the *
pwd               # Print working directory
cd <directory>    # Change directory
mkdir <name>      # Create a new directory
rm <file>         # Remove a file
rm -r <dir>       # Remove a directory and its contents
mv <source> <dest>  # Move or rename files
cp <source> <dest>  # Copy files or directories
whoami            # Show current logged-in user
which <command>   # Show the path of a command
touch <file>      # Create an empty file or update timestamp
cat <file>        # Display the contents of a file
top <file>        # Display the first 10 lines of a file
xdg-open <file>   # Attempts to open a program or file in the default way for that file
nano <file> # Opens the nano text editor in the terminal
w # Tells you who is currently logged in
```

## Piping Outputs and Redirecting to Files
```sh
command > file     # Redirect standard output to a file (overwrite)
command >> file    # Append standard output to a file
command &> file    # Redirect both standard output and error to a file
command | another  # Pipe output of one command to another
```

## Size of Directories
```sh
du -h <directory>  # Show disk usage in human-readable format
du -s <directory>  # Show total disk usage of a directory
free -h            # Show available memory
```


## File Permissions
```sh
chmod 755 <file>  # Set read/write/execute for owner, read/execute for others
chmod +x <file>   # Make file executable
chown user:group <file>  # Change file owner and group
su <user>                # Switch to another user
```

## Process Management
```sh
ps aux            # Show running processes
top               # Interactive process viewer
htop              # Interactive process viewer with more features
kill <PID>        # Terminate a process by PID
kill -9 <PID>     # Force kill a process
pkill <name>      # Kill processes by name
```

## Networking
```sh
ping <host>       # Check connectivity to a host
ifconfig          # Show network interfaces (deprecated, use ip instead)
ip addr show      # Display IP addresses
netstat -tulnp    # Show open ports
```

## Running Screens (Screen)
```sh
screen            # Start a new screen session
screen -S <name>  # Start a screen session with a name
screen -ls        # List all screen sessions
screen -r <name>  # Reattach to a running session
screen -d -r <name>  # Detach and reattach to a session
exit              # Exit a screen session
Ctrl+A then D     # Detach from the current screen session
```

## SSH Basics
```sh
ssh user@host         # Connect to a remote server
ssh -i key.pem user@host  # Connect using an SSH key
scp file user@host:/path  # Copy a file to a remote server
scp user@host:/path file  # Copy a file from a remote server
rsync -avz file user@host:/path  # Sync files to a remote server
ssh -J user@gateway user@destination  # SSH jump through an intermediate server
```

## SSH Config File (~/.ssh/config)
```sh
Host myserver
    HostName example.com
    User myusername
    IdentityFile ~/.ssh/id_rsa
    Port 22
```
Usage:
```sh
ssh myserver      # Connect without specifying full details
```

## Graphic Applications Over SSH
```sh
ssh -X user@host  # Enable X11 forwarding for graphical applications
export DISPLAY=:0 # Set display environment variable (if needed)
```



## Package Management (Debian Based IE Ubuntu)
```sh
apt update                     # Update package lists
apt upgrade                    # Upgrade installed packages
apt install <package>          # Install a package
apt remove <package>           # Remove a package
```


## Cronjob
```sh
crontab -e  # Open the crontab file in the default editor
crontab -l  # List current cron jobs
crontab -r  # Remove all cron jobs

crontab -e  # Open the crontab file in the default editor
# Add the cron job in the following format:
# * * * * * /path/to/command  > /dev/null 2>&1
# ┬ ┬ ┬ ┬ ┬
# │ │ │ │ │
# │ │ │ │ └─ Day of the week (0 - 6) (Sunday=0)
# │ │ │ └──── Month (1 - 12)
# │ │ └────── Day of the month (1 - 31)
# │ └──────── Hour (0 - 23)
# └────────── Minute (0 - 59)

# the piping to dev null ensures to discard outputs and errors
```

