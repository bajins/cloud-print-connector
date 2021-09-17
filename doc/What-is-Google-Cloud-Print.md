## Driverless printing
Most print systems require specific drivers for each printer. Finding drivers can be a chore, and one driver doesn't work for all operating systems. Google Cloud Print can be described concisely as "driverless printing".
- GCP printers describe themselves with JSON-encoded data structures.
- Clients submit print jobs with JSON-encoded data structures; payloads are PDF, Postscript or PWG Raster.

Printers and clients can communicate in two different ways, each with pros and cons.

## Cloud
Printers that communicate via the GCP backend service require an Internet connection to function:
- The GCP cloud service manages printers, print jobs and sharing.
- Clients
  - must have explicit permission granted by the printer owner, in order to discover printers and submit jobs.
  - submit print jobs with [an HTTPS request](https://developers.google.com/cloud-print/docs/appInterfaces#submit).
  - submit print jobs from anywhere that has an Internet connection.
- Printers
  - listen for new print jobs on [an XMPP channel](https://developers.google.com/cloud-print/docs/rawxmpp) and [download payloads](https://developers.google.com/cloud-print/docs/proxyinterfaces#fetch) via HTTPS.
  - must have reliable Internet access.
  - are not affected by access points in isolation mode because client and printer never communicate directly.

Three network connections are created by the printer:

server DNS name | port | protocol | function
----------------|------|----------|---------
`accounts.google.com` | 443 | HTTPS                 | authenticate with [OAuth 2.0](https://developers.google.com/identity/protocols/OAuth2)
`www.google.com`      | 443 | HTTPS                 | register with, receive print jobs from [GCP backend](https://developers.google.com/cloud-print/docs/proxyinterfaces)
`talk.google.com`     | 443 | TLS-encapsulated XMPP | receive [print job notifications](https://developers.google.com/cloud-print/docs/rawxmpp)

Clients create one network connection to https://www.google.com/cloudprint to discover printers and create print jobs.

## Local
Local mode is a lot like [AirPrint](https://en.wikipedia.org/wiki/AirPrint). Printers that communicate locally are great for homes and small companies:
- Clients
  - discover printers when they are on the same subnet (connect to an access point where the printer lives)
  - forget printers when they are not on the same subnet (disconnect from the access point)
  - print by communicating directly with the printer
- Printers
  - manage their own print queues, pushing back on clients as needed
  - announce their presence to all clients within the local subnet
  - have an HTTP server to provide detailed self-description, and to accept print jobs
  - requires that the network policy, or WiFi access point, not isolate the printer from the client. Some access points call this "isolation mode".
  - requires that the network policy allow [multicast](https://en.wikipedia.org/wiki/IP_multicast) for mDNS discovery
  - most depend on the GCP backend to handle print job processing, like format conversion (PDF to PWG Raster) and content adjustments (margin adjustments, fit-to-page, collation, ordering). These printers may require Internet access, or may provide more features when an Internet connection is available.

Two network connections are created by the printer:

mode | port | function
-----|------|---------
mDNS + DNS-SD | 5353, multicast        | [announce presence](https://developers.google.com/cloud-print/docs/privet#discovery) to the local subnet
HTTP          | arbitrary port, listen | [HTTP server](https://developers.google.com/cloud-print/docs/privet#api) provides detailed self-description, receives print jobs