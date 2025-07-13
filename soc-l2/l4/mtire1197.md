chcking the windows defender logs and events 



with this 

index="mitre-t1197" winlog.event_id=1116 
|  table message 
| sort time

we find multiple hits for this Microsoft Defender Antivirus has detected malware or other potentially unwanted software.
 For more information please see the following:
https://go.microsoft.com/fwlink/?linkid=37020&name=Trojan:Win32/Meterpreter.O&threatid=2147729928&enterprise=0


wchi is use used by metasploit for connections 


https://lolbas-project.github.io


we find that bitsadmin and the name of challenge as a hint for bitsadmin.exe used for data transfer



then we start analysing the lgos for the bitsadmin.exe with 

to find first run for bitsadmin we use 1/4866 and bitsadmin to find poreess startup 
2023-07-31 17:39

using this query we find doladed files from a local ip in the network 
index="mitre-t1197" winlog.event_id=60 | table _time, message this is will show all finished transfers 

192.168.190.136/test.exe 

192.168.190.136/Ran5som.exe
  


