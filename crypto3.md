# week7_現代密碼加解密與破密_PART_2

# Part II. Intermediate
>* https://crypto.stanford.edu/~dabo/papers/RSA-survey.pdf

>* Security Attacks on RSA  https://www.slideshare.net/pratikpoddar05051989/security-attacks-on-rsa
>* https://www.slideshare.net/ssuseraba268/rsa-in-ctf


>* 已知c,n,e==>用猜的==>
>* wiener attack(大e小) https://en.wikipedia.org/wiki/Wiener%27s_attack)
==> bctf/ABCTF 2015 WARMUP  Hack.lu CTF 2014: Wiener
>* Twin Prime的RSA破密分析==>2016 - MMA CTF - Twin Primes
>* common factor attack==>SECCON 2017 Quals:crypto_ps_and_qs
>* 加密指數攻擊:Hastad’s Broadcast Attack==>2017 picoCTF Broadcast
>* 模數攻擊:common modulus attack==>codeblue2017-common-modulus1/2/3
>* 如果 P 和 Q 相同==>2016 Qiwi Infosec CTF 2-400
>* Hastad’s Broadcast Attack==>ABCTF 2016

https://rootfs.eu/codeblue2017-common-modulus/

attackrsa
https://github.com/rk700/attackrsa

pablocelayes/rsa-wiener-attack
https://github.com/pablocelayes/rsa-wiener-attack

An automated, modular cryptanalysis tool; i.e., a Weapon of Math Destruction
https://github.com/nccgroup/featherduster

# RSA_101
>* Alex CTF 2017  CR4: Poor RSA

### 解法一:使用RsaCtfTool

>* https://0xd13a.github.io/ctfs/alexctf2017/poor-rsa/

```
> tar tvzf ../cr4_poor_rsa.tar.gz
-rw-r--r--  0 oddcoder oddcoder   69 Dec 11 10:08 flag.b64
-rw-r--r--  0 oddcoder oddcoder  162 Dec 11 09:59 key.pub
```
$ python RsaCtfTool.py --publickey ./key.pub --uncipher ./flag --verbose --private
```
Try weak key attack
-----BEGIN RSA PRIVATE KEY-----
MIH5AgEAAjJSqZ4knufPPAy/ljoAlmF3K8nN9uHj+/xuRKB6Xg+JRFep+Bw64TKs
VoPTWyi6XDJCQwIDAQABAjIzrQnKBvUPnpCxrK5x85DWuS8dbTtmFP+HEYHE3wja
TF9QEkV6ZDCUBers1jQeQwJ5MQIaAImWgwYMdrnA3lgaaeDqnZG+0Qcb6x2SSjcC
GgCZzedK7e6Hrf/daEy8R451mHC08gaS9lJVAhlmZEB1y+i/LC1L27xXycIhqKPe
aoR6qVfZAhlbPhKLmhFavne/AqQbQhwaWT/rqHUL9EMtAhk5pem+TgbW3zCYF8v7
j0mjJ31NC+0sLmx5
-----END RSA PRIVATE KEY-----
Clear text : .?&?d??#H?u6L???:ALEXCTF{SMALL_PRIMES_ARE_BAD}
```

### 解法二:
>* http://ronins.team/alexctf_cr4_poor_rsa/
>* https://fadec0d3.blogspot.com/2017/02/alexctf-2017-crypto.html

步驟一:format the hex values to get the integer product: 

openssl rsa -noout -text -inform PEM -in key.pub -pubin | grep -Evi 'mod|exp' | tr -d ':\n '


openssl rsa -noout -text -inform PEM -in key.pub -pubin | grep -Evi 'mod|exp' | tr -d ':\n ' | xargs python -c 'import sys; print int(sys.argv[1], 16)'

833810193564967701912362955539789451139872863794534923259743419423089229206473091408403560311191545764221310666338878019


步驟二:使用factordb.com 進行大質因數分解

答案如下:

863653476616376575308866344984576466644942572246900013156919 * 965445304326998194798282228842484732438457170595999523426901

步驟三:產生私鑰private key==>使用rsatool工具模組

語法格式: ./rsatool.py -p primeP -q primeQ -o outputFile

python ./rsatool/rsatool.py -p 863653476616376575308866344984576466644942572246900013156919 -q 965445304326998194798282228842484732438457170595999523426901 -o ./priv.key

