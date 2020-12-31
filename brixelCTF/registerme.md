>This program needs to be activated  
>Can you figure out how to do it?  
[registerme.exe](https://ctf.brixel.space/files/f36172553301b9fe376a4413e608399f/registerme.exe?token=eyJ1c2VyX2lkIjo1MDcsInRlYW1faWQiOjMyNSwiZmlsZV9pZCI6MjF9.X-4whA.q_eru0qH29g_cZLd1dkyiEecCyw)  

When opening up the file, we're shown a small black window telling you the program isn't registered:
![test](https://github.com/Happygator/CTF/blob/master/brixelCTF/NotRegistered.PNG)  
We'll need to find out exactly what the program is doing when we run it, and more specifically what criteria makes the program registered.  
Fortunately, there is a program called [Process Monitor](https://docs.microsoft.com/en-us/sysinternals/downloads/procmon) which does exactly that.  
Booting up Process Monitor and running it we get a barrage of notifications from other programs because they also perform actions:
![kevin](https://github.com/Happygator/CTF/blob/master/brixelCTF/Unfiltered.PNG)  
We need to filter the results to only show the program we want and its actions:  
![kevin](https://github.com/Happygator/CTF/blob/master/brixelCTF/thefilter.PNG)  
Once we filter the results, we notice that the program makes many Query Directory actions, looking for a file called `activation.key`:
![kevin](https://github.com/Happygator/CTF/blob/master/brixelCTF/Queries.PNG)  
It keeps returning `NO SUCH FILE` when queried, so let's make a file called activation.key and put it in the same directory as the program:
![kevin](https://github.com/Happygator/CTF/blob/master/brixelCTF/withkey.PNG)  
Now, when the program is run it believes it's been registered, and hands us a "Show Flag" Button.  
![kevin](https://github.com/Happygator/CTF/blob/master/brixelCTF/registered.PNG)  
Flag: `brixelCTF{f1l34cc3ss}`
