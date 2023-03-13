---
layout: post
date: 2023-03-06 09:52:36
title: AutoIT Encrypt Script
category: chocolatey
tags: automation chocolatey package deployment
---
# AutoIT Encrypt Script

Source: [Autoit](https://www.autoitscript.com/forum/files/file/491-codescannercrypterbundle/)


• Open MCFinclude.au3 in Scite and find Func MCFCC_Init()

![image](https://user-images.githubusercontent.com/1507737/224678641-892ba103-fe79-44c7-901d-103b6389a0fe.png)

• Select any existing @macro or Function call that defines run time content of array CCkey , or add your own key definitions.

• $CCkey array index = key ID

• Write down which key ID(s) to use for this encryption!

• Close MCFinclude.au3



In your target script, add the line: #include “MCFinclude.au3” (with path, if located elsewhere)

![image](https://user-images.githubusercontent.com/1507737/224678896-e7e36b9c-f4b3-4b69-a174-147e910d46e4.png)

• Below all other #includes

• Above your own code and save the script.

![image](https://user-images.githubusercontent.com/1507737/224679215-f4a4ce8d-0867-4912-ae79-cfdc7488a0a9.png)

• Run Code Scanner on your script (this takes a long time).

• Check that no issues were detected (or fix these and rerun).

• Subdirectory :<name>.au3.CS_DATA is created (if not, ensure that in CodeScanner’s Settings option [Write MetaCode ] is enabled, and re run.
  
• Close CodeScanner
  
![image](https://user-images.githubusercontent.com/1507737/224679659-6663c071-fb35-423e-8d04-db8e5af49bab.png)

• Start Code Crypter
  
• Press [ to load your script.(the CS_DATA path is filled automatically).
  
• Tick Options [Create MCF0]and [BackTranslate]
  
• Press [ Run](this will take a long time), then Exit.
  
• A new test file MCF0test.au3 has been created in your target file’s home directory. Open it in Scite , check for errors, and do test runs. Ensure that it works exactly as the original.

![image](https://user-images.githubusercontent.com/1507737/224679995-6f303066-d0ba-4455-8642-3315bb1572f8.png)

• Restart Code Crypter.
  
• Under Tab Encrypt, specify the Key ID ; this is the selector in array CCkey in MCFinclude.au3 
	
	*The KeyID 8 in the source code MCFinclude.au3
  
• Under Tab Main, disable option [Create and enable option [ Encrypt]; then Press [Run]
  
• A new encrypted file MCF0test.au3 is created in your target’s home directory.
  
• Open it in Scite , check for errors, do test runs. Ensure that it works exactly as the original.
  
![image](https://user-images.githubusercontent.com/1507737/224680085-6e7abb4d-1e98-4ff9-8d5d-3704dfe40edc.png)
  
![image](https://user-images.githubusercontent.com/1507737/224680178-d41e6f19-e5a0-424d-8fbb-3e243affdfee.png)

source code MCFinclude.au3

```
; =======================================================================================================================
; Title .........: MCFinclude.au3
; AutoIt Version : 3.3.14+
; Description ...: CodeCrypter target's UDFs
; Author.........: A.R.T. Jonkers (RTFC)
; Release........: 3.3
; Latest revision: 11 July 2020
; License........: free for personal use; free distribution allowed provided
;					the original author is credited; all other rights reserved.
; Tested on......: W10Pro/64
; Forum Link.....: http://www.autoitscript.com/forum/topic/155537-mcf-metacode-file-udf/
; Related to......: CodeScanner, MCF, CodeCrypter by RTFC
; Dependencies...:	AES.au3, by Ward; http://www.autoitscript.com/forum/topic/121985-autoit-machine-code-algorithm-collection/
;					Crypto_NG, by TheXman; https://www.autoitscript.com/forum/files/file/490-cryptong-udf-cryptography-api-next-generation/
; ===============================================================================================================================
; Remarks
;
; * Edit/Add keytype definitions in the last UDF of this script: _MCFCC_Init()
;	NB Any key-generating UDFs you add (to fill $CCkey entries at runtime) need to be placed within #Region Encryption2 ABOVE(!) MCFCC_Init().
;
; * if using a password query at startup (set keyID = 1), its timeout parameter can be edited in _MCFCC_Init() (search for "InputBox" there)
;
; * This UDF is called by CodeCrypter, and should be #included into any script
;		you wish to run through CodeCrypter.
;		Any code prior to this #include will not be encrypted; all code following it will be encrypted, if specified in CodeCrypter.
;
; * Please do NOT explicitly include any other MCF-related scripts into your target script!
;	Example: do not #include MCF.au3 (or any part thereof) in your target script.
;
; * If you make changes to this UDF, save it under a new name and change
;		global variable $MCFinclude in this script to reflect this.
;
; * The default encryption engines are Ward's AES UDF for x86-only code, and
;	TheXman's CryptoNG UDF for x86/x64 code.
;		You can replace these by whatever algorithm you desire, BUT be aware that
;		any code you replace it with has to be FAST ENOUGH not to slow down the
;		the script too much (as it may be called in almost every line).
;		Timing-dependent code (e.g., Adlib calls, user event loops in games) may
;		FAIL if decryption handling time >= loop interval (causing stack queueing
;		and/or stack overflow = crashing or hanging script).
;		So using AutoIt's native Crypt library is out of the question.
;
; * A list of actions to take when replacing your encryption algorithm is given
;		in the Remarks of MCF.au3
;
; * If processing is too slow, you can reduce the proportion of encrypted lines <100%
;		by setting 	$MCF_ENCRYPT_SUBSET=true, and
;						$subset_proportion <1 (N*100 = percentage, randomly assigned), or
;						$subset_proportion >1 (cycled = 1 in N lines encrypted)
;
; * Encryption can be two-pass (nested):
;		- outer shell encrypted with key $CCkey[0], using a fixed string (supplied)
;		- inner shell encrypted with key $CCkey[#], containing whatever you want, defined at runtime
;			some simple examples are provided in _MCFCC_Init()
;	Note: you can switch to two-pass encryption by setting $MCF_ENCRYPT_NESTED=True
;			or in CodeCrypter, by ticking the box for "Nested Keys" (under Tab "Encrypt")
;
; * $CCkey[1] in MCFinclude.au3 contains a dummy string, to be replaced at target's
;			runtime by either a parsed commandline parameter or a user password query
;			(to be specified in CodeCrypter)
;
; * IMPORTANT: $CCkey[2-99] should NOT contain any fixed definition.
;		Instead, use data retrieved from the work environment at runtime, for example:
;		- from the user (password query)
;		- from the host machine (e.g, macros, keyfile, machine specs, environment var...)
;		- from a local server or web server
;		- from an external device
;		- something returned by your own UDF
;		- use your imagination
;
; * IMPORTANT: you are not restricted to your own user environment!
;		Variable $decryption_key can be PRESET with whatever is expected in the
;		target environment. See CodeCrypter's Remarks for more details.
;
; * if you use environment variables to define a key ($CCkey[#]=EnvGet("SOME_VAR"),
;		and that variable does not exist in the target environment, an empty string
;		would be returned by EnvGet(), triggering an error.
;		So ensure beforehand that your environment variable exists in the target
;		environment, or use something else instead.
;
; * You can combine multiple keytypes by:
;		1. grouping them consecutively in $CCkey[X] to $CCkey[Y]
;		2. setting $MCF_ENCRYPT_SHUFFLEKEY=True
;		3. defining the range by setting $CCkeyshuffle_start=X and $CCkeyshuffle_end=Y
;		This way the encryption keytype will be assigned at random per encrypted line.
;
; ===============================================================================================================================
#include-once

#include ".\CryptoNG.au3" ; by theXman, https : / / www.autoitscript.com / forum / files / file / 490 - cryptong - udf - cryptography - api - Next - generation /
#include ".\AES.au3" ; by Ward, patched version by RTFC(Do Not use original version) ; www.autoitscript.com / forum / topic / 121985 - autoit - machine - code - algorithm - collection /

#Region Indirection (to be obfuscated only)
; NB do NOT edit this entire region!

; if enabled, these functions replace (unencrypted) direct assignments by
; (encryptable) function calls

; this func def should be the first one of this region,
;	as it is used as its start marker
Func _VarIsVar(ByRef $a, ByRef $b)
	$a = $b          ; e.g., for copying arrays
EndFunc   ;==>_VarIsVar


Func _ArrayVarIsVar(ByRef $a, $b, ByRef $c)
	$a[$b] = $c
EndFunc   ;==>_ArrayVarIsVar


Func _VarIsArrayVar(ByRef $a, ByRef $b, $c)
	$a = $b[$c]
EndFunc   ;==>_VarIsArrayVar


Func _ArrayVarIsArrayVar(ByRef $a, $b, ByRef $c, $d)
	$a[$b] = $c[$d]
EndFunc   ;==>_ArrayVarIsArrayVar


Func _VarIsNumber(ByRef $a, $number)
	$a = Number($number)
EndFunc   ;==>_VarIsNumber


Func _ArrayVarIsNumber(ByRef $a, $b, $number)
	$a[$b] = Number($number)
EndFunc   ;==>_ArrayVarIsNumber
#EndRegion Indirection (to be obfuscated only)


#Region Encryption1 (to be obfuscated only)
; DO NOT EDIT THIS PART!
Global $MCFinclude = "MCFinclude.au3"      ; this script (do NOT use @ScriptName)
Global $MCFprofile = ".\profile.mcf"      ; encrypted storeCC output (for CodeCrypter.au3 and storeCCprofile.au3)
Global $dummypwd = "<EnterUserPassword>"  ; fake content to flag user query
Global $CCkeytype = 2                      ; do NOT change this here
Global $CCkey[100], $CCkeyhandle[100]
For $cc = 0 To UBound($CCkey) - 1
	$CCkey[$cc] = Null      ; NB Null is not the same as empty ("")
	$CCkeyhandle[$cc] = Null
Next
$CCkey[0] = "0x3CA86772DB0B25CBD8AC911792C2217A9DD04C218DAE0F4261BD76EF512838FBDE2BDA417829E56D62EDE396B376E2CC"  ; do NOT edit
$CCkey[1] = $dummypwd  ; triggers decryption key (password) query at startup
$CCkey[2] = @ComputerName  ; required for initialisation and testing

; default cryptoNG parameters
Global $hAlgorithmProvider = -1                      ; filled at initialisation below
Global $sAlgorithmId = $CNG_BCRYPT_AES_ALGORITHM      ; you can change your preferred (SYMMETRIC!) Crypto_NG algorithm here
Global $sProvider = "Microsoft Primitive Provider"  ; ensure this is still correct if you change the algo above

_CryptoNGinit4CC()        ; needs to be called BEFORE _MCFCC_Init()

Func _CryptoNGinit4CC()

	__CryptoNG_Startup()    ; open dll
	If @error Then
		MsgBox(262144 + 4096 + 16, "Fatal CryptoNG Error", "Unable to access bcrypt.dll" & @CR & _CryptoNG_LastErrorMessage(), 3)
		Exit 1
	EndIf

	$hAlgorithmProvider = __CryptoNG_BCryptOpenEncryptionAlgorithmProvider($sAlgorithmId, $sProvider)
	If @error Then
		MsgBox(262144 + 4096 + 16, "Fatal CryptoNG Error", "Invalid Algorithm Provider" & @CR & _CryptoNG_LastErrorMessage(), 3)
		Exit 2
	EndIf

	; set block chaining mode
	If $sAlgorithmId <> $CNG_BCRYPT_RC4_ALGORITHM Then
		__CryptoNG_BCryptSetProperty($hAlgorithmProvider, $CNG_BCRYPT_CHAINING_MODE, $CNG_BCRYPT_CHAIN_MODE_CBC)
		If @error Then
			If $hAlgorithmProvider <> -1 Then __CryptoNG_BcryptCloseAlgorithmProvider($hAlgorithmProvider)
			MsgBox(262144 + 4096 + 16, "Fatal CryptoNG Error", "Invalid block chaining mode" & @CR & _CryptoNG_LastErrorMessage(), 3)
			Exit 5
		EndIf
	EndIf
	$CCkeyhandle[0] = _CryptoNGgetHandle()  ; we need this immediately

	OnAutoItExitRegister("_CryptoNGcleanup")

EndFunc   ;==>_CryptoNGinit4CC


Func _CryptoNGcleanup()

	For $cc = 0 To UBound($CCkeyhandle) - 1
		If IsPtr($CCkeyhandle[$cc]) Then __CryptoNG_BcryptDestroyKey($CCkeyhandle[$cc])
	Next
	$CCkey = Null
	$CCkeyhandle = Null
	If $hAlgorithmProvider <> -1 Then __CryptoNG_BcryptCloseAlgorithmProvider($hAlgorithmProvider)
	__CryptoNG_Shutdown()

EndFunc   ;==>_CryptoNGcleanup


Func _CryptoNGgetHandle($keyID = 0)

	; get key's salted hash
	Local $temp = _CryptoNG_PBKDF2($CCkey[$keyID], $CCkey[$keyID], 123, $CNG_KEY_BIT_LENGTH_AES_192, $CNG_BCRYPT_SHA1_ALGORITHM)
	If @error Then
		MsgBox(262144 + 4096 + 16, "Fatal CryptoNG Error", "Invalid PBKDF2 call; CNG error = " & @CR & _CryptoNG_LastErrorMessage(), 3)
		Exit 1
	EndIf

	; generate symmetric key handle
	$temp = __CryptoNG_BCryptGenerateSymmetricKey($hAlgorithmProvider, $temp)
	If @error Then
		MsgBox(262144 + 4096 + 16, "Fatal CryptoNG Error", "Invalid keyhandle; CNG error = " & @CR & _CryptoNG_LastErrorMessage(), 3)
		Exit 4
	EndIf

	Return $temp
EndFunc   ;==>_CryptoNGgetHandle


Func _MCFCNG(Const $hexstring, $index = 0)
	Return __CryptoNG_BCryptDecrypt($sAlgorithmId, $hexstring, $CCkeyhandle[$index])    ; expects a binary encryption and key handle
EndFunc   ;==>_MCFCNG


Func _MCFCC(Const $hexstring, $index = 0)
	Return _AesDecrypt($CCkey[$index], $hexstring)   ; expects encryption and key as string
EndFunc   ;==>_MCFCC
; This func def HAS to be the last one of this region, as it is used as its end marker.
#EndRegion Encryption1 (to be obfuscated only)


#Region Encryption2 (to be fixed-key encrypted)

_dummyCalls()            ; do not remove!

Func _dummyCalls()    ; DO NOT EDIT!
	; prevents MCF:_CreateSingleBuild() from removing the defs as redundant
	; this UDF will be fixed-key encrypted

	_MCFCC_Init($CCkeytype, False, @AutoItX64, True) ; DO NOT REMOVE! To be edited by CodeCrypter with your selected keytype!
	_MCFCC("")                        ; DO NOT REMOVE! Otherwise decryptor UDF is considered redundant by CodeScanner!
	_MCFCNG("init")                        ; DO NOT REMOVE! Otherwise decryptor UDF is considered redundant by CodeScanner!

	Local $a = 0, $b = 1, $c[1]
	_VarIsVar($a, $b)               ; copy var
	_ArrayVarIsVar($c, 0, $a)          ; copy single value into indexed array location
	_VarIsArrayVar($a, $c, 0)          ; copy single value from indexed array location
	_ArrayVarIsArrayVar($c, 0, $c, 0) ; copy indexed array location to indexed array location
	_VarIsNumber($a, 1)               ; assign number to var
	_ArrayVarIsNumber($c, 0, 1)      ; assign number to indexed array location

EndFunc   ;==>_dummyCalls

Func _DomainComputerBelongs($strComputer = "localhost")
    ; Generated by AutoIt Scriptomatic
    $Domain = ''
    $wbemFlagReturnImmediately = 0x10
    $wbemFlagForwardOnly = 0x20

    $objWMIService = ObjGet("winmgmts:\\" & $strComputer & "\root\CIMV2")
    If Not IsObj($objWMIService) Then Return SetError(1, 0, '')
    $colItems = $objWMIService.ExecQuery("SELECT * FROM Win32_ComputerSystem", "WQL", _
                                            $wbemFlagReturnImmediately + $wbemFlagForwardOnly)

    If IsObj($colItems) then
        For $objItem In $colItems
            $Domain = $objItem.Domain
        Next
    Endif
    Return $Domain
EndFunc


Func _MCFCC_Init($type = 0, $query = True, $useCNG = False, $updateCCkeys = True)
	; NOTE: edit/add your keytype definitions here for $CCkey array entries 3-N
	; this UDF will itself be fixed-key encrypted

	If $updateCCkeys = True Then
		$CCkey[3] = @UserName      ; case-sensitive!
		$CCkey[4] = @ComputerName
		$CCkey[5] = @OSBuild & @OSType & @OSVersion  ; example of multiple macros in a single key
		$CCkey[6] = DriveGetSerial("C:")  ; see also _WinAPI_UniqueHardwareID()
		$CCkey[7] = @IPAddress1  ; NB ensure your IP is fixed in this case (no DHCP)
		$CCKey[8] = _DomainComputerBelongs()
		; ...
		; Add your own definitions here
		; You can also add calls to your own functions (NB their definitions HAVE to be placed ABOVE _MCFCC_Init(), but within #region Encryption2!)
	EndIf

	If StringStripWS($type, 8) = "" Or $type = Default Then $type = 1
	$type = Number($type)
	If $type < 0 Or $type >= UBound($CCkey) Then $type = 1 ; pwd query

	If $cmdline[0] > 0 Then $CCkey[1] = $cmdline[1]                                    ; option to parse password at commandline (NOT RECOMMENDED! commandline can be retrieved externally while programme is running)
	If ($CCkey[$type] == $dummypwd Or $CCkey[$type] = "" Or _
			$CCkey[$type] = Null) And $query = True Then $CCkey[$type] = _
			InputBox("Protected Application", "Please Enter Password: ", "", "*M", 250, 140, Default, Default, 15) ; last parameter defines timeout in sec for typing the password; edit as needed
	$CCkeytype = $type

	Switch $useCNG
		Case True
			For $cc = 1 To UBound($CCkey) - 1 ; skip entry 0 (already done)
				If IsPtr($CCkeyhandle[$cc]) Then __CryptoNG_BcryptDestroyKey($CCkeyhandle[$cc])
				If $CCkey[$cc] <> Null Then $CCkeyhandle[$cc] = _CryptoNGgetHandle($cc)
			Next

		Case Else
			_AES_Startup()                                                        ; IMPORTANT: if this calls fails, it is likely that AES.au3 was not found in the local directory (so move it there, or edit the path in the #include directive above)
	EndSwitch

EndFunc   ;==>_MCFCC_Init
; this func def should be the last one within this region,
;	as it is used as its end marker (so do NOT rename it)
#EndRegion Encryption2 (to be fixed-key encrypted)
;_____________________________________________________________________________________
; Anything below this region will be encrypted using your selected keytype(s).
;
; WARNING: do NOT place any key-generating UDFs below MCFCC_Init(); this won't work!
; Instead, please insert them anywhere within #Region Encryption2 ABOVE MCFCC_Init().
;
; Theoretically, it is possible for an attacker to discover HOW you *define* your key,
; but NOT its contents, unless they have full access to your target user environment.
; For example, by decrypting MCFCC_Init and the outer layer of an encrypted call,
; they could discover that you chose to use the serial number of the host's C: drive,
; but unless they have access to that machine to obtain that serial number,
; the contents of your script would remain secure.
;_____________________________________________________________________________________

  
```