步驟四:使用openssl解密==>就可以得到答案

openssl rsautl -decrypt -in flag.raw -inkey priv.key

### RSA_102

>* ABCTF 2016 : sexy-rsa-160
>* https://kimiyuki.net/writeup/ctf/2016/abctf-2016/

RSA cipher is strong. If only c,n,e are given, p,q must be gussable. For this n, p,q=⌊n−−√⌋±3.
```
#!/usr/bin/env python3
c = 293430917376708381243824815247228063605104303548720758108780880727974339086036691092136736806182713047603694090694712685069524383098129303183298249981051498714383399595430658107400768559066065231114145553134453396428041946588586604081230659780431638898871362957635105901091871385165354213544323931410599944377781013715195511539451275610913318909140275602013631077670399937733517949344579963174235423101450272762052806595645694091546721802246723616268373048438591
n = 1209143407476550975641959824312993703149920344437422193042293131572745298662696284279928622412441255652391493241414170537319784298367821654726781089600780498369402167443363862621886943970468819656731959468058528787895569936536904387979815183897568006750131879851263753496120098205966442010445601534305483783759226510120860633770814540166419495817666312474484061885435295870436055727722073738662516644186716532891328742452198364825809508602208516407566578212780807
e = 65537

def sqrt(x):
    low = -1
    high = c+1
    while low + 1 < high:
        m = (low + high) // 2
        y = m*m
        if y < x:
            low = m
        else:
            high = m
    m = high
    return m

r = sqrt(n)
p = r + 3
q = r - 3
assert n == p * q

import gmpy2
from Crypto.PublicKey import RSA
d = lambda p, q, e: int(gmpy2.invert(e, (p-1)*(q-1)))

key = RSA.construct((n, e, d(p,q,e)))
import binascii
print(binascii.unhexlify(hex(key.decrypt(c))[2:]).decode())
```
## 解法二:

>* https://p-te.fr/2016/07/23/abctf-sexy-rsa/
```
I recovered some RSA parameters. Can you decrypt the message?

c = 293430917376708381243824815247228063605104303548720758108780880727974339086036691092136736806182713047603694090694712685069524383098129303183298249981051498714383399595430658107400768559066065231114145553134453396428041946588586604081230659780431638898871362957635105901091871385165354213544323931410599944377781013715195511539451275610913318909140275602013631077670399937733517949344579963174235423101450272762052806595645694091546721802246723616268373048438591
n = 1209143407476550975641959824312993703149920344437422193042293131572745298662696284279928622412441255652391493241414170537319784298367821654726781089600780498369402167443363862621886943970468819656731959468058528787895569936536904387979815183897568006750131879851263753496120098205966442010445601534305483783759226510120860633770814540166419495817666312474484061885435295870436055727722073738662516644186716532891328742452198364825809508602208516407566578212780807
e = 65537

print len(n)
463

n = p * q = (x - 3)*(x + 3) = x² - 9
donc
p = sqrt(n + 9) - 3 
q = sqrt(n + 9) + 3

rsatool.py -n <n> -p <p> -q <q> -o key.pem

import binascii
c = 293430917376708381243824815247228063605104303548720758108780880727974339086036691092136736806182713047603694090694712685069524383098129303183298249981051498714383399595430658107400768559066065231114145553134453396428041946588586604081230659780431638898871362957635105901091871385165354213544323931410599944377781013715195511539451275610913318909140275602013631077670399937733517949344579963174235423101450272762052806595645694091546721802246723616268373048438591

open('cipher','w').write(binascii.unhexlify("%x"%c))

$ openssl rsautl -decrypt -in cipher -inkey key.pem -raw
ABCTF{i_h4ve_an_RSA_fetish_;)}
```

A Small Broadcast - 125
RSA is decryptable when there are many ciphertexts (≥e) for the same plaintext and the same e. https://en.wikipedia.org/wiki/RSA_(cryptosystem)#Attacks_against_plain_RSA. Implement it.

