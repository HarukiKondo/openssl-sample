# openssl-sample
opensslについて調査するためのサンプルリポジトリです。

## 試したバージョン

```bash
openssl --version
```

```bash
OpenSSL 3.4.0 22 Oct 2024 (Library: OpenSSL 3.4.0 22 Oct 2024)
```

## 動かした記録(証明書の発行・検証)

- 秘密鍵の生成

    アルゴリズムや鍵長を指定して秘密鍵を作成

    ```bash
    openssl genrsa -out private.key 2048
    ```

    すると `private.key` というファイルができており中身がいかのようになっているはず

    ```txt
    -----BEGIN PRIVATE KEY-----
    .
    .
    .<見せられないよ>
    .
    .
    -----END PRIVATE KEY-----
    ```

- CSRを作成する

    ```bash
    openssl req -new -key private.key -out request.csr
    ```

    上記で作成した秘密鍵を使ってCSRを作成する。

    下記のようにいろいろ聞かれる。

    ```bash
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:JP
    State or Province Name (full name) [Some-State]:Tokyo
    Locality Name (eg, city) []:Toshimaku
    Organization Name (eg, company) [Internet Widgits Pty Ltd]:Test Corp
    Organizational Unit Name (eg, section) []:IT Department
    Common Name (e.g. server FQDN or YOUR name) []:www.test.com
    Email Address []:admin@example.com

    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:test
    An optional company name []:
    ```

    すると `request.csr` というファイルが生成されており、以下のようになっている。

    ```txt
    -----BEGIN CERTIFICATE REQUEST-----
    MIIC8TCCAdkCAQAwgZYxCzAJBgNVBAYTAkpQMQ4wDAYDVQQIDAVUb2t5bzESMBAG
    A1UEBwwJVG9zaGltYWt1MRIwEAYDVQQKDAlUZXN0IENvcnAxFjAUBgNVBAsMDUlU
    IERlcGFydG1lbnQxFTATBgNVBAMMDHd3dy50ZXN0LmNvbTEgMB4GCSqGSIb3DQEJ
    ARYRYWRtaW5AZXhhbXBsZS5jb20wggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEK
    AoIBAQCow+OaVWVrcHo7w9nOY+DXN/1DLazbU8+u7cqLutWYpDOnsElmhdks5vhD
    PKZi/X+fD0XSwxnbkZ9CCv6USsX4fDZDBRzzRLHn52V5nZfQ4/hLDcL56HGlEJLA
    K+3m2avv2SfnmG1s4p7EV8QWgxsTVKdIFgCkfBeHjeasUgEmPMitfeV79hgvXHDV
    uJKlG14Ry0LXbTALH0BZSy5tXR50LVRJCFb0RRDwFYxfFBFWklNjOnCYu6kkbywg
    lY0hxMdb9WqbMPNU6fYbBcDOUEVS8uj3TdrFr9UaYMo9asZUl3VWZdIFzWpbnFqw
    RRD8c3wCiZ+Y3UqsOu4DW55rKbUxAgMBAAGgFTATBgkqhkiG9w0BCQcxBgwEdGVz
    dDANBgkqhkiG9w0BAQsFAAOCAQEAic4iReE11qq09jnKyBXGqC02AvtCstThArqR
    dR+RXQo1C4BoKBBemZugnEC5koOUQlCMTi899IBFes88a3oveKd4oLpcFfWUVIoP
    GfpTq1FEOeK2F4h13ayMGKfVFbOlINlNKpnORBWdg3hQ/CyMxOwT6tXhh6NLOqq9
    kNsD2adx3ve9au8pzBo27sjgfTC6poMWFI3VN65nR/6+62vIBHoyqqtKUgvJZAwp
    /x63x8FLxAsm7UFcDCU+Ur13h8NcfaMoWEwu3K2rWhjztpDUACltssVu07TfR/vT
    UzXOQZvAHpMHDFUCPnIZWmghDRVZ2QnRDIT7QTm72K3Z2W+Yiw==
    -----END CERTIFICATE REQUEST-----
    ```

