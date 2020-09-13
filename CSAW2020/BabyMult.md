```
Welcome to reversing! Prove your worth and get the flag from this neat little program!
```
  
The file given is a bunch of numbers. Translate the numbers into binary and then into assembly code using [cyberchef](https://gchq.github.io/CyberChef/#recipe=From_Decimal(%27Comma%27,false)To_Hex(%27Space%27,0)Disassemble_x86(%2764%27,%27Full%20x86%20architecture%27,16,0,true,true)&input=ODUsNzIsMTM3LDIyOSw3MiwxMzEsMjM2LDI0LDcyLDE5OSw2OSwyNDgsNzksMCwwLDAsNzIsMTg0LDIxLDc5LDIzMSw3NSwxLDAsMCwwLDcyLDEzNyw2OSwyNDAsNzIsMTk5LDY5LDIzMiw0LDAsMCwwLDcyLDE5OSw2OSwyMjQsMywwLDAsMCw3MiwxOTksNjksMjE2LDE5LDAsMCwwLDcyLDE5OSw2OSwyMDgsMjEsMSwwLDAsNzIsMTg0LDk3LDkxLDEwMCw3NSwyMDcsMTE5LDAsMCw3MiwxMzcsNjksMjAwLDcyLDE5OSw2OSwxOTIsMiwwLDAsMCw3MiwxOTksNjksMTg0LDE3LDAsMCwwLDcyLDE5OSw2OSwxNzYsMTkzLDMzLDAsMCw3MiwxOTksNjksMTY4LDIzMywxMDEsMzQsMjQsNzIsMTk5LDY5LDE2MCw1MSw4LDAsMCw3MiwxOTksNjksMTUyLDE3MSwxMCwwLDAsNzIsMTk5LDY5LDE0NCwxNzMsMTcwLDE0MSwwLDcyLDEzOSw2OSwyNDgsNzIsMTUsMTc1LDY5LDI0MCw3MiwxMzcsNjksMTM2LDcyLDEzOSw2OSwyMzIsNzIsMTUsMTc1LDY5LDIyNCw3MiwxNSwxNzUsNjksMjE2LDcyLDE1LDE3NSw2OSwyMDgsNzIsMTUsMTc1LDY5LDIwMCw3MiwxMzcsNjksMTI4LDcyLDEzOSw2OSwxOTIsNzIsMTUsMTc1LDY5LDE4NCw3MiwxNSwxNzUsNjksMTc2LDcyLDE1LDE3NSw2OSwxNjgsNzIsMTM3LDEzMywxMjAsMjU1LDI1NSwyNTUsNzIsMTM5LDY5LDE2MCw3MiwxNSwxNzUsNjksMTUyLDcyLDE1LDE3NSw2OSwxNDQsNzIsMTM3LDEzMywxMTIsMjU1LDI1NSwyNTUsMTg0LDAsMCwwLDAsMjAxCg). This yields:  
```
00 55                              PUSH RBP
01 4889E5                          MOV RBP,RSP
04 4883EC18                        SUB RSP,18
08 48C745F84F000000                MOV QWORD PTR [RBP-08],0000004F
10 48B8154FE74B01000000            MOV RAX,000000014BE74F15
1A 488945F0                        MOV QWORD PTR [RBP-10],RAX
1E 48C745E804000000                MOV QWORD PTR [RBP-18],00000004
26 48C745E003000000                MOV QWORD PTR [RBP-20],00000003
2E 48C745D813000000                MOV QWORD PTR [RBP-28],00000013
36 48C745D015010000                MOV QWORD PTR [RBP-30],00000115
3E 48B8615B644BCF770000            MOV RAX,000077CF4B645B61
48 488945C8                        MOV QWORD PTR [RBP-38],RAX
4C 48C745C002000000                MOV QWORD PTR [RBP-40],00000002
54 48C745B811000000                MOV QWORD PTR [RBP-48],00000011
5C 48C745B0C1210000                MOV QWORD PTR [RBP-50],000021C1
64 48C745A8E9652218                MOV QWORD PTR [RBP-58],182265E9
6C 48C745A033080000                MOV QWORD PTR [RBP-60],00000833
74 48C74598AB0A0000                MOV QWORD PTR [RBP-68],00000AAB
7C 48C74590ADAA8D00                MOV QWORD PTR [RBP-70],008DAAAD
84 488B45F8                        MOV RAX,QWORD PTR [RBP-08]
88 480FAF45F0                      IMUL RAX,QWORD PTR [RBP-10]
8D 48894588                        MOV QWORD PTR [RBP-78],RAX
91 488B45E8                        MOV RAX,QWORD PTR [RBP-18]
95 480FAF45E0                      IMUL RAX,QWORD PTR [RBP-20]
9A 480FAF45D8                      IMUL RAX,QWORD PTR [RBP-28]
9F 480FAF45D0                      IMUL RAX,QWORD PTR [RBP-30]
A4 480FAF45C8                      IMUL RAX,QWORD PTR [RBP-38]
A9 48894580                        MOV QWORD PTR [RBP-80],RAX
AD 488B45C0                        MOV RAX,QWORD PTR [RBP-40]
B1 480FAF45B8                      IMUL RAX,QWORD PTR [RBP-48]
B6 480FAF45B0                      IMUL RAX,QWORD PTR [RBP-50]
BB 480FAF45A8                      IMUL RAX,QWORD PTR [RBP-58]
C0 48898578FFFFFF                  MOV QWORD PTR [RBP-00000088],RAX
C7 488B45A0                        MOV RAX,QWORD PTR [RBP-60]
CB 480FAF4598                      IMUL RAX,QWORD PTR [RBP-68]
D0 480FAF4590                      IMUL RAX,QWORD PTR [RBP-70]
D5 48898570FFFFFF                  MOV QWORD PTR [RBP-00000090],RAX
DC B800000000                      MOV EAX,00000000
E1 C9                              LEAVE
```  
This program creates a bunch of variables through multiplying large numbers. 
```
08 48C745F84F000000                MOV QWORD PTR [RBP-08],0000004F     ;var8 = 0x4F
10 48B8154FE74B01000000            MOV RAX,000000014BE74F15            ;RAX = 0x000000014BE74F15
1A 488945F0                        MOV QWORD PTR [RBP-10],RAX          ;var10 = 0x000000014BE74F15     Note that these addresses are in hex, 
1E 48C745E804000000                MOV QWORD PTR [RBP-18],00000004     ;var18 = 0x4                    so it increases by 8 each time.
26 48C745E003000000                MOV QWORD PTR [RBP-20],00000003     ;var20 = 0x3
2E 48C745D813000000                MOV QWORD PTR [RBP-28],00000013     ;var28 = 0x13
36 48C745D015010000                MOV QWORD PTR [RBP-30],00000115     ;var30 = 0x115
3E 48B8615B644BCF770000            MOV RAX,000077CF4B645B61            ;RAX = 000077CF4B645B61
48 488945C8                        MOV QWORD PTR [RBP-38],RAX          ;var38 = RAX
4C 48C745C002000000                MOV QWORD PTR [RBP-40],00000002     ;var40 = 0x2
54 48C745B811000000                MOV QWORD PTR [RBP-48],00000011     ;var48 = 0x11
5C 48C745B0C1210000                MOV QWORD PTR [RBP-50],000021C1     ;var50 = 0x21C1
64 48C745A8E9652218                MOV QWORD PTR [RBP-58],182265E9     ;var58 = 0x182265E9
6C 48C745A033080000                MOV QWORD PTR [RBP-60],00000833     ;var60 = 0x833
74 48C74598AB0A0000                MOV QWORD PTR [RBP-68],00000AAB     ;var68 = 0xAAB
7C 48C74590ADAA8D00                MOV QWORD PTR [RBP-70],008DAAAD     ;var70 = 0x8DAAAD
84 488B45F8                        MOV RAX,QWORD PTR [RBP-08]          ;RAX = 0x4F
88 480FAF45F0                      IMUL RAX,QWORD PTR [RBP-10]         ;RAX *= var10
8D 48894588                        MOV QWORD PTR [RBP-78],RAX          ;var78 = RAX
91 488B45E8                        MOV RAX,QWORD PTR [RBP-18]          ;RAX = var18
95 480FAF45E0                      IMUL RAX,QWORD PTR [RBP-20]
9A 480FAF45D8                      IMUL RAX,QWORD PTR [RBP-28]
9F 480FAF45D0                      IMUL RAX,QWORD PTR [RBP-30]
A4 480FAF45C8                      IMUL RAX,QWORD PTR [RBP-38]         ;RAX *= var20 * var28 * var30 * var38         
A9 48894580                        MOV QWORD PTR [RBP-80],RAX          ;var80 = RAX
AD 488B45C0                        MOV RAX,QWORD PTR [RBP-40]          ;RAX = var40
B1 480FAF45B8                      IMUL RAX,QWORD PTR [RBP-48]
B6 480FAF45B0                      IMUL RAX,QWORD PTR [RBP-50]
BB 480FAF45A8                      IMUL RAX,QWORD PTR [RBP-58]         ;RAX *= var48 * var50 * var58
C0 48898578FFFFFF                  MOV QWORD PTR [RBP-88],RAX          ;var88 = RAX
C7 488B45A0                        MOV RAX,QWORD PTR [RBP-60]          ;RAX = var60
CB 480FAF4598                      IMUL RAX,QWORD PTR [RBP-68]
D0 480FAF4590                      IMUL RAX,QWORD PTR [RBP-70]         ;RAX *= var68 * var70
D5 48898570FFFFFF                  MOV QWORD PTR [RBP-90],RAX          ;var90 = RAX
DC B800000000                      MOV EAX,00000000
E1 C9                              LEAVE
```  
If we take the values of all four variables and [convert them into ASCII](https://gchq.github.io/CyberChef/#recipe=From_Hex('Auto')&input=MHg2NjZjNjE2NzdiCjB4NzM3NTcwMzM3MjVmNzYzNAoweDZjMzE2NDVmNzA3MgoweDMwNjc3MjM0NmQ3ZCc), we get the flag.
```
var78 = 0x666c61677b
var80 = 0x73757033725f7634
var88 = 0x6c31645f7072
var90 = 0x306772346d7d
```
  
Flag: `flag{sup3r_v4l1d_pr0gr4m}`
