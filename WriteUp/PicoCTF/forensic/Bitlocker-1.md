# picoCTF2025 Bitlocker-1

## 問題文

説明
Jackyはセキュリティパスワードの知識が乏しく、BitLockerドライブの暗号化に単純なパスワードを使用しました。この暗号化を突破できるか試してみてください！
ディスクイメージはこちらからダウンロード

## 添付ファイル
bitlocker-1.dd

## 解法

### パスワード解析
まずは`bitlocker2john`というツールでハッシュ値を取ってくる。
`bitlocker2john`は`Jhon the ripper`というツールに含まれていることが多いらしい

```bash
$ bitlocker2john -i bitlocker-1.dd > bitlocker_hash.txt

Signature found at 0x3
Version: 8
Invalid version, looking for a signature with valid version...

Signature found at 0x2195000
Version: 2 (Windows 7 or later)

VMK entry found at 0x21950c5

VMK encrypted with Recovery Password found at 0x21950e6
Searching AES-CCM from 0x2195102
Trying offset 0x2195195....
VMK encrypted with AES-CCM!!

VMK entry found at 0x2195241

VMK encrypted with User Password found at 2195262
VMK encrypted with AES-CCM

Signature found at 0x2c1d000
Version: 2 (Windows 7 or later)

VMK entry found at 0x2c1d0c5

VMK entry found at 0x2c1d241

Signature found at 0x373a000
Version: 2 (Windows 7 or later)

VMK entry found at 0x373a0c5

VMK entry found at 0x373a241
```
`bitlocker_hash.txt`の中身は次の通り

```bash
Encrypted device bitlocker-1.dd opened, size 100MB
Salt: 2b71884a0ef66f0b9de049a82a39d15b
RP Nonce: 00be8a46ead6da0106000000
RP MAC: a28f1a60db3e3fe4049a821c3aea5e4b
RP VMK: a1957baea68cd29488c0f3f6efcd4689e43f8ba3120a33048b2ef2c9702e298e4c260743126ec8bd29bc6d58

UP Nonce: d04d9c58eed6da010a000000
UP MAC: 68156e51e53f0a01c076a32ba2b2999a
UP VMK: fffce8530fbe5d84b4c19ac71f6c79375b87d40c2d871ed2b7b5559d71ba31b6779c6f41412fd6869442d66d


User Password hash:
$bitlocker$0$16$cb4809fe9628471a411f8380e0f668db$1048576$12$d04d9c58eed6da010a000000$60$68156e51e53f0a01c076a32ba2b2999afffce8530fbe5d84b4c19ac71f6c79375b87d40c2d871ed2b7b5559d71ba31b6779c6f41412fd6869442d66d
Hash type: User Password with MAC verification (slower solution, no false positives)
$bitlocker$1$16$cb4809fe9628471a411f8380e0f668db$1048576$12$d04d9c58eed6da010a000000$60$68156e51e53f0a01c076a32ba2b2999afffce8530fbe5d84b4c19ac71f6c79375b87d40c2d871ed2b7b5559d71ba31b6779c6f41412fd6869442d66d
Hash type: Recovery Password fast attack
$bitlocker$2$16$2b71884a0ef66f0b9de049a82a39d15b$1048576$12$00be8a46ead6da0106000000$60$a28f1a60db3e3fe4049a821c3aea5e4ba1957baea68cd29488c0f3f6efcd4689e43f8ba3120a33048b2ef2c9702e298e4c260743126ec8bd29bc6d58
Hash type: Recovery Password with MAC verification (slower solution, no false positives)
$bitlocker$3$16$2b71884a0ef66f0b9de049a82a39d15b$1048576$12$00be8a46ead6da0106000000$60$a28f1a60db3e3fe4049a821c3aea5e4ba1957baea68cd29488c0f3f6efcd4689e43f8ba3120a33048b2ef2c9702e298e4c260743126ec8bd29bc6d58
```
この中の`$bitlocker$1$16$cb4809fe9628471a411f8380e0f668db$1048576$12$d04d9c58eed6da010a000000$60$68156e51e53f0a01c076a32ba2b2999afffce8530fbe5d84b4c19ac71f6c79375b87d40c2d871ed2b7b5559d71ba31b6779c6f41412fd6869442d66d`というところをコピペして別の場所へ移す。WSL上でやったほうが安心。今回は`hashes_rp.txt`というファイルに保存した。

次に、HashCatを使う。コマンドは次の通り

```bash
 hashcat -m 22100 hashes_rp.txt /usr/share/wordlists/rockyou.txt
```
(ここまでにrockyou.txtねえとかいろいろあったのでそのうちManual書きます)

実行結果

