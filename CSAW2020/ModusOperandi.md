`nc crypto.chal.csaw.io 5001`
Upon running the command, we get a prompt asking for a plaintext. When we enter anything in, it spews out a string of hex numbers and asks us "ECB or CBC?"  
This refers to different modes of operation for AES cipher. Each time you connect, it will encrypt your plaintext with a random key and mode of operation.  
You must guess which mode is used many times in a row.  
  
The key to this problem is that ECB and CBC have distinct properties that easily distinguish themselves.  
If you feed the same character hundreds of times into ECB, you will get a repeating pattern.  
If you feed the same character into CBC, you won't.  
With this knowledge, you can now guess each one correctly by just entering hundreds of the same character as the plaintext each time.  
Then, take each ECB answer you wrote as a 0 and each CBC answer as a 1. Concatenate them and translate into binary to find the flag.  
Program:  
```
from pwn import *

oper= ''
io = remote("crypto.chal.csaw.io", 5001)

print(io.recvline())

line = io.recvline()
i=0
try:
  while line.startswith(b'Enter'):
   io.send(b'U'*500 +b'\n')
   cipher = io.recvline().strip().split(b' ')[-1]
   #print(b'cipher is ' + cipher)
   ans = b'ECB\n' if len(cipher.split(cipher[:8]))>2 else b'CBC\n'
   oper += '0' if ans == b'ECB\n' else '1'
   flag=''.join([chr(int(x,2)) for x in [oper[i:i+8] for i in range(0,len(oper),8)]])
   if len(oper) %8 == 0:
     print('flag= ' + flag)
   line = io.recvline()
   io.send(ans)
   if i==175:
     break
     
   line = io.recvline()
   i+=1
except:
  pass
print('finished')
```

Flag: `flag{ECB_re@lly_sUck$}`
