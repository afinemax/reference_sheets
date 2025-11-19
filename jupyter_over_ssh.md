## Setting Up Jupyter Notebook Over an SSH Connection

You can run a Jupyter Notebook on a remote server and access it from your local machine using SSH tunneling. Follow these steps:

```sh
# 1. Install Jupyter Notebook on the Remote Machine (if not installed)
pip install jupyter

# 2. Start Jupyter Notebook on the Remote Machine
# Run the following command to start Jupyter without opening a browser:
jupyter notebook --no-browser --port=8888
# The --no-browser flag prevents Jupyter from trying to open a browser on the remote machine.
# The --port=8888 specifies the port Jupyter will use (you can choose a different port if needed).

# 3. Set Up an SSH Tunnel from Your Local Machine
# On your local machine, run:
ssh -N -L 8888:localhost:8888 user@remote-server
# Replace 'user' with your remote username.
# Replace 'remote-server' with the remote machine's address.
# The -L 8888:localhost:8888 part forwards traffic from your local port 8888 to the remote port 8888.
# the -N doesnt open a terminal in the host

# 4. Access Jupyter Notebook in Your Local Browser
# Once the SSH tunnel is established, open your browser and go to:
http://localhost:8888
# Jupyter should now be accessible from your local machine.
# Pro tip, set up the host computer with a screen session to host the jupyter session 
```
