When running on Windows, the connector uses native [Windows Event log](https://technet.microsoft.com/en-us/library/aa996117(v=exchg.65).aspx) entries. Within the Event Viewer, under Windows Logs > Applications, you should see events from source "Google Cloud Print Connector". The log entry is anything after "The following information was included with the event:"

From Powershell, it's possible to extract the connector's logs. Try running:

TODO: This command works but lacks timestamps and seems overly complicated. If anyone knows a cleaner way to convert Windows events to a plaintext log output, please update.
 
```
Get-WinEvent -LogName Application -FilterXPath "*[System[Provider[@Name='Google Cloud Print Connector']]]" -Oldest | foreach-object { $event = [xml]$_.ToXml(); Write-Host $eve
nt.Event.EventData.Data} *>> connector.log
```
```
#Option 2
Get-EventLog -LogName application -Source 'Google Cloud Print Connector' | export-csv c:\GCPC_Event.txt -NoTypeInformation #You can also replace .txt with .csv

#Just Errors
Get-EventLog -LogName application -Source 'Google Cloud Print Connector' | Where EntryType -eq Error | export-csv c:\GCPC_Event.txt -NoTypeInformation
```