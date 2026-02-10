**cheat sheet OpenSSL**

---

````md
# 🛡️ OpenSSL Cheat Sheet – Cryptographie & Sécurité

## 🔢 Génération de données aléatoires

```bash
openssl rand -base64 182
````

> Génère **182 octets aléatoires** encodés en **Base64**

```bash
openssl rand -hex 32
```

> Génère **32 octets aléatoires** en **hexadécimal**

---

## 🔤 Encodage / Décodage Base64

```bash
openssl enc -a -in doc_clair.txt -out doc_enc.txt
```

> Encode `doc_clair.txt` en **Base64** et écrit le résultat dans `doc_enc.txt`

```bash
openssl enc -d -in doc_enc.txt
```

> Décode un fichier **Base64**

---

## 🔐 Chiffrement symétrique (DES)

```bash
openssl enc -e -des-cbc -k toto -in doc_clair.txt -out doc_enc.txt
```

> Chiffre le fichier avec **DES-CBC** et la clé `toto`

```bash
openssl enc -d -des-cbc -k toto -in doc_enc.txt -out doc_dec.txt
```

> Déchiffre le fichier DES

---

## 🔑 Génération de clés RSA

```bash
openssl genrsa -out priv_key.pem 2048
```

> Génère une **clé privée RSA 2048 bits**

```bash
openssl genrsa -des -out priv_enc_key.pem 2048
```

> Génère une **clé privée RSA chiffrée par DES**

```bash
openssl rsa -pubout -in priv_key.pem -out pub_key.pem
```

> Extrait la **clé publique** depuis la clé privée

---

## 🔐 Chiffrement asymétrique RSA

### Chiffrement avec clé publique

```bash
openssl pkeyutl -encrypt -pubin -inkey pub_key.pem -in doc_clair.txt -out doc_enc.pem
```

> Chiffre un fichier avec la **clé publique RSA**

### Déchiffrement avec clé privée

```bash
openssl pkeyutl -decrypt -inkey priv_key.pem -in doc_enc.pem
```

> Déchiffre avec la **clé privée RSA**

---

## 🧮 Hachage & HMAC

```bash
openssl dgst doc_clair.txt
```

> Hash **MD5** (valeur par défaut)

```bash
openssl dgst -sha512 doc_clair.txt
```

> Hash **SHA-512**

```bash
openssl dgst -sha1 -hmac "cle_secret_partagee" doc_clair.txt
```

> **HMAC-SHA1** (protection contre MITM)

---

## ✍️ Signature numérique (DSA)

### Génération des clés DSA

```bash
openssl dsaparam -genkey 2048 -out priv_dsa.pem
```

> Génère une **clé privée DSA**

```bash
openssl dsa -pubout -in priv_dsa.pem -out pub_dsa.pem
```

> Génère la **clé publique DSA**

---

### Signature d’un document

```bash
openssl dgst -sha256 -sign priv_dsa.pem -out doc_sign doc_clair.txt
```

> Signe le document avec **SHA-256 + DSA**

---

### Vérification de signature

```bash
openssl dgst -sha256 -verify pub_dsa.pem -signature doc_sign doc_clair.txt
```

> Vérifie l’authenticité et l’intégrité du document

