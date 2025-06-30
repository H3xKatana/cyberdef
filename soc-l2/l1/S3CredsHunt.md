A prominent U.S.-based organization recently received an official notification from AWS alerting them to a potential security breach: one of their AWS access keys appears to have been compromised. Coincidentally, a subsequent internal security alert indicated that CloudTrail logging had been unexpectedly disabled. As a cybersecurity specialist, your task is to thoroughly investigate this series of events, and figure out the timeline of these events.


To properly track the activities done by the attacker in the environment, you will need to determine the source of the attack. What is the attacker's IP address?


using this 


to extract all events 

grep -R -A 20 "userName" /path/to/CloudTrail | grep "eventName" | cut -f2 -d ":" | xargs -n1 | sort | uniq



on the whole logs 
 grep -R -A 20  "userName" | grep eventName | cut -f 2 -d ":" | xargs -n1 | sort | uniq -c
      1 AddUserToGroup,
      2 AssumeRole,
      1 CreateAccessKey,
      1 CreateLoginProfile,
      1 CreateUser,
      1 DeleteTrail,
     52 DescribeCacheClusters,
     59 DescribeDBInstances,
     63 DescribeInstances,
      1 GetCallerIdentity,
      1 GetPolicy,
      1 ListAttachedGroupPolicies,
      1 ListAttachedRolePolicies,
      1 ListAttachedUserPolicies,
     48 ListBuckets,
     35 ListFunctions20150331,
      2 ListGroups,
      1 ListGroupsForUser,
     36 ListPolicies,
      1 ListRolePolicies,
      1 ListRoles,
     56 ListTables,
     58 ListTopics,
      2 ListTrails,
      1 ListUserPolicies,
      2 ListUsers,

attacker timeline 
 grep -R -B 20  "185.5.248.11" | grep event | cut -f 2 -d ":" 



some really sus one like createAcess key , delete trail , create user 

grep -R -A 20  "DeleteTrail" | grep sourceIP | cut -f 2 -d ":" | xargs -n1 | sort | uniq -c
      1 185.5.248.11,



grep -R -B 10  "185.5.248.11" | grep Time | cut -f 2,3 -d ":" | sort
==>2023-08-18T03:48


==> role :assumedrole

==> id : root_session
grep -R -B 20  "185.5.248.11" | grep  role
==> sudo_Dev0ps


pressistene 

  "eventSource": "iam.amazonaws.com",
      "eventName": "CreateLoginProfile",
      "awsRegion": "us-east-1",
      "sourceIPAddress": "185.5.248.11",
      "userAgent": "aws-cli/1.29.26 md/Botocore#1.31.26 ua/2.0 os/linux#5.15.0-78-generic md/arch#x86_64 lang/python#3.10.12 md/pyimpl#CPython cfg/retry-mode#legacy botocore/1.31.26",
      "requestParameters": {
        "passwordResetRequired": false,
        "userName": "root_admin_adam"


following the time line we can answer this easily by event name and attributes 

adminstrators 


when loggin was disblaed also just find the event 


who he did get intial acess 
based on leaked creds 


grep -R -B 20  "185.5.248.11" | grep user 
we found the user he used first 

we find assoitaed bucket with shared files public and an anonymous user acesed 