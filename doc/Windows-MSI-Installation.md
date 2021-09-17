# Requirements
* 64-bit Windows Server or Client OS
  * Windows 7 Client or newer
  * Windows Server 2008 R2 or newer
* 32-bit Windows is not supported
* Memory and CPU requirements will vary based on number of users, printers and print jobs. Most boxes will handle up to a dozen or so printers with regular user usage. For hundreds of printers in a server environment we'd recommend a dedicated server.
* Network must allow
  * HTTPS connections to www.google.com and accounts.google.com.
  * XMPP connections over port 443 to talk.google.com

# Installation
The Windows MSI installation process is fairly straightforward for a single server. For advanced installation options see the [Windows MSI Parameters](Windows-MSI-Parameters) and [Windows Silent Install](Windows-MSI-Silent-Install) articles.

1. Download the latest [MSI release](https://github.com/google/cloud-print-connector/releases).
1. Launch the MSI installer by double clicking it.
1. After accepting to the license, you'll be prompted to set a path and the connector files will be installed.
1. After installing files, the connector needs to be configured in order to successfully complete the MSI install and start the connector. A black console window will open.
1. You'll be prompted to go to https://www.google.com/device and enter the supplied code. **Be sure you are logged into the account you wish to own your Cloud Print Connectors before entering the code in the website**
1. Once you've entered the code and selected the account which should own the printers, switch back to the black console window, the setup script should continue on it's own once it detects you've entered the code.
1. Now you'll be prompted to auto-share printers created by the connector with a single user or group of users. If you choose not to auto-share, you can always share the printers manually from google.com/cloudprint/printers when logged in as the owner account.
1. Installation of service files and starting of the connector service should now complete. If the service fails to start, there may have been a problem with your answers in the console window, try cancelling the installation and running the MSI again.

# Maintenance
* The connector runs as a Windows service. You can stop/start/restart it from Administrative Tools > Services.
* To run the connector manually:
  1. Stop the connector service from the Windows service tool.
  1. Within the connector service, copy the "Path to executable". This should be the full Windows path to gcp-windows-connector.exe followed by the --config-filename argument and full path to the config file.
  1. Open a command prompt and run the full command copied from above, the connector should start and allow you to see it's progress.
* The connectors config file path is shown in the above path to executable. You should also be able to find it by opening File Explorer and browsing to %ProgramData%\Google (which usually resolves to something like C:\ProgramData\Google). See the [Configuration](Configuration) article for more details.