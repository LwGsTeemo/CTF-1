# Steg-1: STEG(必) 20
# 編碼與解碼:

編碼與解碼是資訊領域常使用的技術

Ascii、Base64、Base32各有其適用處

# encode_and_decode_1:
>*  Ascii 20
>*  ASCII(American Standard Code for Information Interchange)美國資訊交換標準代碼是基於拉丁字母的一套電腦編碼系統
>*  ASCII是現今最通用的單字元編碼系統。
>*  https://en.wikipedia.org/wiki/ASCII
>*  https://www.asciitable.com/
>*  https://www.w3schools.com/charsets/ref_html_ascii.asp

# encode_and_decode_2:
>* Base64 20
>* https://en.wikipedia.org/wiki/Base64
>* 使用線上工具解題
>* 使用python程式解題


# encode_and_decode_3:

>* Base32 20
>* https://en.wikipedia.org/wiki/Base32


# encode_and_decode_4:

>* Morse code 20
>* https://en.wikipedia.org/wiki/Morse_code
>* 使用線上工具解題
```
https://morsecode.scphillips.com/translator.html
```

# encode_and_decode_5:

>*  angstromCTF 2016 : what-the-hex 20
>* 

解題步驟1:

```
echo "6236343a20615735305a584a755a58526659323975646d567963326c76626c3930623239736331397962324e72" | xxd -r -p
```

解題步驟2:
```
echo aW50ZXJuZXRfY29udmVyc2lvbl90b29sc19yb2Nr | base64 -d
```

# encode_and_decode_6:

>*  Internetwache CTF 2016: The hidden message 50
>*  https://www.xil.se/post/internetwache-2016-misc-50-arturo182/
>*  8進位==>轉成字串(Octal to string)==>再base64

解題步驟1:
```
0000000 126 062 126 163 142 103 102 153 142 062 065 154 111 121 157 113
0000020 122 155 170 150 132 172 157 147 123 126 144 067 124 152 102 146
0000040 115 107 065 154 130 062 116 150 142 154 071 172 144 104 102 167
0000060 130 063 153 167 144 130 060 113 012
0000071
```
使用線上工具解題 ==> http://www.unit-conversion.info/texttools/octal/

使用python解題
```
import string

res = ''
f = open('README.txt')
for line in f:
    res = res + ''.join([chr(string.atoi(x, base=8)) for x in line.split(' ')[1::]])

print res
```


# encode_and_decode_7:

>*  SECCON CTF 2014: Easy Cipher 100
>*  https://github.com/S42X/CTF/blob/master/SECCON/EasyCipher.md

解題:

