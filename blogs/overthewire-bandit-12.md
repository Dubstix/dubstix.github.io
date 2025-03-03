OverTheWire Bandit Level-12 Writeup
===================================

Sep 14, 2021

OverTheWire Bandit is a Linux CTF that's challenging but fun. I chose to write about this specific bandit because of how challenging it was.

**Goal of the challenge**:
The password for the next level is stored in the file data.txt, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work using mkdir. For example: mkdir /tmp/myname123. Then copy the datafile using cp, and rename it using mv (read the manpages!)

We are given a list of Linux commands that might be useful:
grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd, mkdir, cp, mv, file, and finally a Wikipedia page that explains what a [Hex dump](https://en.wikipedia.org/wiki/Hex_dump) is.

First, let's SSH into the given host using:
`ssh bandit.labs.overthewire.org -p 2220 -l bandit12`

**Password**: _5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu_

Once we're in we need to find the data.txt file.

```
bandit12@bandit:~$ ls
data.txt
```

Now we need to create a folder in /tmp then copy data.txt there.

```
bandit12@bandit:~$ mkdir /tmp/myname123
bandit12@bandit:~$ cp data.txt /tmp/myname123
bandit12@bandit:~$ cd /tmp/myname123
bandit12@bandit:/tmp/myname123$ ls
data.txt
```

Next, according to the challenge "The password for the next level is stored in the file data.txt, which is a hexdump of a file that has been repeatedly compressed." We need to figure out what type of compression it is so we can decompress it.

```
bandit12@bandit:/tmp/myname123$ file data.txt
data.txt: ASCII text
```

The file contains ASCII, a hex dump. What we can do is use xxd -r to reverse the hex dump into binary then dump it into a file.

```
bandit12@bandit:/tmp/myname123$ file data.txt
data.txt: ASCII text
bandit12@bandit:/tmp/myname123$ xxd -r data.txt data
```

Now when we check the file type of our new file "data" we get this.

```
bandit12@bandit:/tmp/myname123$ file data
data: gzip compressed data, was "data2.bin", last modified: Thu May 7 18:14:30 2020, max compression, from Unix
```

This means that the file type can be converted to gzip then decompressed with gzip -d. "-d" for decompress.

```
bandit12@bandit:/tmp/myname123$ mv data data.gz
bandit12@bandit:/tmp/myname123$ gzip -d data.gz
bandit12@bandit:/tmp/myname123$ ls
data data.txt
```

Now this will be a little repetitive so bear with it. We got a data file from the data.gz. The next steps will be to: check file type -> convert -> decompress -> repeat until we get the file that contains the password for bandit13.

```
bandit12@bandit:/tmp/myname123$ file data
data: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/myname123$ mv data data.bz2
bandit12@bandit:/tmp/myname123$ bzip2 -d data.bz2
bandit12@bandit:/tmp/myname123$ ls
data data.txt
```

Above we got a bzip file type this time, but the process is still the same.

```
bandit12@bandit:/tmp/myname123$ file data
data: gzip compressed data, was "data4.bin", last modified: Thu May 7 18:14:30 2020, max compression, from Unix
bandit12@bandit:/tmp/myname123$ mv data data.gz
bandit12@bandit:/tmp/myname123$ gzip -d data.gz
bandit12@bandit:/tmp/myname123$ ls
data data.txt
```

Below we got a tar file type, the way to extract it is with `tar xvf`, it extracts and views the extracted file afterwards.

```
bandit12@bandit:/tmp/myname123$ file data
data: POSIX tar archive (GNU)
bandit12@bandit:/tmp/myname123$ mv data data.tar
bandit12@bandit:/tmp/myname123$ tar xvf data.tar
data5.bin
bandit12@bandit:/tmp/myname123$ ls
data5.bin data.tar data.txt
```

We got a .bin file. That's new.

```
bandit12@bandit:/tmp/myname123$ file data5.bin
data5.bin: POSIX tar archive (GNU)
bandit12@bandit:/tmp/myname123$ mv data5.bin data5.tar
bandit12@bandit:/tmp/myname123$ tar xvf data5.tar
data6.bin
```



```
bandit12@bandit:/tmp/myname123$ file data6.bin
data6.bin: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/myname123$ mv data6.bin data6.bz2
bandit12@bandit:/tmp/myname123$ bzip2 -d data6.bz2
bandit12@bandit:/tmp/myname123$ ls
data5.tar data6 data.tar data.txt
```



```
bandit12@bandit:/tmp/myname123$ file data6
data6: POSIX tar archive (GNU)
bandit12@bandit:/tmp/myname123$ mv data6 data6.tar
bandit12@bandit:/tmp/myname123$ tar xvf data6.tar
data8.bin
```



```
bandit12@bandit:/tmp/myname123$ file data8.bin
data8.bin: gzip compressed data, was "data9.bin", last modified: Thu May 7 18:14:30 2020, max compression, from Unix
bandit12@bandit:/tmp/myname123$ mv data8.bin data8.gz
bandit12@bandit:/tmp/myname123$ gzip -d data8.gz
bandit12@bandit:/tmp/myname123$ ls
data5.tar data6.tar data8 data.tar data.txt
```



```
bandit12@bandit:/tmp/myname123$ file data8
data8: ASCII text
bandit12@bandit:/tmp/myname123$ cat data8
The password is 8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL
```

The password for the next level, bandit13, is **8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL**.

If you've reached this far. Thanks for reading my first writeup!