#!/usr/bin/env python3
N1 = 79608037716527910392060670707842954224114341083822168077002144855358998405023007345791355970838437273653492726857398313047195654933011803740498167538754807659255275632647165202835846338059572102420992692073303341392512490988413552501419357400503232190597741120726276250753866130679586474440949586692852365179
C1 = 34217065803425349356447652842993191079705593197469002356250751196039765990549766822180265723173964726087016890980051189787233837925650902081362222218365748633591895514369317316450142279676583079298758397507023942377316646300547978234729578678310028626408502085957725408232168284955403531891866121828640919987
N2 = 58002222048141232855465758799795991260844167004589249261667816662245991955274977287082142794911572989261856156040536668553365838145271642812811609687362700843661481653274617983708937827484947856793885821586285570844274545385852401777678956217807768608457322329935290042362221502367207511491516411517438589637
C2 = 48038542572368143315928949857213341349144690234757944150458420344577988496364306227393161112939226347074838727793761695978722074486902525121712796142366962172291716190060386128524977245133260307337691820789978610313893799675837391244062170879810270336080741790927340336486568319993335039457684586195656124176
N3 = 95136786745520478217269528603148282473715660891325372806774750455600642337159386952455144391867750492077191823630711097423473530235172124790951314315271310542765846789908387211336846556241994561268538528319743374290789112373774893547676601690882211706889553455962720218486395519200617695951617114702861810811
C3 = 55139001168534905791033093049281485849516290567638780139733282880064346293967470884523842813679361232423330290836063248352131025995684341143337417237119663347561882637003640064860966432102780676449991773140407055863369179692136108534952624411669691799286623699981636439331427079183234388844722074263884842748

import functools
import itertools

def chinese_remainder(n, a): # https://rosettacode.org/wiki/Chinese_remainder_theorem
    sum = 0
    prod = functools.reduce(lambda a, b: a*b, n)
    for n_i, a_i in zip(n, a):
        p = prod // n_i
        sum += a_i * mul_inv(p, n_i) * p
    return sum % prod
 
def mul_inv(a, b): # https://rosettacode.org/wiki/Chinese_remainder_theorem
    b0 = b
    x0, x1 = 0, 1
    if b == 1: return 1
    while a > 1:
        q = a // b
        a, b = b, a%b
        x0, x1 = x1 - q * x0, x0
    if x1 < 0: x1 += b0
    return x1

def inv_pow(c, e):
    low = -1
    high = c+1
    while low + 1 < high:
        m = (low + high) // 2
        p = pow(m, e)
        if p < c:
            low = m
        else:
            high = m
    m = high
    assert pow(m, e) == c
    return m
 
N = [N1, N2, N3]
C = [C1, C2, C3]
e = len(N)
a = chinese_remainder(N, C)
for n, c in zip(N, C):
    assert a % n == c
m = inv_pow(a, e)
print(bytes.fromhex(hex(m)[2:]).decode())




# RSA_CF_102
>* SECCON 2017 Quals:crypto_ps_and_qs
>* Common Factor Attack
>* 
>* https://github.com/p4-team/ctf/tree/master/2017-12-09-seccon-quals/crypto_ps_and_qs

```
public keys share a prime
factor both keys
generate private key
decrypt ciphertext with first key
```
### 解答一

>* http://kira924age.hatenadiary.com/entry/2018/01/14/000937

步驟一:解壓縮得到a ciphertext and two RSA public keys: pub1, pub2
```
$ unzip psqs1-0dd2921c9fbdb738e51639801f64164dd144d0771011a1dc3d55da6fbcb0fa02.zip
Archive:  psqs1-0dd2921c9fbdb738e51639801f64164dd144d0771011a1dc3d55da6fbcb0fa02.zip
[psqs1-0dd2921c9fbdb738e51639801f64164dd144d0771011a1dc3d55da6fbcb0fa02.zip] cipher password:
 extracting: cipher
  inflating: pub1.pub
  inflating: pub2.pub
```
步驟二:檢查兩公鑰是否具有共同因子

