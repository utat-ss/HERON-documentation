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
3. Download Tunnelblick (it’s free!) or some other OpenVPN client management software
    - Tunnelblick download site: https://tunnelblick.net 
4. If you download tunnelblick, make sure to upload the er.ovpn file by either clicking it or adding it to the Tunnelblick Configurations
5. Click `connect` to connect to the VPN
6. You will be prompted to enter a username and password. Enter the account given to you by the IT member
7. The time taken to connect shouldn’t be more than a minute. You will be informed if the connection is successful
8. Congrats you should be on!

### Windows
1. Create a directory to place your vpn configuration in (such as Desktop/openvpn)
2. Place the ca.cert, client.crt, er.ovpn and client.key files in that directory
3. Download: https://openvpn.net/client-connect-vpn-for-windows/
4. Click the er.ovpn
5. You will be prompted to enter a username and password. Enter the account given to you by an IT member
6. You should be connected within a minute or so

[Resource for OpenVPN setup instructions](https://help.ubnt.com/hc/en-us/articles/115015971688-EdgeRouter-OpenVPN-Server)