```
#!/usr/bin/python

c = '87 101 108 1100011 0157 6d 0145 040 116 0157 100000 0164 104 1100101 32 0123 69 67 0103 1001111 \
     1001110 040 062 060 49 064 100000 0157 110 6c 0151 1101110 101 040 0103 1010100 70 101110 0124 \
     1101000 101 100000 1010011 1000101 67 0103 4f 4e 100000 105 1110011 040 116 1101000 0145 040 \
     1100010 0151 103 103 0145 1110011 0164 100000 1101000 0141 99 6b 1100101 0162 32 0143 111 1101110\
     1110100 101 0163 0164 040 0151 0156 040 74 0141 1110000 1100001 0156 056 4f 0157 0160 115 44 040\
     0171 1101111 117 100000 1110111 0141 0156 1110100 32 0164 6f 32 6b 1101110 1101111 1110111 100000\
     0164 1101000 0145 040 0146 6c 97 1100111 2c 100000 0144 111 110 100111 116 100000 1111001 6f 117\
     63 0110 1100101 0162 0145 100000 1111001 111 117 100000 97 114 0145 46 1010011 0105 0103 67 79\
     1001110 123 87 110011 110001 67 110000 1001101 32 55 060 100000 110111 0110 110011 32 53 51 0103\
     0103 060 0116 040 5a 0117 73 0101 7d 1001000 0141 1110110 1100101 100000 102 0165 0156 33'
     
flag = ""
for _ in c.split(' '):
  if len(_) == 2 and _[1:].isalpha(): #HEX
    flag += _.decode('hex')
  if (len(_) == 2 and not _[1:].isalpha()) or (len(_) == 3 and int(_[0]) != 0): #DEC
    flag += chr(int(_))
  if len(_) == 4 or (len(_) == 3 and int(_[0]) == 0) : #OCT
    flag += chr(int(_, 8))
  if len(_) > 4: #BIN
    flag += binascii.unhexlify('%x' % int(_,2))
print flag
```
```
import sys
import binascii
import string

def loadlist(infile):
	tlist = []
	for line in open(infile,'r'):
		for w in line.split(): tlist.append(w.lower())
	return tlist

# first argument: binary/octal/decimal/hexadecimal input
if len(sys.argv) != 2: sys.exit(2)

words = loadlist(sys.argv[1])
chars = set('abcdef')
msg = ''
for w in words:
	try:
		msg+=binascii.unhexlify('%x' % int(w,2))
	except (ValueError, TypeError) as e:
		if any((c in chars) for c in w):
			msg+=w.decode('hex')
			continue
		if w[0] == '0':
			msg+=chr(string.atoi(w, base=8))
			continue
		msg+=chr(int(w))
print msg
```


# encode_and_decode_8:

>*  alexctf-2017: CR1: Ultracoded 50
>*  https://fadec0d3.blogspot.tw/2017/02/alexctf-2017-crypto.html
>*  https://0xd13a.github.io/ctfs/alexctf2017/ultracoded/

解題:
```
from pwn import *
import morse_talk as mtalk

with open('zero_one', 'r') as f:
    data = f.read().translate(None, ' \n')

data = data.replace("ZERO","0").replace("ONE","1")
data = b64d(''.join(chr(int(data[i:i+8], 2)) for i in xrange(0, len(data), 8)))

data = mtalk.decode(data)

print data
```


# encode_and_decode_9:
>*  DNA編碼學
>*  qiwi_infosec_ctf_2016_crypto_100 100
>*  https://github.com/USCGA/writeups/tree/master/online_ctfs/qiwi_infosec_ctf_2016/crypto_100_3_COMPLETE

解題:

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# @Author: John Hammond
# @Date:   2016-11-18 12:51:23
# @Last Modified by:   John Hammond
# @Last Modified time: 2016-11-18 13:12:51

mapping = {
		
		'CGA': 'A',
		'CCA': 'B',
		'GTT': 'C',
		'TTG': 'D',
		'GGC': 'E',
		'GGT': 'F',
		'TTT': 'G',
		'CGC': 'H',
		'ATG': 'I',
		'AGT': 'J',
		'AAG': 'K',
		'TGC': 'L',
		'TCC': 'M',
		'TCT': 'N',
		'GGA': 'O',
		'GTG': 'P',
		'AAC': 'Q',
		'TCA': 'R',
		'ACG': 'S',
		'TTC': 'T',
		'CTG': 'U',
		'CCT': 'V',
		'CCG': 'W',
		'CTA': 'X',
		'AAA': 'Y',
		'CTT': 'Z',
		'ATA': ' ',
		'TCG': ',',
		'GAT': '.',
		'GCT': ':',
		'ACT': '0',
		'ACC': '1',
		'TAG': '2',
		'GCA': '3',
		'GAG': '4',
		'AGA': '5',
		'TTA': '6',
		'ACA': '7',
		'AGG': '8',
		'GCG': '9'
}

def decode_dna( string ):

	pieces = []
	for i in range( 0, len(string), 3 ):
		piece =  string[i:i+3]
		# pieces.append()
		pieces.append( mapping[piece] )

	return "".join(pieces)

