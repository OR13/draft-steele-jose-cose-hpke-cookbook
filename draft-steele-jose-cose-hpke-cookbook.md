---
title: "JOSE-COSE HPKE Cookbook"
category: info
docname: draft-steele-jose-cose-hpke-cookbook-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Security"
workgroup: "Javascript Object Signing and Encryption"
keyword:
 - JOSE
 - COSE
 - HPKE
venue:
  group: "Javascript Object Signing and Encryption"
  type: "Working Group"
  mail: "jose@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/jose/"
  github: "OR13/draft-steele-jose-cose-hpke-cookbook"
  latest: "https://OR13.github.io/draft-steele-jose-cose-hpke-cookbook/draft-steele-jose-cose-hpke-cookbook.html"

author:
 -
    fullname: "Orie Steele"
    organization: Transmute
    email: "orie@transmute.industries"

normative:
  I-D.draft-rha-jose-hpke-encrypt: JOSE-HPKE
  I-D.draft-ietf-cose-hpke: COSE-HPKE

informative:
  RFC8792:
  RFC7516: JOSE-JWE
  RFC9052: COSE
  RFC7517: JOSE-JWK
  RFC7520:
  RFC9180:

--- abstract

This document contains a set of examples using JSON Object Signing and Encryption (JOSE), CBOR Object Signing and Encryption (COSE) and Hybrid Public Key Encryption (HPKE) to protect data.
These examples are meant to coverage the edge cases of both JOSE and COSE, including different structures for single and multiple recipients, external additional authenticated data, and key derivation function (KDF) context binding.

--- middle

# Introduction

This document is inspired by Examples of Protecting Content Using JSON Object Signing and Encryption (JOSE) {{RFC7520}}.

JOSE support for HPKE is described in {{-JOSE-HPKE}}.

COSE support for HPKE is described in {{-COSE-HPKE}}.

Both drafts are still work in progress.

The current set of examples are incomplete.

# Terminology

{::boilerplate bcp14-tagged}

The term "JSON Web Key (JWK)" is defined in {{-JOSE-JWK}}.

The term "COSE Key" is defined in {{-COSE}}.

The terms "JSON Web Encryption (JWE)", "Direct Key Agreement", "Key Agreement with Key Wrapping",  "JWE Compact Serialization" and "General JWE JSON Serialization" are defined in {{-JOSE-JWE}}.

The terms "Direct Encryption", and "Key Agreement with Key Wrap" are defined in {{-COSE}}.

The term "encapsulated key" is defined in {{RFC9180}}.

This document does not define any new terms for JOSE or COSE.

# Overview

Note that JSON (JavaScript Object Notation) and EDN (Extended Diagnostic Notation) may not exactly match the bytes provided for each example.

The hexadecimal encoded binary messages are the source of truth, the JSON and EDN examples are for readability.

NOTE: '\' line wrapping per {{RFC8792}} in HTTP examples.


## Private Key

This section provides private key representations that are used throught the following sections.

Public key representations for these keys are left as an excercise for the reader.

The keys in this section are restricted to a single algorithm in both JOSE and COSE: `HPKE-Base-P256-SHA256-AES128GCM`.

Additional algorithms and their private key representations may be provided in future versions of this draft.

### application/jwk+json

This section provides a JSON Web Key for `HPKE-Base-P256-SHA256-AES128GCM`.