- 自己署名証明書の発行

    上記で作成したCSRを利用して、証明書を発行する。

    今回はテストなので自己署名証明書を発行する。

    ```bash
    openssl req -x509 -key private.key -in request.csr -out selfsigned.crt -days 365
    ```

    | フィールド名                     | 説明                                     | 例                   |
    |:---------------------------------|:----------------------------------------|:---------------------|
    | Country Name (C)               | 国名を2文字の国コードで入力します。        | JP                  |
    | State or Province Name (ST)    | 都道府県名を入力します。                   | Tokyo               |
    | Locality Name (L)              | 市区町村名を入力します。                   | Minato-ku           |
    | Organization Name (O)          | 会社名や組織名を入力します。               | Example Corp        |
    | Organizational Unit Name (OU)  | 部門名などを入力します。                   | IT Department       |
    | Common Name (CN)               | サーバーのFQDN（完全修飾ドメイン名）を入力します。| www.example.com     |
    | Email Address                  | 担当者のメールアドレスを入力します。         | admin@example.com   |
    | Certificate Validity (days)    | 証明書の有効期間を日数で入力します。         | 365                 |

    無事に処理が完了すると `selfsigned.crt` というファイルが出力される。

    ```txt
    -----BEGIN CERTIFICATE-----
    MIIEDzCCAvegAwIBAgIUNeRWqy8tLhCbc1rj9rznRJtx4J4wDQYJKoZIhvcNAQEL
    BQAwgZYxCzAJBgNVBAYTAkpQMQ4wDAYDVQQIDAVUb2t5bzESMBAGA1UEBwwJVG9z
    aGltYWt1MRIwEAYDVQQKDAlUZXN0IENvcnAxFjAUBgNVBAsMDUlUIERlcGFydG1l
    bnQxFTATBgNVBAMMDHd3dy50ZXN0LmNvbTEgMB4GCSqGSIb3DQEJARYRYWRtaW5A
    ZXhhbXBsZS5jb20wHhcNMjUwMTIyMDUxNTQyWhcNMjYwMTIyMDUxNTQyWjCBljEL
    MAkGA1UEBhMCSlAxDjAMBgNVBAgMBVRva3lvMRIwEAYDVQQHDAlUb3NoaW1ha3Ux
    EjAQBgNVBAoMCVRlc3QgQ29ycDEWMBQGA1UECwwNSVQgRGVwYXJ0bWVudDEVMBMG
    A1UEAwwMd3d3LnRlc3QuY29tMSAwHgYJKoZIhvcNAQkBFhFhZG1pbkBleGFtcGxl
    LmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKjD45pVZWtwejvD
    2c5j4Nc3/UMtrNtTz67tyou61ZikM6ewSWaF2Szm+EM8pmL9f58PRdLDGduRn0IK
    /pRKxfh8NkMFHPNEsefnZXmdl9Dj+EsNwvnocaUQksAr7ebZq+/ZJ+eYbWzinsRX
    xBaDGxNUp0gWAKR8F4eN5qxSASY8yK195Xv2GC9ccNW4kqUbXhHLQtdtMAsfQFlL
    Lm1dHnQtVEkIVvRFEPAVjF8UEVaSU2M6cJi7qSRvLCCVjSHEx1v1apsw81Tp9hsF
    wM5QRVLy6PdN2sWv1Rpgyj1qxlSXdVZl0gXNalucWrBFEPxzfAKJn5jdSqw67gNb
    nmsptTECAwEAAaNTMFEwHQYDVR0OBBYEFMPPwQl+OLgzgne4HAY7vuR2n7p0MB8G
    A1UdIwQYMBaAFMPPwQl+OLgzgne4HAY7vuR2n7p0MA8GA1UdEwEB/wQFMAMBAf8w
    DQYJKoZIhvcNAQELBQADggEBAJHqN8CKYNedslnyehOJ/flT1id+B2Lbp98ycW/g
    NQ7+P2Atm22b9Vt/s2LAh+AdQAmfR55w46NIzjtl8ag6Wok+I+Jxyq80hT3KmHBo
    bM3/4XZ8rk6DtuOotIaxiezBI5ViIxpNqZjd4ighJpaXb/aKsZiLEHv30b/5F5JK
    XR6BKHUg9l91EOZqVwvTQEmDtLPFHZcatwbwuJvjlM7PN/T7cA8Nm1ib4NBpQJOO
    p5W09iZO6ZWTV1BtzTS8U/nupBzE4YSfwZ7KDCA1TYPSKnuMDqu0kTczbzKKp9fG
    GPNmyxUD0hPPDuuj+AFQsyaWzl414iXWRtOjbJow/7SzCgw=
    -----END CERTIFICATE-----
    ```

