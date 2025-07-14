multi stage malware useds SFX windows build in for obfusscation uses a bat file to build a the second stage file 


rule SFX_dropper_stage1
{
	meta:
		descriptono="multi stage dropper with cab file first for stager then a bat file that build the 2nd stage"
		author="0xkatana"

	strings:
		$s1="cmd.exe /d /c"
		
		
	condition:
		all of them


}


