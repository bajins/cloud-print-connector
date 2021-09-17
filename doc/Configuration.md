## Why?

Good question. You may not need to configure the connector at all. Consider these lists of pros and cons before proceeding:

Running the Connector without a config file:
- computer printers => GCP printers
- clients
 - Chrome/ChromeOS
 - Android
- print via the local subnet
- no Internet connection required
- good for homes and small offices

Running the Connector with a config file:
- computer printers => GCP printers
- clients
 - Chrome/ChromeOS
 - Android
 - [Windows](https://tools.google.com/dlpage/cloudprintdriver)
 - third-party clients that don't support local printing
- print, manage sharing, manage print jobs via the Google Cloud Print service
- reliable Internet connection required
- good for schools and enterprise

## Get started

Run the following command to create a config file called `gcp-cups-connector.config.json`:
```sh
$ gcp-connector-util -init
```

Newer builds will fail; remove the hyphen:
```sh
$ gcp-connector-util init
```

On Windows, create `gcp-windows-connector.config.json`:
```sh
gcp-connector-util.exe init
```

To use the config file, run the connector. The `-config-filename` flag is optional; without it, the connector will look for a file named `gcp-cups-connector.config.json`:
```sh
$ gcp-cups-connector -config-filename gcp-cups-connector.config.json
```

## Config file parameters

### File Format

The Connector config file is in JSON format, so it looks like this:

```
{
  "string_name": "value",
  "boolean_name": true,
  "integer_name": 123,
  "list_name": [
    "value0",
    "value1"
  ],
}
```

### Options

option | description
-------|------------
`cups_max_connections` | Limits the quantity of connections to the CUPS server.
`cups_connect_timeout` | How much patience to apply when opening a new connection to the CUPS server.
`cups_job_queue_size` | Limits the quantity of jobs queued in the CUPS server. Excess jobs wait in the connector queue.
`cups_printer_poll_interval` | The interval between checking the CUPS server for printer changes (state, PPD). When the connector is on the same host as the CUPS server, this value can safely be very small, say `10s`, to keep printer state updated in the Google Cloud Print cloud service.
`cups_printer_attributes` | CUPS printer attributes are converted to CDD tags. This can be used to remotely monitor a CUPS printer attribute via Google Cloud Print. The default list of attributes are required for the CUPS Connector to function properly.
`cups_vendor_ppd_options` | CUPS PPD options to enable, listed by PPD MainKeyword. By default, the connector looks for options that are common. To enable all PPD options, simply list "all" instead of each MainKeyword (be careful, some printers dozens of PPD options that don't translate well).
`cups_job_full_username` | Whether to use the full username (joe@example.com instead of joe) to create CUPS jobs.
`cups_ignore_raw_printers` | Raw printers are ignored by default; change that here.
`cups_ignore_class_printers` | Classes are ignored by default.
`copy_printer_info_to_display_name` | Use the CUPS `printer-info` printer attribute as the Google Cloud Print display name. Otherwise, the `printer-name` attribute is used.
`prefix_job_id_to_job_title` | Adds the Google Cloud Print job ID to the beginning of the CUPS print job title. This can be useful for debugging.
`display_name_prefix` | Adds a prefix to the GCP printer name.
`printer_blacklist` | Names of printers to not share with GCP.
`printer_whitelist` | Names of printers to only share with GCP. If empty or absent, then all printers discovered, minus those named in `printer_blacklist` are shared.
`monitor_socket_filename` | The tool `gcp-cups-connector-util -monitor` uses this socket to query the connector. The default value places this file in `/tmp` but a more traditional location is `/var/spool`.
`local_printing_enable` | Enable local printing, where the print client communicates directly with the CUPS Connector. "Local printing" is also known as ["Privet" or "GCP 2.0"](https://developers.google.com/cloud-print/docs/privet).
`cloud_printing_enable` | Enable "cloud" printing, where the print client communicates with the CUPS Connector via the Google Cloud Print cloud service. This is what most people think of when they talk about Google Cloud Print.
`log_file_name` | Name of the log file.
`log_file_max_megabytes` | Maximum log file size.
`log_max_files` | Maximum quantity of log files.
`log_level` | Threshold for minimum log severity. Can be one of `"FATAL"`, `"ERROR"`, `"WARNING"`, `"INFO"`, `"DEBUG"`

### Options unique to cloud printing

These are only used for "cloud" printing, ie non-local printing.

option | description
-------|------------
`xmpp_jid` | [XMPP JID](http://xmpp.org/extensions/xep-0029.html), the unique identifier of this connector on the XMPP connection. [XMPP is an open chat protocol](https://en.wikipedia.org/wiki/XMPP), used by the Google Cloud Print cloud service to notify the connector of a new print job.
`robot_refresh_token` | This token is used to authenticate HTTP requests and the XMPP conversation, to the Google Cloud Print cloud service. This token is like a password for the connector, so it's important that you treat it as such.
`user_refresh_token` | This token is used to authenticate automatic sharing requests to the Google Cloud Print cloud service. This token is a bit more sensitive than `robot_refresh_token`, as it can be used outside of the context of the connector.
`share_scope` | The group or user to automatically share printers with, indicated with an email address.
`proxy_name` | This is the name given to one instance of the connector. If you only have one connector, then the name can be anything. If you have more than one connector, be sure to give each connector a unique name.
`xmpp_server` | The FQDN for Google's XMPP service. **Do not change.**
`xmpp_port` | The port number for Google's XMPP service. The default value is 443, which should work with any firewall.
`gcp_xmpp_ping_timeout` | The CUPS Connector pings the XMPP server periodically. An error state is detected when this timeout is exceeded.
`gcp_xmpp_ping_interval_default` | Interval between XMPP pings.
`gcp_base_url` | The base URL to access the Google Cloud Print cloud service. **Do not change.**
`gcp_oauth_client_id` | Identifies the CUPS Connector to the Google Cloud Print cloud service. **Do not change.**
`gcp_oauth_client_secret` | Goes along with the Client ID. Not actually secret. **Do not change.**
`gcp_oauth_auth_url` | Google's OAuth2 authentication URL. **Do not change.**
`gcp_oauth_token_url` | Google's OAuth2 token URL. **Do not change.**
`gcp_max_concurrent_downloads` | Limits the maximum quantity of job payloads (normally PDF file) to be downloaded at once. If you have a very busy print server, and have a fast Internet connection, then it might make sense to increase this value.