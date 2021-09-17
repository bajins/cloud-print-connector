## Summary

These instructions will result in sharing all printers on your CUPS server with Chrome, on the local subnet (mDNS broadcast domain) only. This is accomplished without creating/maintaining any config file.

If you need to share the printers with Google Cloud Print clients on Android, iOS, or Windows, or if you need to share the printers with clients not on the local subnet, then visit [Configuration](Configuration) after completing these steps.

## Binaries

We currently don't have any binaries available for download. See [issue #265](/google/cloud-print-connector/issues/265).
For now, visit [Build from source](Build-from-source).

## Prepare the platform

Before running the connector, make sure that the necessary client libraries are available: CUPS and Avahi. Also, git and bzr are needed to fetch some Go dependencies.

If your distro is based on Debian (Ubuntu, Raspbian, Mint, others) then this one-liner will get all dependencies:
```
$ sudo apt-get install cups libcups2 libavahi-client3 avahi-daemon libsnmp30 google-cloud-print-connector
```

OpenSUSE 13.2:
```sh
$ sudo zypper install cups cups-libs libavahi-client3 avahi libsnmp30 google-cloud-print-connector
```

CentOS and friends:
```sh
$ sudo yum install cups cups-libs avahi-libs avahi net-snmp-libs google-cloud-print-connector
```
## Run the CUPS Connector

You will also need a running CUPS server. The standard CUPS client configuration applies:
- [`/etc/cups/client.conf`](https://www.cups.org/documentation.php/doc-1.5/ref-client-conf.html)
- `~/.cups/client.conf`
- environment variables `CUPS_SERVER` and `CUPS_ENCRYPTION`

Run the connector:
```sh
$ gcp-cups-connector
```

The connector logs to to `/tmp/connector.INFO` (and others) by default.

If any printers are installed on the CUPS server, then they should now be available locally via Google Cloud Print. Test this by printing to the newly available GCP printer(s) from a Chrome browser.

Ctrl-C will stop the connector.

## Create a config file
This would be a good time to [create a config file](Configuration), if you need one.

## Run automatically on boot
See the [[(Linux) Run Connector Automatically on Boot]] instructions.

**Note:**
- I used the [Installing-on-Raspberry-Pi](https://github.com/google/cups-connector/wiki/Installing-on-Raspberry-Pi-Raspbian-Jessie) page for most of the setup.
- And since I have as default a PDF printer, I had to create a directory for user gcp 
- And assign myself to the gcp group to read the `/home/gcp/PDF` folder
```
sudo mkhomedir_helper gcp
sudo chmod -R 776 /home/gcp
sudo adduser YOURNAME gcp
```