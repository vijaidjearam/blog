---
layout: post
date: 2023-03-06 09:52:36
title: AutoIT Encrypt Script
category: chocolatey
tags: automation chocolatey package deployment
---
# AutoIT Encrypt Script

Source: https://www.autoitscript.com/forum/files/file/491-codescannercrypterbundle/


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
• Under Tab Main, disable option [Create and enable option [ Encrypt]; then Press [Run]
• A new encrypted file MCF0test.au3 is created in your target’s home directory.
• Open it in Scite , check for errors, do test runs. Ensure that it works exactly as the original.
  
![image](https://user-images.githubusercontent.com/1507737/224680085-6e7abb4d-1e98-4ff9-8d5d-3704dfe40edc.png)
  
![image](https://user-images.githubusercontent.com/1507737/224680178-d41e6f19-e5a0-424d-8fbb-3e243affdfee.png)

