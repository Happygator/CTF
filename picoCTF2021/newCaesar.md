
> We found a brand new type of encryption, can you break the secret code? (Wrap with picoCTF{})
> kjlijdliljhdjdhfkfkhhjkkhhkihlhnhghekfhmhjhkhfhekfkkkjkghghjhlhghmhhhfkikfkfhm  
> [new_caesar.py](https://mercury.picoctf.net/static/d9c139d91d2dfec8fd197ca6d970381a/new_caesar.py)
> 
The jumble of letters we're given is likely the ciphertext, meaning that we'll have to look at the python file to figure out how to decrypt it.
Here's the code we're given:  
```
import string  
LOWERCASE_OFFSET = ord("a")  
ALPHABET = string.ascii_lowercase[:16]  
def b16_encode(plain):  
  enc = ""  
	for c in plain:  
		binary = "{0:08b}".format(ord(c))  
		enc += ALPHABET[int(binary[:4], 2)]  
		enc += ALPHABET[int(binary[4:], 2)]  
	return enc  

def shift(c, k):  
	t1 = ord(c) - LOWERCASE_OFFSET  
	t2 = ord(k) - LOWERCASE_OFFSET  
	return ALPHABET[(t1 + t2) % len(ALPHABET)]  

flag = "redacted"  
key = "redacted"  
assert all([k in ALPHABET for k in key])  
assert len(key) == 1  

b16 = b16_encode(flag)  
enc = ""  
for i, c in enumerate(b16):  
	enc += shift(c, key[i % len(key)])  
print(enc)  
```
The b16_encode function appears to translate one ascii character into two letters from a to p. It accomplishes this by  
converting a character into its decimal representation in ascii, then converting that to binary and seperating the bits into two groups of 4.
Then, each group of 4 is converted back into a number, which denotes the position in the alphabet to use.  
Example: a -> 01100001 -> 0110 0001 -> 6 1 -> g b  
This reveals that the ciphertext only uses 
Afterwards, shift is called and a typical caesar cipher is performed. This actually uses Viginere cipher's algorithm for a key of length 1, but the end result is the same.  
To solve this, we can brute force decrypting the ciphertext with all 16 possible keys(a-p), as both of the functions are reversible.

