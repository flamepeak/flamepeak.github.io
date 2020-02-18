---
title: Linux random vs urandom 区别
date: 2016-12-29 11:12:08
tags: [Linux]
categories: [Linux]
---

### /dev/random
In Unix-like operating systems, `/dev/random` is a special file that serves as a blocking pseudorandom number generator. It allows access to environmental noise collected from device drivers and other sources.

The generator keeps an estimate of the number of bits of noise in the entropy pool. From this entropy pool random numbers are created. When read, the `/dev/random` device will only return random bytes within the estimated number of bits of noise in the entropy pool. `/dev/random` should be suitable for uses that need very high quality randomness such as one-time pad or key generation. When the entropy pool is empty, reads from `/dev/random` will block until additional environmental noise is gathered.

The intent is to serve as a cryptographically secure pseudorandom number generator, delivering output with entropy as large as possible. This is suggested by the authors for use in generating cryptographic keys for high-value or long-term protection.


### /dev/urandom
A counterpart to `/dev/random` is `/dev/urandom` (unlimited/non-blocking random source) which reuses the internal pool to produce more pseudo-random bits. This means that the call will not block, but the output may contain less entropy than the corresponding read from `/dev/random`. While `/dev/urandom` is still intended as a pseudorandom number generator suitable for most cryptographic purposes, the authors of the corresponding man page note that, theoretically, **there may exist an as-yet-unpublished attack on the algorithm** used by `/dev/urandom`, and that users concerned about such an attack should use `/dev/random` instead.


### Conclusion
#### `/dev/urandom` is best used when:
+ You just want a large file with random data for some kind of testing.
+ You are using the dd command to wipe data off a disk by replacing it with random data.
+ Almost everywhere else where you don’t have a really good reason to use `/dev/random` instead.

#### `/dev/random` is likely to be the better choice when:
+ Randomness is critical to the security of cryptography in your application – one-time pads, key generation.
