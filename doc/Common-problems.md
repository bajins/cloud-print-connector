## Run

### "4097 Error in the pull function"
```
$ connector
E0914 23:30:21.437047    8860 connector.go:33] Google Cloud Print CUPS Connector version DEV-linux
Google Cloud Print CUPS Connector version DEV-linux
E0914 23:30:24.203739    8860 connector.go:119] Ready to rock as proxy 'my-proxy'
Ready to rock as proxy 'my-proxy'
E0914 23:31:24.221903    8860 printermanager.go:207] Sync failed while calling GetPrinters(): Failed to call cupsDoRequest() [IPP_OP_CUPS_GET_PRINTERS]: Failed to connect to CUPS server /var/run/cups/cups.sock:631 because 4097 Error in the pull function.
```

Not sure why this happens, something to do with CUPS and TLS. If the connector is running on the same machine as the CUPS server, then a workaround is to simply not use TLS when talking to CUPS.

```
$ CUPS_ENCRYPTION=NEVER connector
```

Encryption can also be turned off in `/etc/cups/client.conf`.

### "Failed to register my-printer locally: Failed to add Avahi group: Not permitted"
This error occurs when the Avahi daemon is configured to disallow publishing. A complete log entry:

```
W0922 11:44:22.777678   29547 printermanager.go:116] Failed to register my-printer locally: Failed to add Avahi group: Not permitted
```

You can change the Avahi daemon config in `/etc/avahi/avahi-daemon.conf` from:
```
...
[publish]
disable-publishing=yes
disable-user-service-publishing=yes
...
```

to:
```
...
[publish]
disable-publishing=no
disable-user-service-publishing=no
...
```

### Printers are already in sync; there are 0
When using CUPS on Linux / MacOS, ensure that your server has sharing enabled, and that the printer you wish to share is marked as shared as well. This is a requirement for `cloud-print-connector` to see and share the printer via Google Cloud Print