## Level 5
#### Problem:
Find password file in a densely-populated directory with the following properties:
  - human-readable
  - 1033 bytes in size
  - not executable

#### Method:
Using [`find`](https://man7.org/linux/man-pages/man1/find.1.html) with specific min and max size constraints.
<details>
  <summary>Answer</summary> 
  
  `find -size -1034c -size +1032c`
</details>

## Level 6
#### Problem:
Find password file *somewhere on the server* with the following properties:
  - owned by user bandit7
  - owned by group bandit6
  - 33 bytes in size

#### Method:
Using [`find`](https://man7.org/linux/man-pages/man1/find.1.html) from root directory with user and group tests.
<details>
  <summary>Answer</summary> 
  
  `find -group bandit6 -user bandit7`
</details>

## Level 7
#### Problem:
Password is hidden in a 4MB `data.txt` file with 98567 lines of strings and possible passwords.  
Hint: password is next to the word `millionth`.

#### Method:
Using [`strings`](https://linux.die.net/man/1/strings) command and piping into [`grep`](https://man7.org/linux/man-pages/man1/grep.1.html).
<details>
  <summary>Answer</summary> 
  
  `strings data.txt | grep "millionth"`
</details>

## Level 8
#### Problem:
Password is in a 1001-line file.  
Hint: password is "the only line of text that occurs only once".

#### Method:
Using [`uniq`](https://man7.org/linux/man-pages/man1/uniq.1.html) in combination with [`sort`](https://man7.org/linux/man-pages/man1/sort.1.html).  
The trick lies in the fact that:
- `uniq` can only filter out adjacent duplicate lines, so we must use `sort` first.
- `uniq` without options still shows first occurences of duplicate lines.
<details>
  <summary>Answer</summary> 
  
  `cat data.txt | sort | uniq -u`  
  Password: `UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR`
</details>

## Level 9
#### Problem:
Password is hidden in a binary file full of human-unreadable characters.  
Hint: password is in one of the few human-readable strings, preceded by several ‘=’ characters.

#### Method:
Instead of overcomplicating things with `xxd`/`hexdump`, use [`strings`](https://linux.die.net/man/1/strings) to pull human-readable lines
and [`grep`](https://man7.org/linux/man-pages/man1/grep.1.html) to filter leading '='.
<details>
  <summary>Answer</summary> 
  
  `strings data.txt | grep "=="`  
  Password: `truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk`
</details>

## Level 10
#### Problem:
Password is in base64 encoded file.
#### Method:
Using [`base64`](https://linux.die.net/man/1/base64) to reverse the encoding.
<details>
  <summary>Answer</summary> 
  
  `cat data.txt | base64 --decode`  
  Password: `IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR`
</details>

## Level 11
#### Problem:
Password is in file where all letters (a-z, A-Z) have been rotated 13 places (see [ROT13 cipher](https://en.wikipedia.org/wiki/ROT13)).

#### Method:
Using [`tr`](https://linux.die.net/man/1/tr) to do the reverse-rotation, as discussed [here](https://unix.stackexchange.com/questions/19772/how-does-tr-a-z-n-za-m-work).
<details>
  <summary>Answer</summary> 
  
  `cat data.txt | tr '[a-z]' '[n-za-m]' | tr '[A-Z]' '[N-ZA-M]'`  
  Password: `5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu`
</details>

## Level 12
#### Problem:
Password is stored in a hexdump which has been compressed several times.

#### Method:
This one was pretty tricky. I will describe the general steps but leave out the order.
- Use [`xxd`](https://linux.die.net/man/1/xxd) to convert the hexdump to binary and **redirect** output to a new file.
- Use [`file`](https://linux.die.net/man/1/file) to reveal specific compression used. `-z` option reveals two levels deep.
- Files containing [`gzip`](https://linux.die.net/man/1/gzip) compressed data should be renamed to appropriate suffix using `mv`.
- Files using [`tar`](https://linux.die.net/man/1/tar) compression can be decompressed with `-xvf` options.
- Repeat until completely decompressed ASCII file is revealed.

<details>
  <summary>Answer</summary> 
  
  ```
  cp data.txt data2.txt
  xxd -r data2.txt > data3
  file data3
  mv data3 data3.gz
  gzip -d data3.gz
  file data3
  bzip2 -d data3
  file data3.out
  gzip -d data3.out
  mv data3.out data3.gz
  gzip -d data3.gz
  file data3.gz
  tar -xvf data3
  file data5.bin
  tar -xvf data5.bin
  file data6.bin
  bzip2 -d data6.bin
  file data6.bin.out
  tar -xvf data6.bin.out
  file data8.bin
  mv data8.bin data8.gz
  gzip -d data8.gz
  ```
  Password: `8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL`
</details>

## Level 13
#### Problem:
Instead of a password, the user has to use a private SSH key to log onto the next level.

#### Method:
Running `cat` on the private SSH key file reveals that it is in fact what it says it is.
Using it therefore just involved finding the right `ssh` options.
<details>
  <summary>Answer</summary> 
  
  `ssh bandit14@localhost -i sshkey.private`  
  Password: N/A
</details>

## Level 14 
#### Problem:
The prompt is to "submit the current level's password to port 30000 on localhost" to receive the next password.

#### Method:
After fiddling with `openssl s_client` for a bit and not getting the correct response, I read through the manuals
of each of the "potentially helpful commands" before finding what I needed in `nc` or Netcat, a command-line tool
for reading and writing data across network connections.
<details>
  <summary>Answer</summary> 
  
  `nc localhost 30000 < bandit14`  
  Password: `BfMYroe26WYalil77FoDi9qh59eK5xNr`
</details>

## Level 15
#### Problem:
Again, the challenge was to submit the current password to a port (30001 this time) on localhost,
but with the added requirement of using SSL encryption.

#### Method:
This time `openssl s_client` was the correct answer, as hinted through the mention of the `-ign_eof` option
in the note.  
Since `s_client`allows connection to a remote host using SSL by default, no additional flags 
were needed for that requirement.  
The only thing that took me a minute was what to do after successfully connecting, since all we see is a
blinking cursor.
<details>
  <summary>Answer</summary> 
  
  `openssl s_client -connect localhost:30001 -ign_eof`  
  Then paste in the current level's password to get the next one.  
  Password: `cluFn7wTiGryunymYOu4RcffSxQluehd`
</details>
  
## Level 16
#### Problem:
Retrieve the credentials for the next level by finding out which port in the range 31000 to 32000
has a server listening and is speaking SSL. There is only 1 server that will give new credentials.

#### Method:
`nmap` was the obvious choice for checking for open ports within that range first.  
 After which I just attempted to connect to the handful of ports to see which would work.
<details>
  <summary>Answer</summary> 
  
  `nmap localhost -p31000-32000`  
  `openssl s_client -showcerts -connect localhost:xxxx`  
  Password: 
  ```
  -----BEGIN RSA PRIVATE KEY-----
  MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
  imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
  Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
  DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
  JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
  x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
  KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
  J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
  d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
  YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
  vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
  +TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
  8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
  SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
  HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
  SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
  R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
  Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
  R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
  L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
  blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
  YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
  77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
  dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
  vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
  -----END RSA PRIVATE KEY-----
  ```
</details>
  
## Level 17
#### Problem:
The password is the only line that differs between the files `passwords.old` and `passwords.new` in the directory.

#### Method:
First we have to connect to the server using the private key gained in the previous level.  
This involved creating the keyfile, giving it appropriate permissions, and then running `ssh` but with a specified identity file.  
Afterwards it was a simple task of using `diff`.
<details>
  <summary>Answer</summary> 
  
  ```
  ssh -i keyfile bandit17@bandit.labs.overthewire.org -p 2220  # connecting to server
  diff passwords.new passwords.old
  ```
  Password: `kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd`
</details>

## Level 18
#### Problem:
The password is stored in a file called `readme` in the home directory, but `.bashrc` on the bandit18 user has been modified to log you out every time you log in.

#### Method:
Another `ssh` option is required to circumvent the logging out and read the file where we know the password is stored.
<details>
  <summary>Answer</summary> 
  
  `ssh bandit18@bandit.labs.overthewire.org -p 2220 -t "cat ~/readme"`  
  Password: `IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x`
</details>
  
## Level 19
#### Problem:
Use the setuid binary in the home directory to access the password stored in `etc/bandit_pass/bandit20`.

#### Method:
Running the binary without arguments as hinted yielded the initially confusing example of "./bandit20-do id". But once I figured out that `id` wasn't a variable but a command, accessing the password was pretty easy.
<details>
  <summary>Answer</summary> 
  
  `./bandit20-do cat /etc/bandit_pass/bandit20`  
  Password: `GbKksEFF4yrVs6il55v6gwY5aVje5f0j`
</details>
  
## Level 
#### Problem:


#### Method:

<details>
  <summary>Answer</summary> 
  
  ``  
  Password: ``
</details>

## Level 
#### Problem:


#### Method:

<details>
  <summary>Answer</summary> 
  
  ``  
  Password: ``
</details>

## Level 
#### Problem:


#### Method:

<details>
  <summary>Answer</summary> 
  
  ``  
  Password: ``
</details>
  
## Level 
#### Problem:


#### Method:

<details>
  <summary>Answer</summary> 
  
  ``  
  Password: ``
</details>
  
## Level 
#### Problem:


#### Method:

<details>
  <summary>Answer</summary> 
  
  ``  
  Password: ``
</details>
