like yara rules but for logs good to integrate with SIEM for easier logs parsing 

example of sigma rule 
# Level 3
# A particular log has raised concerns due to its indication of a defense evasion tactic. In an effort to proactively detect such stealthy maneuvers in subsequent instances, would you be able to formulate a Sigma rule targeting this specific activity?
title: a short capitalised title with less than 50 characters
id: generate one here https://www.uuidgenerator.net/version4
status: experimental
description: A description of what your rule is meant to detect
logsource:                      # important for the field mapping in predefined or your additional config files
    category: process_creation  # In this example we choose the category 'process_creation'
    product: windows            # the respective product
detection:
    selection:
        Image: 'C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe' 
        TargetObject: 'HKLM\System\CurrentControlSet\Control\SESSION MANAGER\Environment\__PSLockdownPolicy'
    condition: selection


#############

    Image: 'C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe' 
    CommndLine|contains: 
        -   'bypass'
        -   'base64'