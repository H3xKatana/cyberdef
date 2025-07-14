first malware 


rule lock_bit_ransome
{
meta:
	author="0xkatana"
	descripiton="dump of lockbit malware"

strings:
	
	$s1=".onion" 
	$s3="LockBit" 

condition:
	 $s1 and $s3

}



rule mal_stealer_family
{

meta:
	author="0xkatana"
	description="katana"

strings:
	
	$s4="Software\\Valve\\SteamLogin" wide
condition:
	 $s4

}