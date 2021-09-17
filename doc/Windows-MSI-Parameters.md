- Run installer but don't attempt to start service:
    ```
    msiexec /i <file.msi> STARTSERVICE=NO
    ```
- Run installer but don’t init config file (install likely to fail on service start unless STARTSERVICE=NO also set):
    ```
    msiexec /i <file.msi> RUNINIT=NO
    ```
- Append arguments to init (run `gcp-connector-util init --help` for all params):
    ```
    msiexec /i <file.msi> INITPARAMS="--gcp-user-refresh-token=<token> --share-scope="
    ```
- Tell installer to use existing file as config instead of running init:
    ```
    msiexec /i <file.msi> CONFIGFILE="c:\path\to\cloud-print-connector.config.json"
    ```