string = 'GGTTCAATGGGCTTGTCAATGGTTCGCATATCCATGGGCACGGTTCGCGGCTCA'	
print decode_dna(string)
```

# Steg-2
>*  CSAW QUALS 2015: keep-calm-and-ctf(必)50

解題步驟1:查看檔案格式
```
file /root/Desktop/img.jpg
```

解題步驟2:查看檔案內藏的字串
```
strings /root/Desktop/img.jpg
```
解題步驟3:安裝工具並學習使用技術

google搜尋jpg metadata linux
>* http://xahlee.info/img/metadata_in_image_files.html
>* http://libre-software.net/edit-image-metadata-on-linux/

```
sudo apt-get install exiftool
```

解題步驟4:查看檔案並讀出答案


```
exiftool img.jpg
```

# Steg-3
>* ABCTF 2016 : just-open-it(必) 50

解題步驟1:查看檔案格式

解題步驟2:查看檔案內藏的字串
```
strings just_open_it.jpg | grep ABCTF
```

# Steg-4

>* CSAW Quals CTF 2013: Black & White 50

解題步驟1:查看檔案格式

解題步驟2:查看檔案內藏的字串

解題步驟3:打開檔案看看

解題步驟4:使用stegsolve調色階
```
https://aur.archlinux.org/packages/stegsolve/

the flag in the blue, red or green 0 pane
```
解題步驟4:
解題步驟5:
解題步驟6:

# Steg-5

>* sCTF 2016 Q1 : banana-boy-20(必) 50
>* https://github.com/ctfs/write-ups-2016/tree/master/sctf-2016-q1/forensic/banana-boy-20
>* http://blog.csdn.net/riba2534/article/details/70544076
>* https://github.com/nbrisset/CTF/tree/master/sctf-2016-q1/challenges/banana-boy-20

解題步驟1:查看檔案格式

解題步驟2:使用hex編輯器分析檔案

解題步驟3:使用binwalk分析檔案

>* https://github.com/devttys0/binwalk
>* http://www.freebuf.com/sectool/15266.html

```
binwalk carter.jpg
```
解題步驟4:使用dd抽出檔案
```
dd if=carter.jpg of=carter-1.jpg skip=140147 bs=1
```

>* if是指定輸入檔
>* of是指定輸出檔
>* skip是指定從輸入檔開頭跳過140147個塊後再開始複製
>* bs設置每次讀寫塊的大小為1位元組 

解題步驟4b:也可以使用foremost抽出檔案

foremost是一個基於檔檔頭和尾部資訊以及檔的內建資料結構恢復檔的命令列工具
```
[1]Linux安裝：apt-get install foremost
[2]查看使用方式:foremost -help
[3]檔案分離:foremost carter.jpg
```
foremost會自動生成output目錄存放分離檔

解題步驟5:從抽離出的檔案中讀出答案


### hex編輯器
```
Windows平台:winhex,UltraEdit
Linux平台:hexedit, hexdump
安裝hexedit==>sudo apt install ncurses-hexedit
```
### binwalk

### dd

>* https://en.wikipedia.org/wiki/Dd_(Unix)
>* http://blog.csdn.net/riba2534/article/details/70544076
>* http://www.cnblogs.com/qq78292959/archive/2012/02/23/2364760.html

```
if=file #輸入檔案名稱，預設為標準輸入。 
of=file #輸出檔案名稱，預設為標準輸出。 
ibs=bytes #一次讀入 bytes 個位元組(即一個塊大小為 bytes 個位元組)。 
obs=bytes #一次寫 bytes 個位元組(即一個塊大小為 bytes 個位元組)。 
bs=bytes #同時設置讀寫塊的大小為 bytes ，可代替 ibs 和 obs 。 
cbs=bytes #一次轉換 bytes 個位元組，即轉換緩衝區大小。 
skip=blocks #從輸入檔開頭跳過 blocks 個塊後再開始複製。 
seek=blocks #從輸出檔開頭跳過 blocks 個塊後再開始複製。(通常只有當輸出檔是磁片或磁帶時才有效)。 
count=blocks #僅拷貝 blocks 個塊，塊大小等於 ibs 指定的位元組數。 
conv=conversion[,conversion...] #用指定的參數轉換檔。
```


# Steg-6
BITSCTF 2017 : black-hole-10 20

>* https://github.com/ctfs/write-ups-2017/tree/master/bitsctf-2017/forensic/black-hole-10
>* https://github.com/USCGA/writeups/tree/master/online_ctfs/bitsctf_2017/black_hole
>* https://github.com/nbrisset/CTF/tree/master/bitsctf-2017/challenges/black-hole-10_lily_flac


解題步驟1:


解題步驟2:
```
root@kali:~/Desktop# strings black_hole.jpg 
JFIF
$3br
%&'()*456789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
	#3R