```bash
└─$ cat -v hashs.txt
$bitlocker$0$16$cb4809fe9628471a411f8380e0f668db$1048576$12$d04d9c58eed6da010a000000$60$68156e51e53f0a01c076a32ba2b2999afffce8530fbe5d84b4c19ac71f6c79375b87d40c2d871ed2b7b5559d71ba31b6779c6f41412fd6869442d66d
┌──(neon_0256㉿DESKTOP-RC3UFQ4)-[~/ctf_shortcut/picoCTF2025/Bitlocker-1]
└─$ nano hashes_rp.txt

┌──(neon_0256㉿DESKTOP-RC3UFQ4)-[~/ctf_shortcut/picoCTF2025/Bitlocker-1]
└─$ ls
bitlocker-1.dd  bitlocker_hash.txt  hashes_rp.txt  hashs.txt

┌──(neon_0256㉿DESKTOP-RC3UFQ4)-[~/ctf_shortcut/picoCTF2025/Bitlocker-1]
└─$ cat -v hashes_rp.txt
$bitlocker$1$16$cb4809fe9628471a411f8380e0f668db$1048576$12$d04d9c58eed6da010a000000$60$68156e51e53f0a01c076a32ba2b2999afffce8530fbe5d84b4c19ac71f6c79375b87d40c2d871ed2b7b5559d71ba31b6779c6f41412fd6869442d66d

┌──(neon_0256㉿DESKTOP-RC3UFQ4)-[~/ctf_shortcut/picoCTF2025/Bitlocker-1]
└─$ hashcat -m 22100 hashes_rp.txt /usr/share/wordlists/rockyou.txt
hashcat (v6.2.6) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #1: cpu-haswell-AMD Ryzen 7 7735U with Radeon Graphics, 6633/13330 MB (2048 MB allocatable), 16MCU

Minimum password length supported by kernel: 4
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Single-Hash
* Single-Salt
* Slow-Hash-SIMD-LOOP

Watchdog: Hardware monitoring interface not found on your system.
Watchdog: Temperature abort trigger disabled.

Host memory required for this attack: 0 MB

Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344392
* Bytes.....: 139921507
* Keyspace..: 14344385
* Runtime...: 1 sec

Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 22100 (BitLocker)
Hash.Target......: $bitlocker$1$16$cb4809fe9628471a411f8380e0f668db$10...42d66d
Time.Started.....: Wed Oct  1 16:39:40 2025 (22 secs)
Time.Estimated...: Wed Oct  1 16:40:02 2025 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:       91 H/s (10.50ms) @ Accel:256 Loops:4096 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 2048/14344385 (0.01%)
Rejected.........: 0/2048 (0.00%)
Restore.Point....: 1792/14344385 (0.01%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:1044480-1048576
Candidate.Engine.: Device Generator
Candidates.#1....: clifford -> lovers1

Started: Wed Oct  1 16:39:37 2025
Stopped: Wed Oct  1 16:40:04 2025
```
実行できたので`--show`で結果を確認

```
$ hashcat -m 22100 hashes_rp.txt --show
$bitlocker$1$16$cb4809fe9628471a411f8380e0f668db$1048576$12$d04d9c58eed6da010a000000$60$68156e51e53f0a01c076a32ba2b2999afffce8530fbe5d84b4c19ac71f6c79375b87d40c2d871ed2b7b5559d71ba31b6779c6f41412fd6869442d66d:jacqueline
```

よって、パスワードは`jacqueline`であることがわかった。

### BitLockerを開いて確認

BitLockerを開くにはdislockerというツールを使うらしい。kaliに標準で入ってなかったので入れてきた。
コマンドは次のように

```bash
mkdir mount_point
sudo dislocker -V bitlocker-1.dd -u"jacqueline" -- mount_point
```
普通に管理者権限でやらないほうがよかったかも
検証はできてないのでよーわからん

中身確認
```bash
$ sudo ls -la mount_point/
total 0
dr-xr-xr-x 2 root      root              0 Jan  1  1970 .
drwxrwxrwx 1 neon_0256 neon_0256      4096 Oct  1 16:43 ..
-rw-rw-rw- 1 root      root      104857600 Jan  1  1970 dislocker-file
```
`dislocker-file`っていうやつが重要らしい。
そのまま開こうとしても開けなかった。
```
中身はファイルシステム（NTFSなど）の構造を持っています。この中身にアクセスするには、これをLinuxシステムにマウントする必要があります。
```
とのことなので、マウントして開く。
```bash
$ mkdir recovered_data
$ sudo mount -o loop,ro mount_point/dislocker-file recovered_data
$ ls recovered_data
'$RECYCLE.BIN'   flag.txt  'System Volume Information'
```
`flag.txt`の中を確認
```bash
$ sudo cat recovered_data/flag.txt
picoCTF{us3_b3tt3r_p4ssw0rd5_pl5!_********}
```
アンマウントしておこう
```bash
$ sudo umount recovered_data
```