- CA証明書の発行用の秘密鍵を生成する。

    ```bash
    openssl genpkey -algorithm RSA -out ca.key
    ```

    うまくいけば `ca.key` というファイルが生成されている。

    ```txt
    -----BEGIN PRIVATE KEY-----
    .
    .
    .<見せられないよ>
    .
    .
    -----END PRIVATE KEY-----
    ```

- CA証明書を発行する

    ```bash
    openssl req -x509 -key ca.key -out ca.crt -days 3650
    ```

    以下のようにいろいろ聞かれる

    ```bash
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:JP
    State or Province Name (full name) [Some-State]:Tokyo
    Locality Name (eg, city) []:Toshimaku
    Organization Name (eg, company) [Internet Widgits Pty Ltd]:Test Corp
    Organizational Unit Name (eg, section) []:IT Development
    Common Name (e.g. server FQDN or YOUR name) []:www.test.com
    Email Address []:admin@test.com
    ```

    うまく行けば `ca.crt` というファイルが生成される。  
    ※ これが自己署名証明書の正体

    ```txt
    -----BEGIN CERTIFICATE-----
    MIIECzCCAvOgAwIBAgIUdvZ6gkZurAjTEoPzWa8zrjJE0W4wDQYJKoZIhvcNAQEL
    BQAwgZQxCzAJBgNVBAYTAkpQMQ4wDAYDVQQIDAVUb2t5bzESMBAGA1UEBwwJVG9z
    aGltYWt1MRIwEAYDVQQKDAlUZXN0IENvcnAxFzAVBgNVBAsMDklUIERldmVsb3Bt
    ZW50MRUwEwYDVQQDDAx3d3cudGVzdC5jb20xHTAbBgkqhkiG9w0BCQEWDmFkbWlu
    QHRlc3QuY29tMB4XDTI1MDEyMjA1MjAwMloXDTM1MDEyMDA1MjAwMlowgZQxCzAJ
    BgNVBAYTAkpQMQ4wDAYDVQQIDAVUb2t5bzESMBAGA1UEBwwJVG9zaGltYWt1MRIw
    EAYDVQQKDAlUZXN0IENvcnAxFzAVBgNVBAsMDklUIERldmVsb3BtZW50MRUwEwYD
    VQQDDAx3d3cudGVzdC5jb20xHTAbBgkqhkiG9w0BCQEWDmFkbWluQHRlc3QuY29t
    MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA9dB4lk4eCn5zwauFbCVI
    Z3HEMbjCWxY83qH2Bb9xCb7m6fpXDgLXeSHUhk7ympNPCzEWDg4esGpg56UcH5sd
    Acb8u734vJJ813RbA+OvzbisP9HewS0/55cTb4x4AmLWUzuHTjzveYBNUkjJpZsM
    upsgiL/M9AG3qyq9Lf3jT6406oluYdpPjjRSs3gii0sAB0ky0IWDV9ZD+D4nm1hH
    TiC/s3dzgmGsOU36tT/lOvdm9G9ifRa7jJUzawTWpfr7wfuWKNr8WfUauClI9Bm1
    cem2wnYBMHmt2xKUjdWD4QeeqwyvxIFby33FcSHhKhO9VbSuKlKYslNutoMD2DGL
    nQIDAQABo1MwUTAdBgNVHQ4EFgQUGECuP/znSC/SeIKFjUFFQXZW8XgwHwYDVR0j
    BBgwFoAUGECuP/znSC/SeIKFjUFFQXZW8XgwDwYDVR0TAQH/BAUwAwEB/zANBgkq
    hkiG9w0BAQsFAAOCAQEAnaimgNIswlJz35glsyLRFmjJVE+wqiSJvxgEv/7eMSiV
    J1a23Omdky/ncbv1vTV6rMl7SFYIWY9LUgpaCj8QzsH06o3O4akTRKLUuxS8DcEh
    qZBmlNPirVj4CKEvmOm/zZAy6SVWcemSOf0C1LwQbSlZ/142LrvJger5os+CVcv9
    zccUOQVn0DK5bS6mYDPfX+U3H7F1leGfJQQbdv2qZFLi8h+vYN8evE8uzSL3L8mM
    LfdK5UV1DAbxAPIHAQYxrRwtA2CjOXowQJTxnUpRtPuwNu8j6TQTtnZ+bQJ2HFQ+
    wBb96P59ggd33e5QJQoVktw7NudxRgvbBJWXUDDI7A==
    -----END CERTIFICATE-----
    ```