&'()*56789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
}#Q-
………………………………………………..
M$Wh
UQklUQ1RGe1M1IDAwMTQrODF9
ZI;Z+
e!K]z
>}v#y=
&XSlP
7*qm
```
解題步驟3:
```
root@kali:~/Desktop# python 
Python 2.7.14 (default, Sep 17 2017, 18:50:44) 
[GCC 7.2.0] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import base64
>>> base64.b64encode('BITSCTF')
'QklUU0NURg=='
```
解題步驟4:
```
root@kali:~/Desktop# strings black_hole.jpg 
JFIF
$3br
%&'()*456789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
	#3R
&'()*56789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
}#Q-
………………………………………………..
M$Wh
UQklUQ1RGe1M1IDAwMTQrODF9
ZI;Z+
e!K]z
>}v#y=
&XSlP
7*qm
```
解題步驟5:
```
root@kali:~/Desktop# python
Python 2.7.14 (default, Sep 17 2017, 18:50:44) 
[GCC 7.2.0] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import base64
>>> base64.b64decode('QklUQ1RGe1M1IDAwMTQrODF9')
```


# Steg-7:BITSCTF 2017 : flagception-30 50(略)

>* https://github.com/USCGA/writeups/tree/master/online_ctfs/bitsctf_2017/flagception

```
#!/usr/bin/env python

print ''.join([chr(int(i,2)) for i in '1000010 1001001 1010100 1010011 1000011 1010100 1000110 1111011 1100110 0110001 1100001 1100111 1100011 0110011 1110000 1110100 0110001 0110000 1101110 1111101'.split(' ')])
```

# Steg-8: ABCTF 2016 : gz-30 60

解題步驟1:查看檔案格式
```
root@kali:~/Desktop# file flag
flag: gzip compressed data, was "flag", last modified: Sun Jun 26 17:22:38 2016, from Unix
```
解題步驟2:
```
root@kali:~/Desktop# mv flag flag.gz
```
解題步驟3:
```
root@kali:~/Desktop# gzip -d flag.gz
```
解題步驟4:
```
root@kali:~/Desktop# cat  flag
```

### gzip命令
gzip命令用來壓縮檔案。gzip是個使用廣泛的壓縮程式，檔經它壓縮過後，其名稱後面會多處“.gz”副檔名。

gzip是在Linux系統中經常使用的一個對檔進行壓縮和解壓縮的命令，既方便又好用。gzip不僅可以用來壓縮大的、較少使用的檔以節省磁碟空間，還可以和tar命令一起構成Linux作業系統中比較流行的壓縮檔格式。據統計，gzip命令對文字檔有60%～70%的壓縮率。減少檔大小有兩個明顯的好處，一是可以減少存儲空間，二是通過網路傳輸檔時，可以減少傳輸的時間。

gzip命令的常用選項
```
-c，--stdout將解壓縮的內容輸出到標準輸出，原檔案保持不變
-d，--decompress解壓縮
-f，--force強制覆蓋舊檔案
-l，--list列出壓縮包內儲存的原始檔案的資訊（如，解壓後的名字、壓縮率等）
-n，--no-name壓縮時不儲存原始檔案的檔案名和時間戳，解壓縮時不恢復原始檔案的檔案名和時間戳（此時，解出來的檔案，其檔案名為壓縮包的檔案名）
-N，--name壓縮時儲存原始檔案的檔案名和時間戳，解壓縮時恢復原始檔案的檔案名和時間戳
-q，--quiet抑制所有警告資訊
-r，--recursive遞迴
-t，--test測試壓縮檔案完整性
-v，--verbose冗餘模式（即顯示每一步的執行內容）
-1、-2、...、-9壓縮率依次增大，速度依次減慢，預設為-6