~~~
7b0a2020226b6964223a202275726e3a696574663a706172616d733a6f617574683a6a\
776b2d7468756d627072696e743a7368612d3235363a374a76784b756a755770596c4b\
49455f75726479543749457165345030635a33476d3163364554526c5130222c0a2020\
22616c67223a202248504b452d426173652d503235362d5348413235362d4145533132\
3847434d222c0a2020226b7479223a20224543222c0a202022637276223a2022502d32\
3536222c0a20202278223a2022646f37595f507a6f6c355f47326650686f50676f4f6f\
306b6b34632d386745386952345958715449596d34222c0a20202279223a2022616b6a\
4d396b56327a317057364b544c657148426b6d6d355332496d5f4f794f37526a527267\
6d66316455222c0a20202264223a20224b583649304a7554664430467231703243646c\
5239636f6e324851737759344673716a33437a533261704d220a7d
~~~
{: #private-key-jwk-hex align="left" title="JSON Web Key Bytes"}

~~~json
{
  "kid": "urn:ietf:params:oauth:jwk-thumbprint:sha-256:7JvxKujuWpYlKIE_urdyT7IEqe4P0cZ3Gm1c6ETRlQ0",
  "alg": "HPKE-Base-P256-SHA256-AES128GCM",
  "kty": "EC",
  "crv": "P-256",
  "x": "do7Y_Pzol5_G2fPhoPgoOo0kk4c-8gE8iR4YXqTIYm4",
  "y": "akjM9kV2z1pW6KTLeqHBkmm5S2Im_OyO7RjRrgmf1dU",
  "d": "KX6I0JuTfD0Fr1p2CdlR9con2HQswY4Fsqj3CzS2apM"
}
~~~
{: #private-key-jwk align="left" title="JSON Web Key"}

### application/cose-key

This section provides a COSE Key for `HPKE-Base-P256-SHA256-AES128GCM`.

~~~
a702784d75726e3a696574663a706172616d733a6f617574683a636b743a7368612d3\
235363a4b565f3339384668506158773761514d55595f4e5a497a7364416a7341544b\
7a73765a6f333058424e514d03182301022001215820768ed8fcfce8979fc6d9f3e1a\
0f8283a8d2493873ef2013c891e185ea4c8626e2258206a48ccf64576cf5a56e8a4cb\
7aa1c19269b94b6226fcec8eed18d1ae099fd5d5235820297e88d09b937c3d05af5a7\
609d951f5ca27d8742cc18e05b2a8f70b34b66a93
~~~
{: #private-key-cose-key-bytes align="left" title="COSE Key Bytes"}

~~~ cbor-diag
{
  / kid / 2: "urn:ietf:params:oauth:ckt:sha-256:KV_398FhPaXw7aQMUY_NZIzsdAjsATKzsvZo30XBNQM",
  / alg: ES256 /  3: 35,
  / kty: EC2   /  1: 2,
  / crv: P-256 / -1: 1,
  / x / -2: h'768ed8fcfce8979fc6d9f3e1a0f8283a8d2493873ef2013c891e185ea4c8626e',
  / y / -3: h'6a48ccf64576cf5a56e8a4cb7aa1c19269b94b6226fcec8eed18d1ae099fd5d5',
  / d / -4: h'297e88d09b937c3d05af5a7609d951f5ca27d8742cc18e05b2a8f70b34b66a93'
}
~~~
{: #private-key-cose-key-diag align="left" title="COSE Key Diagnostic"}

## Direct Encryption

JOSE and COSE HPKE both support a "Direct Encryption Mode", where HPKE encrypt a plaintext message and optional additional authenticated data directly to a recipient public key.

Note that HPKE Direct Encryption is not exactly the same as "Direct Encryption" as described in {{-JOSE-JWE}} and {{-COSE}}.

In this section we provide direct encryption examples to the private keys in the previous section.

~~~
⌛ My lungs taste the air of Time Blown past falling sands ⌛
~~~
{: #direct-encryption-message align="left" title="Direct Encryption Message"}

~~~
✨ It’s a dangerous business, Frodo, going out your door. ✨
~~~
{: #direct-encryption-addition-authenticated-data align="left" title="Direct Encryption AAD"}

### application/jose+json

~~~
7b0a20202270726f746563746564223a202265794a68624763694f694a6b615849694c\
434a6c626d4d694f694a49554574464c554a6863325574554449314e69315453454579\
4e545974515556544d54493452304e4e496977695a584272496a7037496d7430655349\
36496b564c496977695a5773694f694a43526d466a593073795546687859586c615130\
3078526b68366245307a4d6a46584e3268334d6a56485557677751544974616e426163\
553479516e564654575253595746464d7a5a7856306b315a5446766346687161474e4f\
56575234643164435a334e5a596b4e7056456f7a65453579537a5169665830222c0a20\
2022616164223a20223470796f49456c30346f435a6379426849475268626d646c636d\
39316379426964584e70626d567a63797767526e4a765a473873494764766157356e49\
47393164434235623356794947527662334975494f4b637141222c0a20202263697068\
657274657874223a20226449326b4b2d6634787634446d45647848706e4f474130732d\
695f4338735059444d3674664a456f6a6c6b5351355865586f41755151566d587a6479\
754c6255476a69416a6967744e3455414a556b556f2d55673430695771435632544662\
326e586579566436456167220a7d
~~~
{: #direct-ciphertext-jose-bytes align="left" title="JOSE Direct Encryption Bytes"}

~~~json
{
  "protected": "eyJhbGciOiJkaXIiLCJlbmMiOiJIUEtFLUJhc2UtUDI1Ni1TSEEyNTYtQUVTMTI4R0NNIiwiZXBrIjp7Imt0eSI6IkVLIiwiZWsiOiJCRmFjY0syUFhxYXlaQ00xRkh6bE0zMjFXN2h3MjVHUWgwQTItanBacU4yQnVFTWRSYWFFMzZxV0k1ZTFvcFhqaGNOVWR4d1dCZ3NZYkNpVEozeE5ySzQifX0",
  "aad": "4pyoIEl04oCZcyBhIGRhbmdlcm91cyBidXNpbmVzcywgRnJvZG8sIGdvaW5nIG91dCB5b3VyIGRvb3IuIOKcqA",
  "ciphertext": "dI2kK-f4xv4DmEdxHpnOGA0s-i_C8sPYDM6tfJEojlkSQ5XeXoAuQQVmXzdyuLbUGjiAjigtN4UAJUkUo-Ug40iWqCV2TFb2nXeyVd6Eag"
}
~~~
{: #direct-ciphertext-json align="left" title="JOSE Direct Encryption"}

~~~json
{
  "alg": "dir",
  "enc": "HPKE-Base-P256-SHA256-AES128GCM",
  "epk": {
    "kty": "EK",
    "ek": "BFaccK2PXqayZCM1FHzlM321W7hw25GQh0A2-jpZqN2BuEMdRaaE36qWI5e1opXjhcNUdxwWBgsYbCiTJ3xNrK4"
  }
}
~~~
{: #direct-ciphertext-json-decoded-protected-header align="left" title="JOSE Direct Encryption Decoded Protected Header"}

### application/cose

~~~
d08344a1011823a204784d75726e3a696574663a706172616d733a6f617574683a636\
b743a7368612d3235363a4b565f3339384668506158773761514d55595f4e5a497a73\
64416a7341544b7a73765a6f333058424e514d235841048024424acb8a77edfbc461b\
c4947e5d3ac3e5d31bc5f8a9fe71d0e4ef79f4b27943418a3817a14f6de06b43b98ce\
ac551d4cf47e888293a271a9117db7abf19a584f693181d2479aca080d3e71ce37624\
10665415cab2b831cac4fef97105cdec3b3a71de61019ceb0431d1637de54bab7f855\
fe8bae7bd9ba5cc12a20d458baf08f4cf861da4a831964745eceb97f3bbf
~~~
{: #direct-ciphertext-cose-bytes align="left" title="COSE Direct Encryption Bytes"}

~~~ cbor-diag
16([ / Single Recipient Encryption /
  h'a1011823', / Protected Header
  { / Unprotected Header /
    / kid / 4: "urn:ietf:params:oauth:ckt:sha-256:KV_398FhPaXw7aQMUY_NZIzsdAjsATKzsvZo30XBNQM",
    / ek / -4: h'048024424acb8a77edfbc461bc4947e5d3ac3e5d31bc5f8a9fe71d0e4ef79f4b27943418a3817a14f6de06b43b98ceac551d4cf47e888293a271a9117db7abf19a'
  },
  h'693181d2479aca080d3e71ce3762410665415cab2b831cac4fef97105cdec3b3a71de61019ceb0431d1637de54bab7f855fe8bae7bd9ba5cc12a20d458baf08f4cf861da4a831964745eceb97f3bbf'
])
~~~
{: #direct-ciphertext-cose-diag align="left" title="COSE Direct Encryption Diagnostic"}

Note that JOSE and COSE transport the encapsulated key "ek" differently.

## Key Encryption

JOSE and COSE HPKE both support a "Key Encryption Mode", where HPKE encrypt a content encryption key to a recipient public key, and a plaintext message and optional additional authenticated with the content encryption key.

Note that HPKE Key Encryption Mode is not exactly the same as "Key Agreement with Key Wrap" as described in {{-JOSE-JWE}} and {{-COSE}}.

In this section we provide direct encryption examples to the private keys in the previous section.

~~~
⌛ My lungs taste the air of Time Blown past falling sands ⌛
~~~
{: #key-encryption-message align="left" title="Key Encryption Message"}

~~~
✨ It’s a dangerous business, Frodo, going out your door. ✨
~~~
{: #key-encryption-addition-authenticated-data align="left" title="Key Encryption AAD"}

### application/jose+json

~~~
7b0a20202270726f746563746564223a202265794a6c626d4d694f694a424d5449345\
2304e4e496977695a584272496a7037496d743065534936496b564c496977695a5773\
694f694a43526b524862456f77533045774e3364305a454668543270445644557a564\
5314c546e4266595578504f4467794d30525954314d32533370525557396e63555256\
576a6832515768335355396e513035345a574d78545735575345563361556c4d4d6e7\
07963334a50526d786b5255746865453069665830222c0a202022656e637279707465\
645f6b6579223a202274774d563367697a4c7a5f74674455425831564b2d505a6a6b2\
d503737415845745442445a44444c423973222c0a2020226976223a20223178716256\
53586e6654524253675947222c0a20202263697068657274657874223a20225a5f546\
6517a684643355a2d394a2d4f6b41676632784535364d75584b5f6b69793569594935\
4a444e6c76474e794338534856754a7956346e5a6e7867396e624953525a7561477a7\
8594971355a745469614434222c0a202022746167223a202259696844336762695a69\
47624458662d5851434d7241222c0a202022616164223a20223470796f49456c30346\
f435a6379426849475268626d646c636d39316379426964584e70626d567a63797767\
526e4a765a473873494764766157356e4947393164434235623356794947527662334\
975494f4b637141220a7d
~~~
{: #wrap-ciphertext-jose-bytes align="left" title="JOSE Key Encryption Bytes"}

~~~json
{
  "protected": "eyJlbmMiOiJBMTI4R0NNIiwiZXBrIjp7Imt0eSI6IkVLIiwiZWsiOiJCRkRHbEowS0EwN3d0ZEFhT2pDVDUzVE1LTnBfYUxPODgyM0RYT1M2S3pRUW9ncURVWjh2QWh3SU9nQ054ZWMxTW5WSEV3aUlMMnpyc3JPRmxkRUtheE0ifX0",
  "encrypted_key": "twMV3gizLz_tgDUBX1VK-PZjk-P77AXEtTBDZDDLB9s",
  "iv": "1xqbVSXnfTRBSgYG",
  "ciphertext": "Z_TfQzhFC5Z-9J-OkAgf2xE56MuXK_kiy5iYI5JDNlvGNyC8SHVuJyV4nZnxg9nbISRZuaGzxYIq5ZtTiaD4",
  "tag": "YihD3gbiZiGbDXf-XQCMrA",
  "aad": "4pyoIEl04oCZcyBhIGRhbmdlcm91cyBidXNpbmVzcywgRnJvZG8sIGdvaW5nIG91dCB5b3VyIGRvb3IuIOKcqA"
}
~~~
{: #wrap-ciphertext-jose-json align="left" title="JOSE Key Encryption JSON"}

~~~json
{
  "enc": "A128GCM",
  "epk": {
    "kty": "EK",
    "ek": "BFDGlJ0KA07wtdAaOjCT53TMKNp_aLO8823DXOS6KzQQogqDUZ8vAhwIOgCNxec1MnVHEwiIL2zrsrOFldEKaxM"
  }
}
~~~
{: #key-encryption-protected-header align="left" title="JOSE Key Encryption Decoded Protected Header"}

Note that the ephemeral public key (epk) is present in the protected header due to there only being a single recipient for this message.

### application/cose

~~~
d8608443a10101a10550335552a987fd47dc85016ccc760bb541584fa0d7678a14400\
1cc48d1ff514545df9e0da6b696a8ed3bceb529b78ba86c26bef93767d07b0111a48e\
38dd79bfe9c351d6508ec2805b30ea16f6b46156e3ba9cffc11a39c311554970c7bda\
a40d4c1818344a1011823a204784d75726e3a696574663a706172616d733a6f617574\
683a636b743a7368612d3235363a4b565f3339384668506158773761514d55595f4e5\
a497a7364416a7341544b7a73765a6f333058424e514d235841044e733517b62d8cf9\
00d3d84606f8907bea0e3481c123359197782f0869b36c0efe13e76ae4740bcaf2f7a\
f1e03523efe1b98dc4b81a94d45d9dfea583ef14e0f582000871c2c5f6a8b73aab9cf\
df26953dc026036f00a08b61d903dd4a72e9c01229
~~~
{: #wrap-ciphertext-cose-bytes align="left" title="COSE Key Encryption Bytes"}

~~~ cbor-diag
96([ / Multiple Recipient Encrypted Message /
  h'a10101',  / Protected Header /
  {
    / IV / 5: h'335552a987fd47dc85016ccc760bb541'
  },
  / Ciphertext / h'a0d7678a144001cc48d1ff514545df9e0da6b696a8ed3bceb529b78ba86c26bef93767d07b0111a48e38dd79bfe9c351d6508ec2805b30ea16f6b46156e3ba9cffc11a39c311554970c7bdaa40d4c1',
  [ / Recipients /
    [ / Recipient 0 /
      h'a1011823',  / Recipient Protected Header /
      { / Recipient Unprotected Header /
        / kid /  4: "urn:ietf:params:oauth:ckt:sha-256:KV_398FhPaXw7aQMUY_NZIzsdAjsATKzsvZo30XBNQM",
        / ek  / -4: h'044e733517b62d8cf900d3d84606f8907bea0e3481c123359197782f0869b36c0efe13e76ae4740bcaf2f7af1e03523efe1b98dc4b81a94d45d9dfea583ef14e0f'
      },
      / encrypted key / h'00871c2c5f6a8b73aab9cfdf26953dc026036f00a08b61d903dd4a72e9c01229'
    ]
  ]
])
~~~
{: #wrap-ciphertext-cose-diag align="left" title="COSE Key Encryption Diagnostic"}


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
