# **the-substitution-game**

  **Category:** misc

  **Description:** `nc mc.ax 31996`


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
This solution doesn't work for the test case of a singular g, but 
