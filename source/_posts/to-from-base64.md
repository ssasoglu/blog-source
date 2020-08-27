---
title: "TIL: To/From Base64"
date: 2020-05-20 21:49:43
tags:
- bash
- terminal
- base64
- encoding
- decoding
---

Base64 encode file in terminal:

```bash
base64 -i <in-file> -o <outfile>
```

Base64 encode input string in terminal:

```bash
# -n prevents adding line feed to the end of the string
echo -n 'input string' | base64
```

Decode base64 string:
```bash
# -n prevents adding line feed to the end of the string
echo -n 'InputBase64String' | base64 --decode
```

[<- Back to all TILs](../../19/til/)