$ openssl rsa -text -pubin < pub1.pub
```
Public-Key: (4096 bit)
Modulus:
    00:cf:cf:bb:ee:a7:df:14:3a:8a:c2:08:b1:aa:1d:
    2f:86:54:5a:c4:cb:58:8c:94:a3:fb:1c:14:ad:91:
``` 
$ openssl rsa -text -pubin < pub2.pub
``` 
Public-Key: (4096 bit)
Modulus:
    00:bb:33:cc:7f:cc:8e:ca:f3:bf:9e:d9:5c:58:37:
    92:e1:ec:6b:80:ee:87:5e:c2:06:4d:bc:f0:75:95:
    c8:34:49:23:bf:53:65:24:d4:e0:a7:55:74:c7:79:  
``` 
``` 
#!/usr/bin/env python2
# coding: utf-8

s1 = """00:cf:cf:bb:ee:a7:df:14:3a:8a:c2:08:b1:aa:1d:
    2f:86:54:5a:c4:cb:58:8c:94:a3:fb:1c:14:ad:91:
    a4:f0:b9:36:15:7c:5a:4b:86:9c:18:a8:b8:64:f4:
    72:6b:f8:fc:dc:02:0c:b4:10:42:ba:c9:67:84:ab:
    7d:03:f9:37:49:47:ef:b0:bc:3d:66:58:31:97:43:
    40:15:9f:fc:3d:b7:c8:e7:4b:63:90:fd:a6:ee:c3:
    0b:81:c6:ff:62:4e:8d:3f:5b:17:bf:b7:a5:c7:ff:
    d8:ec:f4:e6:51:8b:39:3a:be:fd:dd:0f:ae:ba:43:
    08:74:6b:a6:3f:81:06:b5:9d:7e:05:89:43:a0:01:
    31:a7:d4:e5:38:c4:64:b2:70:57:76:47:ed:bc:47:
    8c:c1:ce:95:85:ef:e8:77:30:5b:3a:7c:2e:7c:44:
    db:54:75:ed:da:dc:34:5a:2c:90:a9:46:77:1c:ac:
    0a:45:4c:db:cb:46:1f:28:40:e7:61:3c:83:e9:ce:
    cc:94:03:7f:a0:9b:b9:da:a3:f1:80:56:2c:01:df:
    0b:e6:c5:1f:0c:06:e8:f0:e2:d6:e1:a5:e5:0d:0a:
    28:c3:88:11:40:77:0a:9f:45:93:41:46:b7:f3:59:
    b9:39:ce:23:f0:fa:50:7a:6f:4e:45:45:71:43:09:
    52:00:3c:20:f1:d9:7a:67:14:0b:6e:5f:cb:fb:3b:
    37:6e:4e:24:96:9a:eb:1d:48:9c:fc:72:af:4f:15:
    a4:78:8a:1a:a9:7c:89:75:6d:1d:4d:94:aa:47:e7:
    cd:3a:81:ae:cb:92:44:8c:c9:2c:77:d2:ef:57:6a:
    a0:db:c1:35:08:62:ac:cd:da:dd:bc:e8:03:57:f0:
    cd:5b:85:4d:d0:f8:c4:62:7f:e4:b7:18:b2:4e:cf:
    e1:1e:d2:4c:3b:e2:2f:00:64:3b:be:d4:ee:5e:34:
    5a:f1:76:e5:b7:6d:23:a2:f8:0e:0e:c6:f3:4e:57:
    18:c6:2a:70:fe:55:70:c2:8b:80:7b:44:f2:2e:ad:
    eb:d9:b5:ff:90:6f:6a:85:be:88:c0:c8:f6:e5:f8:
    80:a5:1f:17:f8:4d:b1:c2:ee:fe:a8:af:34:04:04:
    44:ce:d1:a3:7d:f0:e4:f5:f7:2c:c3:f5:0b:7e:42:
    7c:8c:2d:8b:61:86:ea:d7:62:f0:c4:44:b3:ca:3a:
    01:03:ed:12:a9:3b:ce:9c:ae:74:79:a2:29:eb:bc:
    0a:64:8e:aa:6f:97:e5:05:1a:66:eb:09:eb:d7:34:
    8e:92:f7:5f:12:5e:bd:c3:67:e2:a7:d1:da:77:59:
    d4:1f:ae:2e:26:35:bf:4b:7a:7f:91:be:ca:b3:ac:
    7d:05:bd"""

s2 = """
    00:bb:33:cc:7f:cc:8e:ca:f3:bf:9e:d9:5c:58:37:
    92:e1:ec:6b:80:ee:87:5e:c2:06:4d:bc:f0:75:95:
    c8:34:49:23:bf:53:65:24:d4:e0:a7:55:74:c7:79:
    8c:73:b1:97:dd:2b:1b:42:05:4b:1e:49:cb:45:fb:
    f0:4e:6f:11:4c:f8:a3:65:c3:df:36:45:52:4f:77:
    82:68:03:8a:3f:a2:68:02:e9:d1:ed:bf:bb:5e:df:
    b5:a0:c3:75:37:0d:7f:10:f5:7d:ab:bd:4f:77:1d:
    ad:36:32:f0:1b:9b:ce:10:48:99:66:ee:88:2d:ab:
    17:a3:3b:78:6a:a5:f7:31:65:a5:40:51:30:0b:1d:
    f9:28:03:92:a3:ed:e9:d3:fc:9c:4d:8a:6a:06:35:
    1f:6e:f3:59:8e:8d:e2:b3:9d:3b:19:af:64:a1:71:
    6c:d1:58:26:c3:f2:4c:b1:3d:eb:72:2c:3a:03:ef:
    1d:2b:e2:d0:a5:a6:e2:10:ff:5d:01:83:67:be:3b:
    f9:9e:a2:6b:a0:06:e5:16:4a:4d:d5:5a:ab:cd:44:
    9d:e5:ce:18:64:82:5d:c1:60:e5:0d:50:9e:b0:e6:
    fe:72:3e:f1:82:68:1e:dd:b9:40:84:b8:3e:c9:e2:
    e9:43:e8:7c:b8:75:09:ab:0f:d9:b1:ca:22:c1:ce:
    af:f3:9f:ca:cf:67:29:fc:0e:05:78:67:0d:87:d7:
    f0:f9:cc:be:09:cb:3e:12:ce:b8:95:57:2a:99:79:
    d1:0b:fd:bf:af:a2:60:56:8d:8d:b1:84:be:12:b3:
    e3:19:3e:07:72:9c:e3:c1:d9:cd:82:83:ed:69:83:
    a0:63:88:03:6a:0a:70:29:4f:23:39:29:44:77:82:
    80:e7:de:9f:60:16:3a:81:50:e3:0f:f4:a4:ea:02:
    79:2c:be:83:05:ba:a2:e9:9a:fe:51:e1:7d:af:c5:
    6b:e0:d3:84:14:7b:cd:38:e9:d1:29:34:ec:71:26:
    22:21:77:73:a4:b3:85:1a:9b:0c:6c:7c:3e:01:f6:
    11:1a:1e:1a:55:7f:4e:2a:e4:a2:47:ce:9b:75:cc:
    cc:b1:81:98:25:f3:05:4a:a1:c0:55:bd:3e:23:40:
    09:3a:e2:ef:1d:0f:a5:a1:76:82:5e:fd:f7:95:07:
    02:7f:51:04:08:00:09:14:2f:0d:43:e2:f1:0c:fa:
    d2:20:81:3b:bb:90:14:d4:f4:32:5e:da:c5:38:fb:
    5e:82:b7:53:e2:ad:3b:24:60:7d:73:80:aa:64:fc:
    b9:8b:59:ea:8b:5a:73:6b:80:93:83:24:8c:ec:e0:
    b1:72:55:ea:55:9e:90:12:7f:77:8a:f6:d7:e8:a6:
    6d:ad:91"""

n1 = int(s1.replace("\n", "").replace(":", "").replace(" ", ""), 16)
n2 = int(s2.replace("\n", "").replace(":", "").replace(" ", ""), 16)
e = 65537

# Extended Euclidean algorithm擴展歐幾裡得演算法(extgcd)
def extgcd(a, b):
    x,y, u,v = 0,1, 1,0
    while a != 0:
        q, r = b/a, b%a
        m, n = x-u*q, y-v*q
        b,a, x,y, u,v = a,r, u,v, m,n
        g = b
    return x, y, g

p = extgcd(n1, n2)[2]

q1 = n1 / p
q2 = n2 / p

def int2bin(d):
    t = format(d, 'x')
    return (t if len(t) % 2 == 0 else "0" + t).decode("hex")

def enclen(l):
    if l < 0x80:
        return chr(l)
    else:
        t = int2bin(l)
        return chr(0x80 + len(t)) + t

def encint(n):
    t = int2bin(n)
    return "\x02" + enclen(len(t)) + t

def generate_secret_key(n, p, q, e, s = "secret.key"):

    d = extgcd(e, (p-1)*(q-1))[0] % ((p-1)*(q-1))
    exp1 = d % (p-1)
    exp2 = d % (q1-1)
    coef = pow(q, p-2, p)

    t = "".join(map(encint, [0, n1, e, d, p, q, exp1, exp2, coef]))
    t = "\x30" + enclen(len(t)) + t

    secret_key = "-----BEGIN RSA PRIVATE KEY-----\n"
    secret_key += t.encode("base64")[:-1]
    secret_key += "\n-----END RSA PRIVATE KEY-----"

    with open(s, 'w') as f:
        f.write(secret_key)

generate_secret_key(n1, p, q1, e, "secret1.key")
generate_secret_key(n1, p, q2, e, "secret2.key")

``` 
步驟三:解密

