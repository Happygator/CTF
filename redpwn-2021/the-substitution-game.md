# **the-substitution-game**

  **Category:** misc

  **Description:** `nc mc.ax 31996`

  **Attached files:** [chall.py](https://github.com/Happygator/CTF/blob/master/redpwn-2021/chall.py)

The problem gives no description, but connecting with the netcat command gives us a lengthy description.  
```
Welcome to The Substitution Game!

In each level, you will enter a list of string substitutions.
For example, you may want to change every instance of 'abcd' to 'def'.

The game will provide a series of test cases.
For each case, substitutions will be applied repeatedly in a series of rounds.
In each round, the first possible substitution will be performed.
For test case of length s, there will be s ^ 2 substitution rounds.

In each round, we will show examples of intended substitution behavior.
It is your goal to match our behavior.

See next level? (y/n) 
```
To summarize, we must construct a series of rules consisting of two strings. Afterwards, the rules will be run through test cases.  
In each test case, the first rule that applies will replace the first string in the rule with the second string.  
The rules also have priority in the order you entered them in.(first rule entered has highest priority, second has second highest, etc)  
This will occur several hundred times, so no need to worry about efficiency.  
Note that some rules changing a string may cause other rules in future rounds to apply.  

## Level 1
Level 1 is a simple find-replace, nothing extra.  
```
Here is this level's intended behavior:

Initial string: initial00000000
Target string: target00000000

Initial string: 0000000initial00000
Target string: 0000000target00000

Initial string: 0000000000000initial00000
Target string: 0000000000000target00000

(it keeps going like this for 10 cases)
```
Solution: `initial => target`  
  
## Level 2

Level 2 is almost the same, except there are two rules required instead of one.  
```
Here is this level's intended behavior:

Initial string: hellohelloginkoidhellohellohellohellohellohelloginkoidhelloginkoidhellohellohello
Target string: goodbyegoodbyeginkygoodbyegoodbyegoodbyegoodbyegoodbyegoodbyeginkygoodbyeginkygoodbyegoodbyegoodbye

Initial string: ginkoidginkoidhellohelloginkoidhelloginkoidginkoidhelloginkoidhelloginkoidginkoidhelloginkoidginkoidhelloginkoid
Target string: ginkyginkygoodbyegoodbyeginkygoodbyeginkyginkygoodbyeginkygoodbyeginkyginkygoodbyeginkyginkygoodbyeginky

Initial string: ginkoidhellohelloginkoidginkoidhelloginkoidginkoidginkoidginkoidhellohelloginkoidhello
Target string: ginkygoodbyegoodbyeginkyginkygoodbyeginkyginkyginkyginkygoodbyegoodbyeginkygoodbye
(and so on)
```
This is also easily solvable by observing what turns into what. In this case, `hello` turns into `goodbye`, and `ginkoid` turns into `ginky`.  
No extra shenanigans.  
  
Solution: `hello => goodbye, ginkoid =< ginky`

## Level 3

Level 3 introduces the "multiple rounds of substitution" gimmick.  
```
Here is this level's intended behavior:

Initial string: aaaaaaaaaaaaaaaaaaaaaaaaaa
Target string: a

Initial string: aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
Target string: a

Initial string: aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
Target string: a
(and so on)
```
If we treated this like a typical find-replace, it would be impossible as we only have 10 rules for this level and there are more than 10 test cases.  
However, we can take advantage of the fact that substitution is performed repeatedly with the rule `aa => a`.  
When this rule is applied repeatedly, it will remove one a from the beginning of the string each time until there is only one.  
  
Solution: `aa => a`

## Level 4
```
Here is this level's intended behavior:

Initial string: gggggggggggggggggggggggggggggggggggggggggggggggggg
Target string: ginkoid

Initial string: gggggggggggggggggggggggggggggggggg
Target string: ginkoid

Initial string: gggggggggggggggggggggggggggggggggggggggggggggggggggggggggggggggggggggggggggggggg
Target string: ginkoid
```
This level is very similar to level 3, but if we try the same solution except with `gg => g` and `g => ginkoid` it won't work.  
While the priority of rules would cause you to eventually land on ginkoid, the second rule would then infinitely trigger because ginkoid has a g in it.  
We can sidestep this by adding a new letter(I chose a) and having each a eat other as and gs, and later have one a turn into ginkoid.  
This solution doesn't work for the test case of a singular g, but looking at the challenge code reveals that the test cases only consist of 10-100 gs.

Solution: `gg => a, ag => a, aa => a, a => ginkoid`  
a won't turn into ginkoid immediately because each substitution round only applies one rule, so as long as another rule applies(i.e. there are extra gs and as), the 4th rule won't.  

## Level 5

```
Here is this level's intended behavior:

Initial string: ^00010010001100010000111010010111111010001010101100110111001$
Target string: not_palindrome

Initial string: ^11000100001110011111111101100101101100111010000111111111000010$
Target string: not_palindrome

Initial string: ^11010011010101010101000101000010100010101010101011001011$
Target string: palindrome

Initial string: ^100010101110001000100111111111110011110011111111111001000100011101010001$
Target string: palindrome
```
This problem is only possible because of two facts.  
1. There are only two digits, 0 and 1.
2. We are guaranteed to know where the start and end of the string are, as they are marked with ^ and $.
Here's what I came up with:
```
h$ => palindrome 
^ => hs  
0n => n,  1n => n  
1s1$ => $,  0s0$ => $,  1s0$ => n,  0s1$ => n,  
s00 => 0s0,  s01 => 1s0,  s10 => 0s1,  s11 => 1s1, 
hs1$ => palindrome,  hs0$ => palindrome,  
hn => not_palindrome,  
h => ^ 
```
Here's what the program does:  
Supposed the input string is `^010010$`.  
`^ => hs` will apply, changing the string to `^h s0 10010$`(spaaces added for clarity). H stands for head(of the string), and s is our "cursor" which will carry digits.  
`s01 => 1s0` will apply, changing the string to `^h1 s0 0010$`. This effectively moves s0 one place to the right.  
`s00 => 0s0` will apply, changing the string to `^h10 s0 010$`. This process will continue until there are no digits to the right of s0, resulting in `^h10010s0$`.  
`0s0$ => $` will apply, erasing s and the two zeroes because they are the same. This leaves us with `h1001$`.  
`h => ^` will apply, leaving us with the initial string but with the first and last digits erased.  
This process will continue until either the first and last digits are different(at which an n will be inserted and erase everything) or there are 0 or 1 digits left.  
If the first and last digits are ever different, an n will replace the $ at the end of the string. This n will eat all other 0s and 1s and leave us with hn, which will turn into `not palindrome`.  
if there are 0 or 1 digits left, the string is guaranteed to be one of `hs1$`, `hs0$`, and `h$` at the end. This will replace it with `palindrome`.  
## Level 6

In level 6, we have to do binary addition and verify whether the equation is correct or not.
```
Here is this level's intended behavior:

Initial string: ^100111+1100100=111011110$
Target string: incorrect

Initial string: ^100111+1100100=111011110$
Target string: incorrect

Initial string: ^1110111+11101001=101100000$
Target string: correct
```
Here's my solution:
```
#n erodes everything
0n => n, 1n => n, +n => n, =n => n, 
n0 => n, n1 => n, n+ => n, n= => n, 
n$ => n, nt => n, qn => n, nq => n, 

#prints out the result
^n => incorrect, ^+=$ => correct, 

#check if digits are equal
0t0$ => $, 1t1$ => $, 1t0$ => n, 0t1$ => n, =t0$ => =$, 

=0 => =, 

#ending cases
^+=t1$ +> ^n, ^+=t0$ => +=$, ^+=1 => ^n, +=t1$ => n,

#move s
s00 => 0s0, s01 => 1s0, s10 => 0s1, s11 => 1s1, +s0= => +=s0, +s1= => +=s1, 
# calculates t(the sum)
0s0= => =t0, 1s0= => =t1, 0s1= => =t1, 1s1= => q=t0, 
0s0q= => =t1, 1s0q= => q=t0, 0s1q= => q=t0, 1s1q= => q=t1,  
+s0q= => +=t1, +s1q= => +q=t0, 
#move t
t00 => 0t0, t01 => 1t0, t10 => 0t1, t11 => 1t1,

#skip to t if only first number has digits
0+= => +=t0, 1+= => +=t1, 0+q= => +=t1, 1+q= => +q=t0, 

#skip to t if second number only has digits
^+ => ^+s0, 

#carry functions as a 1 if no digits are left
^+q= => ^+=t1, 

#create the initial s
0+ => +s0, 1+ => +s1
```

This program uses the same idea of a letter cursor. It's process can be turned into a bulletpoint list:  
1. Remove all extraneous zeroes from the ending result.
2. Put an s(your cursor) behind the last digit of the first number.  
  a. If the first number contains no digits, treat the last digit as 0.  
  b. If the second number contains no digits(+ is directly next to =), move the last digit of the first number ahead of the equals and put a t behind it. Goto 6.  
  c. If both operands contain no digits(string contains ^+=) and there is a carry left, change it to a 1. Place a t before it and move it just after the equals sign. Goto 6.  
  d. If both operands contain no digits and there are no carries left, goto 8a.
3. Move the s and its digit to the end of the second number until there is only one digit between s and the = sign.
4. Compute the sum of the number behind the s(last digit of second number) and the number in front of s. Account for carries(add 1 if there is a q between s and =) and place a q(for carry, because incorrect contained a c in it) between s and = if required.
5. Move the sum past the equals sign and change the cursor from s to t.  
6. Move t and its digit to the end of the string, right before $.  
7. If the two digits surrounding t are the same, delete both digits and t.  
  a. Otherwise, put an n there. n eats everything else in the equation except ^.
8. If the equation is just ^n, replace it with `incorrect.`
  a. If the equation contains no numbers and no carries but a 1 in the result, replace it with an n(which will later be replaced with incorrect).  
  b. If no digits are left in any number, replace the entire equation with `correct`.  
  c. If none of these conditions apply, goto 2.  

Note that the solutions for levels 5 and 6 take lots of time to enter, so I used pwntools to enter it for me.
```
from pwn import *
from time import *
conn = remote("mc.ax", 31996)
responses = [
"y", 
"initial => target", "n", "y",
"hello => goodbye", "y", "ginkoid => ginky", "n", "y",
"aa => a", "n", "y",
"gg => a", "y", "ag => a", "y", "aa => a", "y", "a => ginkoid", "n", "y"
]
level5 = ["h$ => palindrome", "y", "^ => hs", "y", "0n => n", "y", "1n => n", "y", "1s1$ => $", "y", "0s0$ => $", "y", "1s0$ => n", "y", "0s1$ => n", "y", "s00 => 0s0", "y", "s01 => 1s0", "y", "s10 => 0s1", "y", "s11 => 1s1", "y", "hs1$ => palindrome", "y", "hs0$ => palindrome", "y", "hn => not_palindrome", "y", "h => ^", "y", "nn => n", "n", "y"]
level6 = [
"0n => n", "1n => n", "+n => n", "=n => n", 
"n0 => n", "n1 => n", "n+ => n", "n= => n", 
"n$ => n", "nt => n", "qn => n", "nq => n", 
"^n => incorrect", "^+=$ => correct", 
"0t0$ => $", "1t1$ => $", "1t0$ => n", "0t1$ => n", "=t0$ => =$", 
"=0 => =", 
"^+=t1$ +> ^n", "^+=t0$ => +=$", "^+=1 => ^n", "+=t1$ => n",
"s00 => 0s0", "s01 => 1s0", "s10 => 0s1", "s11 => 1s1", "+s0= => +=s0", "+s1= => +=s1", 
"0s0= => =t0", "1s0= => =t1", "0s1= => =t1", "1s1= => q=t0", 
"0s0q= => =t1", "1s0q= => q=t0", "0s1q= => q=t0", "1s1q= => q=t1",  
"+s0q= => +=t1", "+s1q= => +q=t0", 
"t00 => 0t0", "t01 => 1t0", "t10 => 0t1", "t11 => 1t1",
"0+= => +=t0", "1+= => +=t1", "0+q= => +=t1", "1+q= => +q=t0", 
"^+ => ^+s0", 
"^+q= => ^+=t1", 
"0+ => +s0", "1+ => +s1" 
]
for i in responses:
	conn.sendline(i)
for i in level5:
	conn.sendline(i)
for j in range(1, 8):
	print(j)
	sleep(1)
for i in level6:
	conn.sendline(i)
	if i != "1+ => +s1":
		conn.sendline("y")
	else:
		conn.sendline("n")
while True:
	print(str(conn.recv(), 'utf-8'))
```
(7 second wait is requried because it takes a while to test level 5)  
  
flag: `flag{wtf_tur1n9_c0mpl3t3}`
