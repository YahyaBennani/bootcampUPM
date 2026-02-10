# challenge maker for LAB1 
``` python
from Cryptodome.Util.number import getPrime, bytes_to_long

flag = b"5T4F1T{80uKHN6H4_chirir}"
m = bytes_to_long(flag)

e = 3

# Générer un n assez grand pour que m^e < n
while True:
    p = getPrime(1024)
    q = getPrime(1024)
    n = p * q
    if m**e < n:
        break

c = pow(m, e, n)

print("=== RSA Challenge ===")
print(f"n = {n}")
print(f"e = {e}")
print(f"c = {c}")
```
# challenge solver for LAB1 

``` python
from Cryptodome.Util.number import long_to_bytes
import gmpy2
c = 5709720175026317841944505166332182779419760031115432215563425901875620052791608163398750297304277886495537367793006288112991932717211066611899319963701838062828209254458935983141207100322897221021063116398621664612418813778554568264340430225832578491114653771121708580262264085968492732626559392615882541267670045570974444907542776294075571558085100930760760722416252378077610954367547852126659348372505030936460878940046808104816042562380177143194070773417030106158693436848897733795588156153439353374354378946281556140400056185498147808737766921652179963585037964078150136578302230878368862263000059181416416783313808950923297557514158129515879758935765781055731969330005473022370060498393105532201494698762244481915406880607127300210095672274400200661881861043562650754163743246330438392531433701024389754537178000821754581449881408420282753987132115940027280372196160429387747427183641264436940827271631231510105131701780747726012636466088768725514820344357055715523621362143648027363044170885031058505704
e = 3

m = gmpy2.iroot(c, e)[0]
print(long_to_bytes(m))
#picoCTF{e_sh0u1d_b3_lArg3r_92f4d5a5}

# don t forget to install dependecies
```
# challenge LAB 2

``` python
n = 11413
c = 913
cor_m = 900
e = 2

P.<x> = Zmod(n)[]
f = (cor_m + x)^e - c

roots = f.small_roots(X=20)

print(roots)   # [7]
```
# install sage

``` bash
- docker pull sagemath/sagemath
- docker run -it -v ${PWD}:/workspace sagemath/sagemath sage /workspace/solve.sage

- https://sagecell.sagemath.org/ #online sage
```

# challenge XOR

``` python
#!/usr/bin/env python3
import zlib
import string
import multiprocessing as mp

# ================= CONFIG =================

KEY_LEN = 20

CHARSET = (
    string.ascii_lowercase +
    string.ascii_uppercase +
    string.digits +
    "!@#$%^&*()-_=+{}[]:;,.?"
)

KNOWN_PLAINTEXT = bytes([
    0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A,
    0x00, 0x00, 0x00, 0x0D,
    0x49, 0x48, 0x44, 0x52
])

FILE_PATH = "cipher.txt" 

# ==========================================


def xor(data, key, offset=0):
    return bytes(
        data[i] ^ key[(offset + i) % len(key)]
        for i in range(len(data))
    )


def worker(char_range, cipher, base_key):
    ihdr_data_offset = 16
    ihdr_data_len = 13
    crc_offset = ihdr_data_offset + ihdr_data_len

    ihdr_data_cipher = cipher[ihdr_data_offset:ihdr_data_offset + ihdr_data_len]
    crc_cipher = cipher[crc_offset:crc_offset + 4]

    for c1 in char_range:
        for c2 in CHARSET:
            for c3 in CHARSET:
                for c4 in CHARSET:

                    tail = (c1 + c2 + c3 + c4).encode("utf-8")

                    key = base_key[:] + tail

                    ihdr_data = xor(ihdr_data_cipher, key, ihdr_data_offset)
                    crc = xor(crc_cipher, key, crc_offset)

                    calc_crc = zlib.crc32(b"IHDR" + ihdr_data) & 0xffffffff

                    if crc == calc_crc.to_bytes(4, "big"):
                        return key

    return None


def main():
    with open(FILE_PATH, "rb") as f:
        cipher = f.read()

    # 1️⃣ 16 premiers bytes connus
    base_key = bytearray()
    for i in range(16):
        base_key.append(cipher[i] ^ KNOWN_PLAINTEXT[i])

    print("[+] 16 premiers bytes de clé récupérés")

    # 2️⃣ parallélisation
    cpu = mp.cpu_count()
    chunk = len(CHARSET) // cpu

    ranges = [
        CHARSET[i * chunk:(i + 1) * chunk]
        for i in range(cpu)
    ]

    print(f"[+] Bruteforce ASCII parallèle ({cpu} cœurs)")

    with mp.Pool(cpu) as pool:
        results = pool.starmap(
            worker,
            [(r, cipher, base_key) for r in ranges]
        )

    for key in results:
        if key:
            print("[+] CLÉ TROUVÉE :", key)
            print("[+] CLÉ TEXTE  :", key.decode("utf-8", errors="ignore"))

            decrypted = xor(cipher, key)
            with open("decoded.png", "wb") as f:
                f.write(decrypted)

            print("[+] PNG déchiffré : decoded.png")
            return

    print("[-] Clé non trouvée")


if __name__ == "__main__":
    main()

```
# challenge chinwa bonus

## maker

``` python
data = bytes.fromhex("""""") #put your hex zip value here

# transformation inverse
patched = data.replace(bytes.fromhex("08 00 08 00"), bytes.fromhex("09 00 08 00"))

print(patched.hex())
```

## solver

``` python
data = bytes.fromhex("""""")
# transformation simple
patched = data.replace(bytes.fromhex("09 00 08 00"), bytes.fromhex("08 00 08 00"))
print(patched.hex())
```
