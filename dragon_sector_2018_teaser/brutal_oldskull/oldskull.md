## Brutal Oldskull

### Solution
Being a noob, I spent a lot of time reversing the entire key check algorithm before it dawned on me that I could simply use the program against itself to brute-force the keys. 


### Brute-forcing code input [1-4]

Each of the Code [1-4] inputs require a 16-bit value to be entered. Since the code inputs are being checked in succession, it would only require a maximum of 4 * 0x10000 tests in the worst-case scenario. 

This is the code checking input code 2 (the code check sections are more or less the same for the 4 inputs)

![](https://github.com/thag8keepr/ctf/blob/master/dragon_sector_2018_teaser/brutal_oldskull/1.png)

I patched the code like so:

![](https://github.com/thag8keepr/ctf/blob/master/dragon_sector_2018_teaser/brutal_oldskull/2.png)

EDI is used as an index which is cleared on entry, and incremented by one at each iteration and copied to EAX, which in turn is feed into the check function. At some point EDI will contain the correct value and the program execution will continue to check the next code input. I put a breakpoint at each of these branches to intercept the value of EDI and thereby the value of code [1-4]. 

### Resulting input codes
```
Code 1: 0x5B42 Code 2: 0x13CC Code 3: 0xF129 Code 4: 0x62AC
```


Entering the 4 codes as shown below gets us a bit further: “Wrong Flag”. So, input codes are correct, but apparently, we need to enter something in the “Final Flag” input box – supposedly it should be in the DrgnS{xxx} format. 

![](https://github.com/thag8keepr/ctf/blob/master/dragon_sector_2018_teaser/brutal_oldskull/3.png)

Back to the disassembly. What really happens when we enter the input codes [1-4]? In short it decrypts a blob from the binary using the 4 input codes in succession:

![](https://github.com/thag8keepr/ctf/blob/master/dragon_sector_2018_teaser/brutal_oldskull/4.png)

The decrypted blob is written to a file called **“oldskull_checker.exe”** in your temp directory and is called with the value from the “Final Flag” input text box as argument. If the return value from this call is correct the “Well done” message is shown. 

OK, let’s fire up the “oldskull_checker.exe” in IDA. We find this piece of code:


![](https://github.com/thag8keepr/ctf/blob/master/dragon_sector_2018_teaser/brutal_oldskull/5.png)

The code checks that the length of our input is 0x14, and then runs through a loop which compares each char of the flag to be tested with some hardcoded bytes from the binary xor’ed with 0x8F.  

![](https://github.com/thag8keepr/ctf/blob/master/dragon_sector_2018_teaser/brutal_oldskull/6.png)

Extract the bytes: 
**CBFDE8E1DCF4D8EEEEEEF6DBE0E0CAD5AEAEBEF2**

Xor’ed with 0x8F:
**4472676e537b5761616179546f6f455a2121317d**

Hex to ascii:
**DrgnS{WaaayTooEZ!!1}**

Final test:

![](https://github.com/thag8keepr/ctf/blob/master/dragon_sector_2018_teaser/brutal_oldskull/7.png)


### Flag
```
DrgnS{WaaayTooEZ!!1}
```