- 設定ファイルの作成

    CNFファイル(設定ファイル)を作成するといちいち入力しなくて良いので楽

    ```toml
    [ req ]
    distinguished_name = req_distinguished_name
    req_extensions = v3_req
    prompt = no

    [ req_distinguished_name ]
    C = JP
    ST = Tokyo
    L = Minato-ku
    O = Example Corp
    OU = IT Department
    CN = www.example.com
    emailAddress = admin@example.com

    [ v3_req ]
    subjectAltName = @alt_names

    [ alt_names ]
    DNS.1 = www.example.com
    DNS.2 = example.com
    ```

- 設定ファイルを使ってCSRを生成する方法

    ```bash
    openssl req -new -key private.key -out request2.csr -config csr_config.cnf
    ```

    先ほどと同じようにCSRファイルが作成されるが、何も聞かれずに発行される。

- 設定ファイルを使って自己署名証明書を発行する方法

    `selfsigned_config.cnf` というファイルを作成し、以下のように設定する。

    ```toml
    [ req ] 
    distinguished_name = req_distinguished_name 
    x509_extensions = v3_ca 
    prompt = no

    [ req_distinguished_name ]
    C = JP
    ST = Tokyo
    L = Minato-ku
    O = Example Corp
    OU = IT Department
    CN = www.example.com
    emailAddress = admin@example.com

    [ v3_ca ]
    subjectKeyIdentifier = hash
    authorityKeyIdentifier = keyid:always,issuer
    basicConstraints = CA:true
    keyUsage = critical, digitalSignature, cRLSign, keyCertSign
    ```

    そして以下のコマンドを実行

    ```bash
    openssl req -x509 -key private.key -out selfsigned2.crt -days 365 -config selfsigned_config.cnf
    ```

    うまくいけば自己署名証明書のファイルが作成されている。

- 設定ファイルを使ってCA証明書を発行する方法

    以下のように設定した `ca_config.cnf` ファイルを用意する。

    ```toml
    [ req ]
    distinguished_name = req_distinguished_name
    x509_extensions = v3_ca
    prompt = no

    [ req_distinguished_name ]
    C = JP
    ST = Tokyo
    L = Minato-ku
    O = Example Corp
    OU = IT Department
    CN = Example CA
    emailAddress = admin@example.com

    [ v3_ca ]
    subjectKeyIdentifier = hash
    authorityKeyIdentifier = keyid:always,issuer
    basicConstraints = CA:true
    keyUsage = critical, digitalSignature, cRLSign, keyCertSign
    ```

    ```bash
    openssl req -x509 -key ca.key -out ca2.crt -days 3650 -config ca_config.cnf
    ```

    うまく処理されれば、ca2.crtというファイルが生成している。

## コマンドメモ

- 暗号化

    "Hello, OpenSSL!" という文言を暗号化する。

    パスワードの入力が求められる。

    ```bash
    echo "Hello, OpenSSL!" | openssl enc -aes-256-cbc -out encrypted.dat
    ```

- 復号

    暗号化の時に設定したパスワードを使って復号する。

    ```bash
    openssl enc -d -aes-256-cbc -in encrypted.dat
    ```

    以下のように復号できればＯＫ

    ```bash
    "Hello, OpenSSL!"
    ```

- ハッシュ化

    ```bash
    echo -n "Hello, OpenSSL!" | openssl dgst -sha256
    ```

    以下のようになればOK！

    ```bash
    SHA2-256(stdin)= 015225167dbf9b229533874fe779ed094474e82f118c36459c32e8bdca844c56
    ```

