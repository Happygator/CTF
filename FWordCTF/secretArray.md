`nc secretarray.fword.wtf 1337`  
Upon running this netcat command, we're greeted with:
```
I have a 1337 long array of secret positive integers. The only information I can provide is the sum of two elements. You can ask for that sum up to 1337 times by specifing two different indices in the array.

[!] - Your request should be in this format : "i j". In this case, I'll respond by arr[i]+arr[j]

[!] - Once you figure out my secret array, you should send a request in this format: "DONE arr[0] arr[1] ... arr[1336]"

[*] - Note 1: If you guessed my array before 1337 requests, you can directly send your DONE request.
[*] - Note 2: The DONE request doesn't count in the 1337 requests you are permitted to do.
[*] - Note 3: Once you submit a DONE request, the program will verify your array, give you the flag if it's a correct guess, then automatically exit.

START:
```  
As the problem says, we can get the sums of two different numbers in the array and we habve 1337 guesses to get all 1337 numbers from the array.  
If we query 0 1, 1 2, and 0 2 then we can find out all three of the numbers:  
(arr[0] + arr[1]) + (arr[1] + arr[2]) + (arr[0] + arr[2])  
= 2 * (arr[0]) + arr[1] + arr[2])  
Divide by two to get the sum of all three and then subtract each pair sum to get arr[0], arr[1], arr[2].  
Next, we can query 0 3, 0 4, and so on up to 0 1336 because we know arr[0].  
Program:  
```
from pwn import *

def get_int(line):
  #print(line)
  line = line.decode("utf-8") 
  return int(line)
conn = remote('secretarray.fword.wtf', 1337)
conn.recvuntil('START:')

conn.send(b'0 1\n')
conn.recvline()
sum01= get_int(conn.recvline())
print('sum01', sum01)

conn.send(b'0 2\n')
#conn.recvline()
sum02= get_int(conn.recvline())
print('sum02', sum02)

conn.send(b'1 2\n')
sum12= get_int(conn.recvline())
print('sum12', sum12)

v=[0]* 1337
v[0] = (sum01 + sum02 - sum12) //2
v[1] = sum01 - v[0]
v[2] = sum02 - v[0]
print(0, v[0])
print(1, v[1])
print(2, v[2])

for i in range(3, 1337):
  #print(b'0 '+  str.encode(str(i)) + b'\n')
  conn.send(b'0 '+  str.encode(str(i)) + b'\n')
  #conn.recvline()
  sum= get_int(conn.recvline())
  v[i] = sum - v[0]
  print(i, v[i])

result = b'DONE'
for i in range(1337):
  result += b' ' + str.encode(str(v[i]))

print(result)
conn.send(result + b'\n')
print(conn.recvline())
print(conn.recvline())
```

Flag: `FwordCTF{it_s_all_about_the_math}`
