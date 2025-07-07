As a forensic investigator at IResponseDash, you are tasked with examining a ransomware attack that has compromised multiple endpoints. Your primary objective is to determine the delivery method of the ransomware and to trace all activities of the attacker to understand the progression of the attack.

To accomplish this, you will analyze logs, review system and network activities, and gather evidence of the attacker's actions. This investigation will allow you to provide recommendations for addressing the current incident and enhancing defenses to prevent future attacks.


we were given 2 machines fiile system to analyse 

cheking loigin we find a login with multiple failes in the logs of IT_machine for hani_it account 

q1/q2
we get ip and SID 192.168.19.100 S-1-5-21-1393444541-2628512620-2908104607-1112

q3
checking powershell logs 
Set-MpPreference -DisableRealtimeMonitoring $true


q4 by checking recent acess files in appdata folder 
we find a file called ipall.txt 

q5 checking for runned programs  with prefetch we find that netscan a suspiooucs network scanning process 
we find it it in a folder called tools with other tools likley used by attacker 
we will check it more later


q6- the attacker also used RClone to exfil data 


q7- we find that the attacker used mstct.exe / rdp to login for 192.168.31.129 and checking other rdp logs we find acess to  192.168.31.130


q8- checking the lan fileshares from the registry HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanServer\Shares 

q9- this is a hard question to find the hash from the prefetch the tools called final 

but we need to get the hash how there is a good artifcat that keeps the hash of the files run on the system 
and can be pareesed with pecmd 

 Amcache_UnassociatedFileEntries.csv

 in appcompat/programs/amache.hve

q10 checking the new extenstion by looking at the mft table after malware ran 

q11- registry added is the run key for prestince or for ransmware note 
c:\users\hanii_it\appdata\local\temp\how_to_decrypt.hta i