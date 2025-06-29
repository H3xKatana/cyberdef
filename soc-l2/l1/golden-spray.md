



index=goldenspray "winlog.event_data.IpAddress"="77.91.78.115" 
|  stats count by winlog.event_data.TargetUserName

index=goldenspray "winlog.event_data.IpAddress"="77.91.78.115"
| table winlog.event_data.TargetUserName, message, event.action

index=goldenspray
| eval login_success=if(like(message, "%An account was successfully logged on.%"), "Yes", "No")
| stats count by login_success


index=goldenspray event.code=11 winlog.event_data.TargetFilename="*.zip"
| table winlog.event_data.TargetFilename

