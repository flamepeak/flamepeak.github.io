---
title: 2FA 双因子验证
date: 2020-09-03 09:00:39
tags: [cryptography]
categories: [Cryptography]
---


### 基础知识

OTP（One Time Passowrd）可以分为两种，HOTP和TOTP，即**HMAC-based One Time Password** and **Time-based OTP**。 TOPT并不是一种完全不同的模式，而是HOTP的增强版。

HOTP算法在 [RFC4226](https://tools.ietf.org/html/rfc4226)中进行了描述，大约有35页，包含了格式描述、例子等。

HMAC stands for **Hash-based Message Authentication Code**.

**MAC** is a way of proving that a message came from the expected sender and not someone else. **MAC algorithm** produces a **MAC tag** using a secret key that is only known to the sender and the receiver. 消息认证码的输入包括任意长度的消息和一个发送者与接收者之间之间共享的密钥，它可以输出固定长度的数据，这个数据称为MAC值。

**MAC** itself is not a specific algorithm, but rather a term that refers to one. 

**HMAC**, in turn, IS a specific implementation. Or, to be more precise — HMAC-*X*, where *X* is one of the crypthographic hash functions. Now, HMAC takes two parameters — a secret key and your message, mixes them together in a special way, applies a hash function of your choice twice and produces a MAC tag.

[参考：认证](https://flamepeak.com/2016/11/23/tu-jie-mi-ma-ji-shu-authentication20161123/)

根据HOTP规范，HOTP的计算基于两个值：K（客户端和服务器端的密钥secret key）和C（Counter）。共享密钥K必须大于128位，160位更好，它在你初始化2FA时生成。Counter是一个8字节的值，它在服务器和客户端之间同步，当你生成密码时它会持续更新。

```
In HOTP, client counter is incremented each time a new password is generated and server counter — each time a password is validated. Since we can generate passwords without actually using them, server allows counter values to be a bit ahead of what the current one is but only within a certain window. If you played with your OTP token too much and it was an HOTP algorithm — you'll have to resync your token with server.
```



RFC4226定义的HOTP如下：

```
HOTP(K,C) = Truncate(HMAC-SHA-1(K,C))
```

So, K is predictably used as our secret key and **C**ounter is used as the message.

### 流程与计算

- Generate HMAC-SHA1 value from our **K** and **C** parameters. This will be a 20-byte string
- Extract 4 bytes from that string in a specific way
- Convert those bytes into a number, divide that number by 10^n, where n = number of digits in the OTP and take the remainder. Usually n=6.



**首先，计算HMAC-sha1**

在Hash计算时，可以使用 [**Web Crypto API**](https://www.w3.org/TR/WebCryptoAPI/)，这也要求你的脚本必须运行在使用HTTPS的网站上。幸运的是，在Firefox中你可以使用任何环境，而不用重复造轮子或者引入第三方库。

Crypto API位于`window.crypto.subtle`。

首先，我们需要使用`importKey`方法，因为我们要引入自己的key，而不是在浏览器中生成。它使用5个参数：

```
importKey(
    format
    keyData,
    algorithm,
    extractable,
    usages
);
```

在我们例子中:

- `format` will be `'raw'`, meaning that we will supply the key as raw bytes in an `ArrayBuffer`.
- `keyData` is the ArrayBuffer mentioned above. We'll talk about generating it in a bit
- `algorithm` will be `HMAC-SHA1` as per OTP spec. This has to be an [HmacImportParams](https://developer.mozilla.org/en-US/docs/Web/API/HmacImportParams) object
- `extractable` can be false, since we don't plan to export the key
- And finally, of all possible `usages` we will only need `'sign'`

Our secret key will be a long random string. In reality it could be a sequence of bytes that are not necessarily printable。

为了把密钥转换成`ArrayBuffer`，我们将使用`TextEncoder`。代码如下：

```java
const encoder = new TextEncoder('utf-8');
const secretBytes = encoder.encode(secret);
```

汇总一下：

```java
 const Crypto = window.crypto.subtle;
  const encoder = new TextEncoder('utf-8');
  const secretBytes = encoder.encode(secret);

  const key = await Crypto.importKey(
    'raw',
    secretBytes,
    { name: 'HMAC', hash: { name: 'SHA-1' } },
    false,
    ['sign']
  );	
```



接下来，我们处理Counter：

根据规范，Counter应该为8字节，同样`ArrayBuffer`格式。To convert it into this form we will first use a trick that is usually used to pad numbers with leading zeroes in JS and then put each individual byte into the `ArrayBuffer` using a `DataView`. Please note that according to spec all binary data is treated as **big endian** (most significant bit first).

```java
function padCounter(counter) {
  const buffer = new ArrayBuffer(8);
  const bView = new DataView(buffer);

  const byteString = '0'.repeat(64); // 8 bytes
  const bCounter = (byteString + counter.toString(2)).slice(-64);

  for (let byte = 0; byte < 64; byte += 8) {
    const byteValue = parseInt(bCounter.slice(byte, byte + 8), 2);
    bView.setUint8(byte / 8, byteValue);
  }

  return buffer;
}
```

With that in place — we are ready to sign! To do that we will just need to use `sign` function of `SubtleCrypto`.

```java
const counterArray = padCounter(counter);
const HS = await Crypto.sign('HMAC', key, counterArray);
```



**第二步，生成4字节字符串**

Generate a 4-byte string (Dynamic Truncation)  
Let Sbits = DT(HS) // DT, defined below,  
// returns a 31-bit string  

```java
function DT(HS) {
  // First we take the last byte of our generated HS and extract last 4 bits out of it.
  // This will be our _offset_, a number between 0 and 15.
  const offset = HS[19] & 0b1111;

  // Next we take 4 bytes out of the HS, starting at the offset
  const P = ((HS[offset] & 0x7f) << 24) | (HS[offset + 1] << 16) | (HS[offset + 2] << 8) | HS[offset + 3]

  // Finally, convert it into a binary string representation
  const pString = P.toString(2);

  return pString;
}
```

Now we only need to convert what we got from DT to an integer and off we go to stage 3.

```java
function truncate(uKey) {
  const Sbits = DT(uKey);
  const Snum = parseInt(Sbits, 2);

  return Snum;
}
```



**第三步，获得结果**

Stage 3 is really small. All we need to do now is to divide our resulting number by `10 ** (number of digits in OTP)` and take the remainder of that division. This way we basically cut last N digits from the resulting number. The spec mentions that you must extract at least 6 digits and possibly 7 or 8. Theoretically since it's a 31-bit integer you can extract up to 9 digits, but in reality I've never seen anything over 6. Have you?

```java
async function generateHOTP(secret, counter) {
  const key = await generateKey(secret, counter);
  const uKey = new Uint8Array(key);

  const Snum = truncate(uKey);
  // Make sure we keep leading zeroes
  const padded = ('000000' + (Snum % (10 ** 6))).slice(-6);

  return padded;
}
```



**TOTP**

当你打开流行的2FA app，可以看到倒计时，那就是TOTP。

**Time-based** means that instead of a static counter, current time is used as a moving factor. Or, to be precise, current *time step*. To calculate this *time step* we take current unix epoch time (number of milliseconds since 00:00:00 UTC on 1 January 1970) and divide it by a *time window* (usually 30 seconds). Server usually allows for a bit of time drift to account for imperfections in time sync — about 1 step forwards and backwards depending on the configuration.

关于TOTP，可以查阅[RFC6238](https://tools.ietf.org/html/rfc6238)。

Due to time-based scheme being an extension over original algorithm, no changes to the original implementation are required. We will use `requestAnimationFrame` and check on every tick if we are still inside the time window. If we are not — we will calculate a new time step (counter) and regenerate HOTP with it. Omitting all the administrative code it will look roughly like this:

```java
let stepWindow = 30 * 1000; // 30 seconds in ms
let lastTimeStep = 0;

const updateTOTPCounter = () => {
  const timeSinceStep = Date.now() - lastTimeStep * stepWindow;
  const timeLeft = Math.ceil(stepWindow - timeSinceStep);

  if (timeLeft > 0) {
    return requestAnimationFrame(updateTOTPCounter);
  }

  timeStep = getTOTPCounter();
  lastTimeStep = timeStep;
    <...update counter and regenerate...>
  requestAnimationFrame(updateTOTPCounter);
}
```



### 二维码

通常，我们设置2FA时需要扫描二维码，其中包含我们需要的信息：密钥、使用的OTP算法、帐户名、发行者、位数。

浏览器中二维码扫描可以[参考](https://dev.to/al_khovansky/intro-to-screen-capture-api-scanning-qr-codes-bgi)

二维码中的2FA地址格式通常如下：

```
otpauth://TYPE/LABEL?PARAMETERS
```

例如：

```
otpauth://totp/label?secret=oyu55d4q5kllrwhy4euqh3ouw7hebnhm5qsflfcqggczoafxu75lsagt&algorithm=SHA1&digits=6&period=30
```

解析地址代码如下：

```java
const setupFromQR = data => {
  const url = new URL(data);

  // drop the "//" and get TYPE and LABEL
  const [scheme, label] = url.pathname.slice(2).split('/');
  const params = new URLSearchParams(url.search);

  const secret = params.get('secret');
  let counter;

  if (scheme === 'hotp') {
    counter = params.get('counter');
  } else {
    stepWindow = parseInt(params.get('period'), 10) * 1000;
    counter = getTOTPCounter();
  }
}
```









参考：

1. https://dev.to/al_khovansky/generating-2fa-one-time-passwords-in-js-using-web-crypto-api-1hfo
2. 



