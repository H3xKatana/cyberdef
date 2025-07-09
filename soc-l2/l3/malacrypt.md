a good challs for static/some dynamic


first started with hashmyfiles and detect-it-easy
to get the basic infot



then we used Floos to get the strings from code and stack .. emulation and it is really cool tool 

it got most of stirngs 


we got windows api directly from floos and chatgpt 


we got the ip using  floos stack


now let's go with moredynamic 

buy runing program we get error 

using  procmon and see loded dlls we find ntdll.dll first dll loaded  and also we find reg queires 


with some static analysis i used cuter with autonaming we find main and following it we find first api call for Getenv

we find by floos some string for coperation called Zeon


finally i used Capa which is my first time to use this it is a tool that test multiple analysis techniques and yara rules on malware and give you a summary 