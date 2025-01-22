# ECDSAでの練習

## 鍵を作る

```bash
openssl ecparam -name prime256v1 -genkey -noout > root.key
```

以下のコマンドで鍵の中身を確認

```bash
openssl pkey -text -noout < root.key
```

以下のように確認できます。

```bash
Private-Key: (256 bit)
priv:
    0c:2d:b4:f4:8c:27:9e:06:9b:7c:1c:be:50:ff:c8:
    e4:04:1d:e2:fd:da:3b:8c:ab:2d:40:f3:8d:d7:52:
    f8:84
pub:
    04:df:5b:70:f0:96:54:58:35:0a:d8:87:a5:14:c5:
    ec:ac:a7:3a:f1:71:70:b5:ac:5d:53:c3:4a:bf:37:
    ce:ce:e7:8f:96:f9:34:1a:57:1b:ed:28:1e:c0:32:
    68:e8:a5:c5:45:68:36:ca:00:f2:aa:05:6f:98:0b:
    44:bc:d6:8e:44
ASN1 OID: prime256v1
NIST CURVE: P-256
```

## ルート証明書を作成する。

```bash
openssl req -x509 -days 36500 -subj "/CN=Tama Root CA" -key root.key  > root.crt
```

すると `root.crt` というCRTファイルが生成される。

以下のコマンドで証明書の中身が確認できる。

```bash
openssl x509 -text -noout < root.crt
```

以下のように表示されればOKです！

```bash
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            0f:01:f0:e8:97:a0:fc:99:e4:f8:1c:0f:d5:ed:bb:42:ce:d5:37:1f
        Signature Algorithm: ecdsa-with-SHA256
        Issuer: CN=Tama Root CA
        Validity
            Not Before: Jan 22 06:16:24 2025 GMT
            Not After : Dec 29 06:16:24 2124 GMT
        Subject: CN=Tama Root CA
        Subject Public Key Info:
            Public Key Algorithm: id-ecPublicKey
                Public-Key: (256 bit)
                pub:
                    04:df:5b:70:f0:96:54:58:35:0a:d8:87:a5:14:c5:
                    ec:ac:a7:3a:f1:71:70:b5:ac:5d:53:c3:4a:bf:37:
                    ce:ce:e7:8f:96:f9:34:1a:57:1b:ed:28:1e:c0:32:
                    68:e8:a5:c5:45:68:36:ca:00:f2:aa:05:6f:98:0b:
                    44:bc:d6:8e:44
                ASN1 OID: prime256v1
                NIST CURVE: P-256
        X509v3 extensions:
            X509v3 Subject Key Identifier:
                6A:BD:27:A8:D6:69:C4:26:02:E1:B0:55:A0:96:1E:65:A1:5D:E6:85
            X509v3 Authority Key Identifier:
                6A:BD:27:A8:D6:69:C4:26:02:E1:B0:55:A0:96:1E:65:A1:5D:E6:85
            X509v3 Basic Constraints: critical
                CA:TRUE
    Signature Algorithm: ecdsa-with-SHA256
    Signature Value:
        30:44:02:20:26:42:25:68:2d:ee:4a:f4:e2:20:07:fe:a7:09:
        10:5f:de:05:01:33:02:b9:ba:bc:ae:39:d7:6f:c1:12:4c:70:
        02:20:5b:b6:4a:5a:cd:7a:1f:7f:d8:97:6c:6c:55:bf:33:0c:
        8d:f3:27:d3:33:6a:4b:f0:92:5f:91:6b:a4:1b:c3:3e
```

## S/MIME 用の証明書を要求するための鍵ペアを作成する。

```bash
openssl ecparam -name prime256v1 -genkey -noout > signer.key
```

## S/MIME 用の証明書発行要求(CSR)を作成する。

```bash
openssl req -new -subj "/L=Musashi/CN=kodaira.example.com" -key signer.key > signer.csr
```

そうすると `signer.csr` というファイルが作成される。

以下のコマンドでCSRの中身を確認する。

```bash
openssl req -text -noout < signer.csr
```

