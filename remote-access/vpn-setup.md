# VPN Setup

The following set of instructions will tell you how to get onto the VPN from your home computer.

Required files (Ask IT members for them):
- cacert.pem (certificate file)
- client.pem (client cert file)
- client.key (client key file)
- er.ovpn (configuration file)

Note that the names may be vaguely different and that the .pem extension may be replaced with .crt

You will be prompted to enter a username and password to login to the VPN. Ask an IT member to create an account for you and for the account's details.

## Steps
### Mac
1. Create a directory to place your vpn configuration in (such as Desktop/openvpn)
2. Place the ca.cert, client.crt, er.ovpn and client.key files in that directory
3. Download [Tunnelblick](https://tunnelblick.net) (it’s free!) or some other OpenVPN client management software
4. If you download tunnelblick, make sure to upload the er.ovpn file by either clicking it or adding it to the Tunnelblick Configurations
5. Click `connect` to connect to the VPN
6. You will be prompted to enter a username and password. Enter the account given to you by the IT member
7. The time taken to connect shouldn’t be more than a minute. You will be informed if the connection is successful
8. Congrats you should be on!

### Windows
1. Create a directory to place your vpn configuration in (such as Desktop/openvpn)
2. Place the ca.cert, client.crt, er.ovpn and client.key files in that directory (need to unzip the folder)
3. Download [OpenVPN for Windows](https://openvpn.net/client-connect-vpn-for-windows/)
4. Import the er.ovpn file from the OpenVPN GUI menu or drag the file into the GUI window
5. You will be prompted to enter a username and password when the file is successfully imported. Enter the account given to you by an IT member
6. You should be connected within a minute or so.


### Linux
1. Create a directory to place your vpn configuration in, then place the four required files mentioned above in that directory.
2. If you are on Ubuntu/Debian Linux (or its variations), open your terminal and run `sudo apt-get install openvpn` to install openVPN for Linux. 
    - OpenVPN also supports Fedora/CentOS/RedHat (install with `yum` instead of `apt`)
3. Once the installation is successful, navigate to the directory where your er.ovpn file is placed in terminal and run `sudo openvpn --config er.ovpn`.
4. Texts will start flying through the terminal and you will be prompted to enter your username and password at some point.
5. Once the connection is established, you should see something like "Initialization Sequence Complete" and you should be good to go. The connection will stay established as long as the terminal windows is open.

[Resource for Linux OpenVPN setup instructions](https://openvpn.net/vpn-server-resources/how-to-connect-to-access-server-from-a-linux-computer/)

[Guideline for OpenVPN setup instructions](https://help.ubnt.com/hc/en-us/articles/115015971688-EdgeRouter-OpenVPN-Server)
