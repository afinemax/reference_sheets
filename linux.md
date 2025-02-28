## Basic Linux Commands
```sh
ls                # List directory contents
ls -lah           # List with details, including hidden files
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
```

## File Permissions
```sh
chmod 755 <file>  # Set read/write/execute for owner, read/execute for others
chmod +x <file>   # Make file executable
chown user:group <file>  # Change file owner and group
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