- help コマンド

    ```bash
    openssl help
    ```

    ```bash
    help:

    Standard commands
    asn1parse         ca                ciphers           cmp
    cms               crl               crl2pkcs7         dgst
    dhparam           dsa               dsaparam          ec
    ecparam           enc               engine            errstr
    fipsinstall       gendsa            genpkey           genrsa
    help              info              kdf               list
    mac               nseq              ocsp              passwd
    pkcs12            pkcs7             pkcs8             pkey
    pkeyparam         pkeyutl           prime             rand
    rehash            req               rsa               rsautl
    s_client          s_server          s_time            sess_id
    smime             speed             spkac             srp
    storeutl          ts                verify            version
    x509

    Message Digest commands (see the `dgst' command for more details)
    blake2b512        blake2s256        md4               md5
    mdc2              rmd160            sha1              sha224
    sha256            sha3-224          sha3-256          sha3-384
    sha3-512          sha384            sha512            sha512-224
    sha512-256        shake128          shake256          sm3

    Cipher commands (see the `enc' command for more details)
    aes-128-cbc       aes-128-ecb       aes-192-cbc       aes-192-ecb
    aes-256-cbc       aes-256-ecb       aria-128-cbc      aria-128-cfb
    aria-128-cfb1     aria-128-cfb8     aria-128-ctr      aria-128-ecb
    aria-128-ofb      aria-192-cbc      aria-192-cfb      aria-192-cfb1
    aria-192-cfb8     aria-192-ctr      aria-192-ecb      aria-192-ofb
    aria-256-cbc      aria-256-cfb      aria-256-cfb1     aria-256-cfb8
    aria-256-ctr      aria-256-ecb      aria-256-ofb      base64
    bf                bf-cbc            bf-cfb            bf-ecb
    bf-ofb            camellia-128-cbc  camellia-128-ecb  camellia-192-cbc
    camellia-192-ecb  camellia-256-cbc  camellia-256-ecb  cast
    cast-cbc          cast5-cbc         cast5-cfb         cast5-ecb
    cast5-ofb         des               des-cbc           des-cfb
    des-ecb           des-ede           des-ede-cbc       des-ede-cfb
    des-ede-ofb       des-ede3          des-ede3-cbc      des-ede3-cfb
    des-ede3-ofb      des-ofb           des3              desx
    idea              idea-cbc          idea-cfb          idea-ecb
    idea-ofb          rc2               rc2-40-cbc        rc2-64-cbc
    rc2-cbc           rc2-cfb           rc2-ecb           rc2-ofb
    rc4               rc4-40            seed              seed-cbc
    seed-cfb          seed-ecb          seed-ofb          sm4-cbc
    sm4-cfb           sm4-ctr           sm4-ecb           sm4-ofb
    ```

## 用語集

### OpenSSL

OpenSSLは、暗号化通信を行うためのオープンソースのソフトウェアライブラリです。SSLやTLSプロトコルの実装を提供し、インターネット上での安全な通信を可能にします。

簡単にいうと、OpenSSLはデジタル世界の鍵と錠前のようなもので、データを安全に保つためのツールです。

### CSR（Certificate Signing Request）

CSRは、証明書を発行するために認証局（CA）に提出するリクエストです。

CSRには、公開鍵と申請者の情報が含まれています。

簡単にいうと、CSRは「この公開鍵と情報を元に証明書を発行してください」とCAにお願いするための書類のようなものです。例えば、パスポートを申請する際に必要な書類のようなものです。CSRを作成する際には、以下の情報を入力する必要があります。

| フィールド名                | 説明                                  | 例                   |
|:----------------------------|:-------------------------------------|:---------------------|
| Country Name (C)          | 国名を2文字の国コードで入力します。      | JP                  |
| State or Province Name (ST)| 都道府県名を入力します。                 | Tokyo               |
| Locality Name (L)         | 市区町村名を入力します。                  | Minato-ku           |
| Organization Name (O)     | 会社名や組織名を入力します。               | Example Corp        |
| Organizational Unit Name (OU) | 部門名などを入力します。                  | IT Department       |
| Common Name (CN)          | サーバーのFQDN（完全修飾ドメイン名）を入力します。| www.example.com     |
| Email Address             | 担当者のメールアドレスを入力します。         | admin@example.com   |


### 参考文献
1. [初心者向け！はじめてのOpenSSL インストールから証明書作成まで](https://envader.plus/article/390#2.%20%E7%92%B0%E5%A2%83%E6%A7%8B%E7%AF%89)
2. [OpenSSL 公式サイト](https://openssl.org/)
3. [GitHub - OpenSSL](https://github.com/openssl/openssl/tree/master)
4. [インストーラー ダウンロードサイト](https://slproweb.com/products/Win32OpenSSL.html)
