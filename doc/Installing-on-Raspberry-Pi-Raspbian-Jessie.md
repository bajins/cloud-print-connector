These instructions assume you already have a working CUPS server running on your Raspberry Pi.
If not this howto may help you. https://samhobbs.co.uk/2014/07/raspberry-pi-print-scanner-server

First, [[build from source]].

Create an unprivileged gcp system user:
```
sudo useradd -s /usr/sbin/nologin -r -M cloud-print-connector
```
Create /opt/cloud-print-connector:
```
sudo mkdir /opt/cloud-print-connector
```
Move the binaries to /opt/cloud-print-connector:
```
sudo mv ~/go/bin/gcp-cups-connector /opt/cloud-print-connector
```
```
sudo mv ~/go/bin/gcp-connector-util /opt/cloud-print-connector
```
Make sure the binaries are executable:
```
sudo chmod 755 /opt/cloud-print-connector/gcp-cups-connector
```
```
sudo chmod 755 /opt/cloud-print-connector/gcp-connector-util
```
Change the owner of the binaries to gcp:
```
sudo chown cloud-print-connector:cloud-print-connector /opt/cloud-print-connector/gcp-cups-connector
```
```
sudo chown cloud-print-connector:cloud-print-connector /opt/cloud-print-connector/gcp-connector-util
```
Delete unneeded binaries:
```
rm -f ~/go/bin/gcp*
```
Make a gcp config:
```
sudo /opt/cloud-print-connector/gcp-connector-util init
```
Example configuration:
```
"Local printing" means that clients print directly to the connector via local subnet,
and that an Internet connection is neither necessary nor used.
Enable local printing?
y

"Cloud printing" means that clients can print from anywhere on the Internet,
and that printers must be explicitly shared with users.
Enable cloud printing?
y

Retain the user OAuth token to enable automatic sharing?
y

User or group email address to share with:
xxx@gmail.com

Proxy name for this GCP CUPS Connector:
MyPrinterName

Visit https://www.google.com/device, and enter this code. I'll wait for you.
XXXX-XXXX
Acquired OAuth credentials for robot account

The config file /home/pi/gcp-cups-connector.config.json is ready to rock.
Keep it somewhere safe, as it contains an OAuth refresh token.
```
Move the gcp config to /opt/cloud-print-connector/:
```
sudo mv ~/gcp-cups-connector.config.json /opt/cloud-print-connector/
```
Change the file permissions of the gcp config:
```
sudo chmod 660 /opt/cloud-print-connector/gcp-cups-connector.config.json
```
Change the owner of the gcp config to gcp:
```
sudo chown cloud-print-connector:cloud-print-connector /opt/cloud-print-connector/gcp-cups-connector.config.json
```

If you want the connector to start automatically on boot, see [[(Linux) Run Connector Automatically on Boot]], then
reboot your Raspberry Pi and enjoy!!!