```bash
Certificate Request:
    Data:
        Version: 1 (0x0)
        Subject: L=Musashi, CN=kodaira.example.com
        Subject Public Key Info:
            Public Key Algorithm: id-ecPublicKey
                Public-Key: (256 bit)
                pub:
                    04:98:fe:ef:e7:8b:6c:8e:34:13:c8:52:84:c1:f4:
                    d8:0d:0e:5a:1e:6b:31:14:54:a2:3f:81:77:dd:e1:
                    22:dc:19:7a:aa:1a:5f:6f:31:a1:14:b5:ea:8c:d1:
                    98:d6:42:4b:6e:c1:80:24:be:17:bf:2a:43:c7:a9:
                    fd:89:58:81:34
                ASN1 OID: prime256v1
                NIST CURVE: P-256
        Attributes:
            (none)
            Requested Extensions:
    Signature Algorithm: ecdsa-with-SHA256
    Signature Value:
        30:44:02:20:78:d8:fe:1c:f7:b3:f4:de:e4:1b:c2:bd:9a:5a:
        0f:f8:c8:62:1b:f4:36:14:71:20:5e:98:12:5b:51:a9:48:52:
        02:20:77:03:76:bc:32:40:6f:b4:02:ef:f0:81:59:23:9e:24:
        5d:80:37:51:37:60:7e:d5:2e:db:44:fc:ee:2e:38:e8
```

## S/MIME 用 CSR をルート証明書で署名する

以下のコマンドでCRT(証明書)を発行する。

```bash
openssl x509 -req -days 36500 -CA root.crt -CAkey root.key -CAcreateserial -extfile signer.conf < signer.csr > signer.crt
```

以下のように出力されればOK!

```bash
Certificate request self-signature ok
subject=L=Musashi, CN=kodaira.example.com
```

以下のコマンドで発行されたS/MIME用の証明書の中身を確認する。

```bash
openssl x509 -text -noout -purpose < signer.crt
```

以下のように出力されればOK!

```bash
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            2f:5e:f5:22:b2:b8:43:fa:40:b2:b2:0c:da:5e:7d:65:42:22:ad:66
        Signature Algorithm: ecdsa-with-SHA256
        Issuer: CN=Tama Root CA
        Validity
            Not Before: Jan 22 06:21:48 2025 GMT
            Not After : Dec 29 06:21:48 2124 GMT
        Subject: L=Musashi, CN=kodaira.example.com
        Subject Public Key Info:
            Public Key Algorithm: id-ecPublicKey
                Public-Key: (256 bit)
                pub:
                    04:98:fe:ef:e7:8b:6c:8e:34:13:c8:52:84:c1:f4:
                    d8:0d:0e:5a:1e:6b:31:14:54:a2:3f:81:77:dd:e1:
                    22:dc:19:7a:aa:1a:5f:6f:31:a1:14:b5:ea:8c:d1:
                    98:d6:42:4b:6e:c1:80:24:be:17:bf:2a:43:c7:a9:
                    fd:89:58:81:34
                ASN1 OID: prime256v1
                NIST CURVE: P-256
        X509v3 extensions:
            X509v3 Basic Constraints: critical
                CA:FALSE
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Extended Key Usage:
                E-mail Protection
            X509v3 Subject Key Identifier:
                5B:64:BA:A1:AF:F0:70:21:95:0E:31:06:9D:E4:EF:90:04:0A:A4:4C
            X509v3 Authority Key Identifier:
                6A:BD:27:A8:D6:69:C4:26:02:E1:B0:55:A0:96:1E:65:A1:5D:E6:85
    Signature Algorithm: ecdsa-with-SHA256
    Signature Value:
        30:45:02:21:00:c8:eb:44:d8:7f:e6:17:5b:01:52:42:40:f2:
        8e:52:d6:90:99:1f:08:91:26:bf:68:e2:c9:26:a8:e7:ec:ba:
        dd:02:20:70:2b:ab:25:a3:d1:36:49:fd:11:b9:dd:ac:ec:8b:
        3c:ad:36:28:24:23:9c:f8:c2:19:fa:e6:42:ef:e3:31:ef
Certificate purposes:
SSL client : No
SSL client CA : No
SSL server : No
SSL server CA : No
Netscape SSL server : No
Netscape SSL server CA : No
S/MIME signing : Yes
S/MIME signing CA : No
S/MIME encryption : Yes
S/MIME encryption CA : No
CRL signing : No
CRL signing CA : No
Any Purpose : Yes
Any Purpose CA : Yes
OCSP helper : Yes
OCSP helper CA : No
Time Stamp signing : No
Time Stamp signing CA : No
Code signing : No
Code signing CA : No
```

Certificate purposes(証明書の用途)の部分で

- S/MIME signing: Yes (電子メールの署名として使用可能)
- S/MIME encryption: Yes (電子メールの暗号化として使用可能)
- Any Purpose: Yes (汎用的な目的で使用可能)
- OCSP helper: Yes (OCSP レスポンス用に使用可能)

になっていることが確認できる。

その他の用途 (SSL、コード署名など) は「No」となっており、これらには使用できません。