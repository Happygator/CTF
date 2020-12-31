>This program needs to be activated  
>Can you figure out how to do it?  
[registerme.exe](https://ctf.brixel.space/files/f36172553301b9fe376a4413e608399f/registerme.exe?token=eyJ1c2VyX2lkIjo1MDcsInRlYW1faWQiOjMyNSwiZmlsZV9pZCI6MjF9.X-4whA.q_eru0qH29g_cZLd1dkyiEecCyw)  

When opening up the file, we're shown a small black window telling you the program isn't registered:
![test](https://github.com/Happygator/CTF/blob/master/brixelCTF/NotRegistered.PNG)  
We'll need to find out exactly what the program is doing when we run it, and more specifically what criteria makes the program registered.  
Fortunately, there is a program called [Process Monitor](https://docs.microsoft.com/en-us/sysinternals/downloads/procmon) which does exactly that.  
Booting up Process Monitor and running it we get a barrage of notifications from other programs because they also perform actions:
![kevin]()
