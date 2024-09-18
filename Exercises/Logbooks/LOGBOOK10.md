# Logbook 10

## Task 1

In the first task, a file named `ciphertxt.txt` was provided, containing encrypted content. Utilizing a technique known as `Frequency analysis`, the list of most frequently used letters was determined using the `freq.py` code that was also provided.

```note
1-gram (top 20):
f: 578
v: 578
m: 525
x: 385
w: 299
g: 237
e: 235
y: 198
d: 192
i: 192
k: 188
b: 111
j: 110
l: 106
s: 80
r: 63
q: 52
h: 39
t: 38
o: 30
-------------------------------------
2-gram (top 20):
mx: 108
fx: 79
wf: 73
fg: 73
yv: 70
ym: 67
vw: 66
iv: 61
dv: 60
vx: 59
yf: 56
gi: 56
mw: 56
vg: 55
fw: 53
qd: 52
ve: 47
im: 47
wv: 46
vk: 45
-------------------------------------
3-gram (top 20):
qdv: 40
lmx: 30
vgi: 28
fgy: 26
fgi: 22
giv: 22
Qdv: 19
gym: 19
gim: 18
lvw: 16
ifg: 15
wfg: 15
fym: 14
fwf: 14
xfx: 13
gyv: 13
mxx: 13
vxi: 13
wfk: 12
mxf: 12
```

Combining this letter frequency with the provided bigrams and trigrams, it led us to the following correlation:

```note
q -> Q
d -> U
v -> E
m -> O 
k -> M
x -> S
b -> L
l -> V
f -> A
w -> R
i -> T
c -> J
e -> I
g -> N
r -> G
y -> D
j -> C
s -> P
o -> H
h -> B
t -> F
p -> Z
z -> X
```

Thus, the file was decrypted with the command `tr 'qdvmkxblfwicegryjsohtpz' 'QUEOMSLVARTJINGDCPHBFXZ' < cipher.txt > out.txt`:

![Alt text](/images/Logbook10-1.png)

![Alt text](/images/Logbook10-2.png)

## Task 2

This task required encrypting a file with three different cipher techniques. The recommended ones were:

```note
openssl enc -aes-128-cbc -e -in out.txt -out cipher.bin \-K 00112233445566778889aabbccddeeff \-iv 0102030405060708
openssl enc -bf-cbc -e -in out.txt -out cipher1.bin \-K 00112233445566778889aabbccddeeff \-iv 0102030405060708
openssl enc -aes-128-cfb -e -in out.txt -out cipher2.bin \-K 00112233445566778889aabbccddeeff \-iv 0102030405060708
```

Thus, `out.txt` was encrypted with three different encryptors.

## Task 3

In this task, an image `pic_original.bmp` was provided. The task was to encrypt the image, we used the following command to encrypt it:

```note
openssl enc -aes-128-ecb -e -in pic_original.bmp -out img-bmp \-K 00112233445566778889aabbccddeeff \-iv 0102030405060708
```

Subsequently, the following commands were executed:

```note
head -c 54 pic_original.bmp > header
tail -c +55 img-bmp > body
cat header body > newimg.bmp
```

The resulting image was:

![Alt text](/images/Logbook10-3.png)