$ cat cipher | openssl rsautl -decrypt -inkey secret1.key

SECCON{1234567890ABCDEF}

$ cat cipher | openssl rsautl -decrypt -inkey secret2.key

RSA operation error

### 解答二

As usual in case when there are more than one RSA public key, it's worth to check if maybe they don't share a prime:
```
import codecs
from Crypto.PublicKey import RSA
from crypto_commons.rsa.rsa_commons import gcd

def read_key(filename):
    with codecs.open(filename, "r") as input_file:
        data = input_file.read()
        pub = RSA.importKey(data)
        print(pub.e, pub.n)
    return pub


def main():
    pub1 = read_key("pub1.pub")
    pub2 = read_key("pub2.pub")
    p = gcd(pub1.n, pub2.n)
    print(p)
```
```
import codecs
from Crypto.Util.number import long_to_bytes
from crypto_commons.generic import bytes_to_long
from crypto_commons.rsa.rsa_commons import gcd, get_fi_distinct_primes, modinv

def read_ct():
    with codecs.open("cipher", "rb") as input_file:
        data = input_file.read()
        print(len(data))
        msg = bytes_to_long(data)
    return msg


p = gcd(pub1.n, pub2.n)
print(p)
q1 = pub1.n / p
q2 = pub2.n / p
print(p, q1)
print(p, q2)
msg = read_ct()

d1 = modinv(pub1.e, get_fi_distinct_primes([p, q1]))
d2 = modinv(pub2.e, get_fi_distinct_primes([p, q2]))

first = pow(msg, d1, pub1.n)
print(long_to_bytes(first))
```
```
import codecs
from Crypto.PublicKey import RSA
from Crypto.Util.number import long_to_bytes
from crypto_commons.generic import bytes_to_long
from crypto_commons.rsa.rsa_commons import gcd, get_fi_distinct_primes, modinv


def read_ct():
    with codecs.open("cipher", "rb") as input_file:
        data = input_file.read()
        print(len(data))
        msg = bytes_to_long(data)
    return msg


def read_key(filename):
    with codecs.open(filename, "r") as input_file:
        data = input_file.read()
        pub = RSA.importKey(data)
        print(pub.e, pub.n)
    return pub


def main():
    pub1 = read_key("pub1.pub")
    pub2 = read_key("pub2.pub")
    p = gcd(pub1.n, pub2.n)
    print(p)
    q1 = pub1.n / p
    q2 = pub2.n / p
    print(p, q1)
    print(p, q2)
    msg = read_ct()

    d1 = modinv(pub1.e, get_fi_distinct_primes([p, q1]))
    d2 = modinv(pub2.e, get_fi_distinct_primes([p, q2]))

    first = pow(msg, d1, pub1.n)
    print(long_to_bytes(first))


main()
```
# RSA_TP_10

MMA CTF 2nd 2016 : twin-primes-50



# RSA_CM1_104
codeblue2017
https://rootfs.eu/codeblue2017-common-modulus/
https://theromanxpl0it.github.io/ctf_codeblue2017/common1/

# RSA_CM1_105

# RSA_CM1_106

# RSA_WA_

# RSA Wiener Attack
```
使用openssl工具解析RSA公開金鑰檔後發現加密金鑰e的位數很大，因此對應的解密金鑰位數就會比較低，
可以猜測解題的思路是猜解出RSA解密金鑰d，也就是針對RSA演算法的低解密指數攻擊(RSA Wiener Attack)，
攻擊代碼可以參考https://ctfcrew.org/writeup/87
```
```
0x01  猜解RSA私密金鑰和素因數
0x02 生成RSA私密金鑰檔
0x03 使用openssl解密RSA密文

http://bobao.360.cn/ctf/learning/124.html
```
# HASH_001_暴力破解法

angstromCTF 2016 : brute-force-40
