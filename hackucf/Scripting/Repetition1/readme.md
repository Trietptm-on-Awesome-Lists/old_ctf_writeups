To start off let’s try to connect to the challenge and see what happens. To do so, just use netcat. You can type in the following command to do so.

```
nc ctf.hackucf.org 10101
```

 After we try that we are prompted to essentially repeat a value, several times. We will probably have to do this hundreds of times, so we can script that out. I scripted it out using python, and a library for python made for ctf challenges called pwntools. Pwntools will make setting up the connection and dealing with that connection much easier (Pwntools can do a lot more, however we don’t need that now). So essentially what the script does is it set’s up the connection, takes the text from the challenge, filters out the value and sends it to the server, and then repeats untill it get’s a flag. Refer to the code below for specifics, and it’s commented so it should answer any questions you have about how to code this.


```
#This imports pwntool which will help us make connections
from pwn import *


#This sets up the remote connection to the challenge
conn = remote('ctf.hackucf.org', 10101)


#This prints out the first line from the challenge
print conn.recvline()


#This sets up a string which we will use in the while loop
a = ""


#This prints out the second line from the challenge
print conn.recvline()


#This establishes the loop which will solve the challenge
while True:
    #This takes the input from the challenge and stores it in a variable
    a = conn.recvline()
    #This filters out the extra stuff from string
    a = a.replace("Value: ", "")
    a = a.replace("Repeat: ", "")
    
    #This prints out the filtered string, then sends it
    print "sending: " + a
    conn.sendline(a)
}
```

Flag: flag{again_and_again_and_again_and_again}

