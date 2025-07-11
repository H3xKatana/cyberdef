Following a security breach on our Linux e-commerce web server that disrupted our operations, the security team quickly prepared the system for a live forensic analysis. Your objective is to investigate the active environment and map the attack vectors utilized to compromise our web server.



this is a life forensics case we have the server online 


we checking systemctl we find apache2 is running 
now checking /var/www/html and 
then acess logs 
we find descrption.php


checking the acess logs we find lfi vuln on parammetre on description 
to acess /etc/passwd

checking logins attempts on /var/logs/auth.log
we find multiple requests to login on the account jermey 


checking the audit logs :
/var/log/audit/audit.log files are part of the Linux Audit System, used to record security-relevant events on a system.
and checking User_Cmd request on exe we get a running reuqest on a directory /dev/shm/

checking cron jobs /ect/cron
we find this cront job
systemctl list-units --type=service --state=running | grep apache > /root/running_services.txt

checking previliges escalation 
most used file is suderos and it is the one 


checking audit logs and files we find archive1.tar.gz

a user was created called ftp to trick the analyst to think it is a service user account 


the user was added to sudo group