```

# Steg-9: ABCTF 2016 : best-ganondorf-50  60

解題步驟1:查看檔案格式
```
root@kali:~/Desktop# file ezmonay.jpg
ezmonay.jpg: PDP-11 UNIX/RT ldp
```

解題步驟2:上網看看檔案特徵

List of file signatures檔案格式特徵

https://en.wikipedia.org/wiki/List_of_file_signatures

找出jpg的檔案特徵

解題步驟3:使用xxd查看題目的檔案格式
```
root@kali:~/Desktop# xxd ezmonay.jpg | head 
```


解題步驟4:修改成正確的檔案格式
```
root@kali:~/Desktop# printf '\xff\xd8\xff' | dd of=ezmonay.jpg bs=1 seek=0 conv=notrunc
```
解題步驟5:確認是否已經修改正確
```
root@kali:~/Desktop# file ezmonay.jpg
ezmonay.jpg: JPEG image data
```

```
root@kali:~/Desktop# xxd ezmonay.jpg | head 
00000000: ffd8 ff00 4800 4800 00ff db00 4300 0302  ....H.H.....C...
```


解題步驟6:打開修正後的圖片幾可得到解答

### xxd

xxd:將一個檔案以十六進位的形式顯示出來
```
xxd -h

Usage:
       xxd [options] [infile [outfile]]
    or
       xxd -r [-s [-]offset] [-c cols] [-ps] [infile [outfile]]
```
```
Options:
    -a          toggle autoskip: A single '*' replaces nul-lines. Default off.
    -b          binary digit dump (incompatible with -ps,-i,-r). Default hex.
    -c cols     format <cols> octets per line. Default 16 (-i: 12, -ps: 30).
    -E          show characters in EBCDIC. Default ASCII.
    -e          little-endian dump (incompatible with -ps,-i,-r).
    -g          number of octets per group in normal output. Default 2 (-e: 4).
    -h          print this summary.
    -i          output in C include file style.
    -l len      stop after <len> octets.
    -o off      add <off> to the displayed file position.
    -ps         output in postscript plain hexdump style.
    -r          reverse operation: convert (or patch) hexdump into binary.
    -r -s off   revert with <off> added to file positions found in hexdump.
    -s [+][-]seek  start at <seek> bytes abs. (or +: rel.) infile offset.
    -u          use upper case hex letters.
    -v          show version: "xxd V1.10 27oct98 by Juergen Weigert".
```

PasswordPDF - 80
Use dictionary attack.

https://github.com/ctfs/write-ups-2016/tree/master/abctf-2016/forensic/passwordpdf-80

https://kimiyuki.net/blog/2016/07/23/abctf-2016/



$ pdfcrack --wordlist=crackstation-human-only.txt mypassword.pdf

 https://crackstation.net/buy-crackstation-wordlist-password-cracking-dictionary.htm
 
 The pdf password is elephant. Invert the pdf content: ABCTF{Damn_h4x0rz_always_bypassing_my_PDFs}.
http://blog.squareroots.de/en/2014/08/hitcon-ctf-2014-puzzle/
