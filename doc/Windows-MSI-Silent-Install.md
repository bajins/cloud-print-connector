- [Introduction](#introduction)
- [Method A (most secure)](#method-a-most-secure)
- [Method B (less secure but easier to automate)](#method-b-less-secure-but-easier-to-automate)

# Introduction
It’s possible to automate the installation of Google Cloud Print MSI across Windows machines. Automated / Silent installs allow for quick deployment of the connector on many servers (retail kiosks, remote print clients, etc).

## Important
The new connector creates and utilizes a special "robot" account for each connector installation. Robot accounts have access to manage printers and print jobs for their own connector instance. They cannot access printers or print jobs for other connector instances even if the same user account was used to authorize both. Robot accounts do not have access to modify sharing permissions of printers (even the printers they own). Thus, the user refresh token (gcp-user-refresh-token) must be preserved in the config file if automatic sharing is desired. Be aware that the user refresh token has access to all printers and print job data for printers owned by the user. For this reason, we recommend not using automatic sharing when many connectors are in use so that user refresh tokens are not stored in the config file and each connector instance is isolated from accessing another’s printers and print jobs.

# Method A (most secure)
1. On a master device, install the connector and enter some email address for sharing printers. It’s important to enter an address for sharing so that the user refresh token is stored in this master config file (this config file will not be copied to other devices).

2. Open the `c:\programdata\google\cloud print connector\gcp-windows-connector.config.json` file in a text editor and copy the value for `gcp-user-refresh-token`.

3. Open an admin command prompt (elevated privileges) and run:

    ```
    cd "\Program Files\Google\Cloud Print Connector"
    for /L %i in (1,1,100) do (gcp-connector-util.exe init --gcp-user-refresh-token=1/9dabLHkA3vL_OPly_gkpjtqEV-uaHtxnUeNhQl5BQHs --share-scope= & move gcp-windows-connector.config.json %i.json)
    ```
    Replace `1/9dabLHkA3vL_OPly_gkpjtqEV-uaHtxnUeNhQl5BQHs` with your own refresh token from step 2. This will create config files for 100 automated installations of the connector. To modify this number, edit the last number in parenthesis, like (1,1,50) to do 50.

4. This will leave you with numerous JSON files in `C:\Program Files\Google\Cloud Print Connector` numbers 1 through 100. Each JSON file will have a unique robot account (robot-refresh-token) associated with it. All printers created by these config files will be owned by the Google user you authenticated with in step 1 but they will not have access to each others printers or print jobs. This means if one connector is compromised, only it's printers and jobs are affected.

5. Copy each config files to the appropriate remote computer(s) which will run the connector along with the connector MSI file. The MSI installation can be automated with the command:
    ```
    msiexec /qr /i windows-connector.msi CONFIGFILE=1.json
    ```

# Method B (less secure but easier to automate)
1. On a master device, install the connector and enter some email address for sharing printers so that the user refresh token is stored in the config file.

2. Open the `c:\programdata\google\cloud print connector\gcp-windows-connector.config.json` file in a text editor and copy the value for gcp-user-refresh-token.

3. For each additional computer which will run the connector, automate the installation with:

    ```
    msiexec /qr /i windows-connector.msi INITPARAMS="--gcp-user-refresh-token=1/9dabLHkA3vL_OPly_gkpjtqEV-uaHtxnUeNhQl5BQHs --share-scope="
    ```
    Replace `1/9dabLHkA3vL_OPly_gkpjtqEV-uaHtxnUeNhQl5BQHs` with your own user refresh token value from step 2. 

This will automate the MSI installation. Be aware that anyone with access to the `gcp-user-refresh-token` value will have access to all printers and print job data sent to printers owned by the Google user. For this reason we recommend protecting the user refresh token and keeping it off the connector installations using [method A](#method-a-most-secure).