## A "connector"

A Google Cloud Print connector allows GCP clients to print to a printer that is not GCP-enabled. Some examples:

- Software
  - [Chrome GCP Connector](https://support.google.com/chrome/answer/1069693)
  - [GCP Service for Windows](https://support.google.com/a/answer/3179170)
  - [Google Cloud Print Connector for Windows/CUPS](https://github.com/google/cups-connector) (this project)
- Hardware
  - Lantronix [xPrintServer - Cloud Print Edition](http://www.lantronix.com/products/xprintserver-cloud-print/)
  - Lantronix [xPrintServer - Office](http://www.lantronix.com/products/xprintserver-office/)

## The Google Cloud Print Connector

This project is the GCP Connector. It registers the printers on a CUPS server or Windows Print Spooler with GCP. Some features:

- Implemented in the [Go programming language](https://en.wikipedia.org/wiki/Go_(programming_language)), which is fast, expressive, and very good at concurrency.
  - Lives happily on a Raspberry Pi with one printer.
  - Lives happily under heavy use serving hundreds printers.
- Makes managing multiple printers very easy.
  - One CUPS/Windows server, one Cloud Print Connector, hundreds of printers.
  - Even if some of your printers are GCP-native, it might make sense to put them behind the Cloud Print Connector for centralized management.
  - Print quality from a local Cloud Print connector may be much higher than that offered by many printers built in native GCP support.
- It's easier to update software-on-a-computer than firmware-on-a-printer.
- *Provides local printing*.
  - Many GCP printers do not or cannot implement all of the GCP features internally, due to CPU and memory constraints. To overcome these limits, these printers hand off local print jobs to the GCP cloud service, which digests the job, then sends a normal cloud print job back to the printer.
  - The Cloud Print Connector can provide every feature available from both CUPS (or Windows Print Spooler) and the printer driver, so the print job never goes to the cloud. This is important to:
    - sites that have HIPAA compliance requirements
    - sites with no Internet access
    - sites with unreliable Internet access
  - When the Cloud Print Connector is run without a config file, local-only mode is the default.