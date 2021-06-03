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
