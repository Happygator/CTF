> Another slight twist on a classic, see if you can recover the flag. 
> (Wrap with picoCTF{}) epdfglkfnbjbhbpicohidjgkhfnejeecmjfnejddgmhpndmchbmifnepdhdmhbah 
[new_vignere.py](https://mercury.picoctf.net/static/cf0a2b589b6e55cfc5b108b3bdeb2647/new_vignere.py)
As the description suggests, this problem is New Caesar but Vignere is applied. Here's the contents of the file:
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
assert all([c in "abcdef0123456789" for c in flag])

key = "redacted"
assert all([k in ALPHABET for k in key]) and len(key) < 15

b16 = b16_encode(flag)
enc = ""
for i, c in enumerate(b16):
	enc += shift(c, key[i % len(key)])
print(enc)
```  
Note that the flags typically only contain lowercase letters, numbers, and underscores, like in New Caesar.  
Every odd-positioned letter represents the first four (and most significant) binary digits.  
Combined with the limited input range, this means that every other letter must have originally been 0011(d), 0101(f), or 0110(g).
As such, we can find potential key letters for every other letter by subtracting the ciphertext letter's position in the alphabet(from a to p) from the possible plaintext's position.  
```
Plaintext at top(every other letter), possible key letters at bottom
e d g k n j h p c h d g h n j e m f e d g h n m h m f e d d h a  
  
b a d h k g e m p e a d e k g b j c b a d e k j e j c b a a e n  
o n a e h d b j m b n a b h d o g p o n a b h g b g p o n n b k  
n m p d g c a i l a m p a g c n f o n m p a g f a f o n m m a j  
```  
To actually figure out what the key is, we need to find the key length. To do so, we can use a modified [Kasiski examination](https://en.wikipedia.org/wiki/Vigen%C3%A8re_cipher#Cryptanalysis) and look for repeated jumbles of letters.  
After some searching, we can find tghe letters `fn` repeated four times in the (relatively small) ciphertext.
`epdfglk(fn)bjbhbpicohidjgkh(fn)ejeecmj(fn)ejddgmhpndmchbmi(fn)epdhdmhbah`  
These pairs of letters happen to be always either 9 or 18 letters apart from each other, implying that the key is 3 or 9 letters long. We can test both of these with the chart we found above by looking at which character of the key encrypts which character of the plaintext and then comparing them.  
With a 3-length key:
```
Table above, but with character of key used to encode it(1 means first character was used, 2 means second character was used, etc)
e d g k n j h p c h d g h n j e m f e d g h n m h m f e d d h a 

1 3 2 1 3 2 1 3 2 1 3 2 1 3 2 1 3 2 1 3 2 1 3 2 1 3 2 1 3 2 1 3

b a d h k g e m p e a d e k g b j c b a d e k j e j c b a a e n 
o n a e h d b j m b n a b h d o g p o n a b h g b g p o n n b k 
n m p d g c a i l a m p a g c n f o n m p a g f a f o n m m a j
```
However, this comes up with issues because the first character of the key can't be one of b, o, n and also be one of h, e, d. This means that the key is likely length 9.  
With a 9-length key:
```
e d g k n j h p c h d g h n j e m f e d g h n m h m f e d d h a 

1 3 5 7 9 2 4 6 8 1 3 5 7 9 2 4 6 8 1 3 5 7 9 2 4 6 8 1 3 5 7 9

b a d h k g e m p e a d e k g b j c b a d e k j e j c b a a e n 
o n a e h d b j m b n a b h d o g p o n a b h g b g p o n n b k 
n m p d g c a i l a m p a g c n f o n m p a g f a f o n m m a j 
```
This does work and allows us to get the key, or 3 possible keys in this case because the third letter is always either a, n, or m.  
Our three possible keys are bgabajepk, bgnbajepk, and bgmbajepk. Finally, we simply attempt to decrypt the key with all three of the keys.
`Key bgabajepk -> djdegcggdadbgbgedegcdigbdgdddedcdfgddddcgddadcgcgbdegddjdgdddcgg -> 94bf01ad4b8a63425c32c02ba4c9632f`
This flag is the correct answer, and so we do not need to try the other two:  
Flag: `94bf01ad4b8a63425c32c02ba4c9632f>>>`

