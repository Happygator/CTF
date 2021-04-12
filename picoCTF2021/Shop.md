> Best Stuff - Cheap Stuff, Buy Buy Buy...  
> Store Instance: source.  
> The shop is open for business at nc mercury.picoctf.net 42159.
When you connect to the shop over netcat, it shows this:
```
Welcome to the market!
=====================
You have 40 coins
	Item		Price	Count
(0) Quiet Quiches	10	12
(1) Average Apple	15	8
(2) Fruitful Flag	100	1
(3) Sell an Item
(4) Exit
```
Evidently, we need to acquire 60 more coins so we can buy the flag.  
We can do so by buying negative amounts of items, as there is no check here to prevent this.  
```
Welcome to the market!
=====================
You have 40 coins
	Item		Price	Count
(0) Quiet Quiches	10	12
(1) Average Apple	15	8
(2) Fruitful Flag	100	1
(3) Sell an Item
(4) Exit
Choose an option: 
0
How many do you want to buy?
-9
You have 130 coins
	Item		Price	Count
(0) Quiet Quiches	10	21
(1) Average Apple	15	8
(2) Fruitful Flag	100	1
(3) Sell an Item
(4) Exit
```
Once we do so, we can simply buy the flag.  
The flag comes in a series of decimal numbers, so we can simply convert this to ascii.  
Flag: `picoCTF{b4d_brogrammer_797b292c}`
Flag: ``
