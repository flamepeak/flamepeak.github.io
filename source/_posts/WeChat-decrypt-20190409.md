---
title: 微信数据库解密与实现
date: 2019-04-09 10:27:55
tags: [WeChat]
categories: [Cryptography]
---

---

### 微信PC版

本文都是参考大佬的文章，自己实现了一下，膜拜大佬。在此，描述一下全流程，以及一些坑

首次，核心代码在看雪论坛：[微信数据库解密算法](https://bbs.pediy.com/thread-222652.htm), 作者称`这里提供拿到密码之后的解密算法，兼容安卓上的、pc上的微信数据库。（免去到处找sqlcipher，又怕版本错误的问题）`

```C++
#include "stdafx.h"
#include <Windows.h>
#include <openssl/rand.h>
#include <openssl/evp.h>
#include <openssl/aes.h>
#include <openssl/hmac.h>



//#define ANDROID_WECHAT

#define SQLITE_FILE_HEADER "SQLite format 3" //length == 16
#define IV_SIZE 16
#define HMAC_SHA1_SIZE 20
#define KEY_SIZE 32

#ifndef ANDROID_WECHAT
#define DEFAULT_PAGESIZE 4096
#define DEFAULT_ITER 64000
#else
#define NO_USE_HMAC_SHA1
#define DEFAULT_PAGESIZE 1024
#define DEFAULT_ITER 4000
#endif

//安卓端这里密码是7位，pc端是经过算法得到的32位pass。
//下面附pc端拿密码的OD图
unsigned char pass[] = { 0x20, 0x5B, 0xD4, 0xF8, 0xCE, 0x57, 0x4E, 0x52, 0xA4, 0xF2, 0x63, 0x95, 0xA1, 0xFE, 0x4D, 0x4B, 0x91, 0xD6, 0x87, 0xCA, 0x57, 0x46, 0x4D, 0x8B, 0x83, 0xD1, 0x3B, 0x4B, 0xC0, 0xDB, 0xA7, 0x70 };

int _tmain(int argc, _TCHAR* argv[])
{
	FILE *fpdb = fopen("MicroMsg.db", "rb+");
	if (!fpdb)
	{
		printf("Open file error!\n");
		return 0;
	}
	fseek(fpdb, 0, SEEK_END);
	long nFileSize = ftell(fpdb);
	fseek(fpdb, 0, SEEK_SET);
	unsigned char *pDbBuffer = new unsigned char[nFileSize];
	fread(pDbBuffer, 1, nFileSize, fpdb);
	fclose(fpdb);

	unsigned char salt[16] = { 0 };
	memcpy(salt, pDbBuffer, 16);

#ifndef NO_USE_HMAC_SHA1
	unsigned char mac_salt[16] = { 0 };
	memcpy(mac_salt, salt, 16);
	for (int i = 0; i < sizeof(salt); i++)
	{
		mac_salt[i] ^= 0x3a;
	}
#endif

	int reserve = IV_SIZE;
#ifndef NO_USE_HMAC_SHA1
	reserve += HMAC_SHA1_SIZE;
#endif
	reserve = ((reserve % AES_BLOCK_SIZE) == 0) ? reserve : ((reserve / AES_BLOCK_SIZE) + 1) * AES_BLOCK_SIZE;

	unsigned char key[KEY_SIZE] = { 0 };
	unsigned char mac_key[KEY_SIZE] = { 0 };

	OpenSSL_add_all_algorithms();
	PKCS5_PBKDF2_HMAC_SHA1((const char *)pass, sizeof(pass), salt, sizeof(salt), DEFAULT_ITER, sizeof(key), key);
#ifndef NO_USE_HMAC_SHA1
	PKCS5_PBKDF2_HMAC_SHA1((const char *)key, sizeof(key), mac_salt, sizeof(mac_salt), 2, sizeof(mac_key), mac_key);
#endif

	unsigned char *pTemp = pDbBuffer;
	unsigned char pDecryptPerPageBuffer[DEFAULT_PAGESIZE];
	int nPage = 1;
	int offset = 16;
	while (pTemp < pDbBuffer + nFileSize)
	{
		printf("decrypt page:%d/%d \n", nPage, nFileSize / DEFAULT_PAGESIZE);

#ifndef NO_USE_HMAC_SHA1
		//check hmac
		unsigned char hash_mac[HMAC_SHA1_SIZE] = { 0 };
		unsigned int hash_len = 0;
		HMAC_CTX hctx;
		HMAC_CTX_init(&hctx);
		HMAC_Init_ex(&hctx, mac_key, sizeof(mac_key), EVP_sha1(), NULL);
		HMAC_Update(&hctx, pTemp + offset, DEFAULT_PAGESIZE - reserve - offset + IV_SIZE);
		HMAC_Update(&hctx, (const unsigned char *)&nPage, sizeof(nPage));
		HMAC_Final(&hctx, hash_mac, &hash_len);
		HMAC_CTX_cleanup(&hctx);
		if (0 != memcmp(hash_mac, pTemp + DEFAULT_PAGESIZE - reserve + IV_SIZE, sizeof(hash_mac)))
		{
			//hash check err
			return 0;
		}
#endif
		//
		if (nPage == 1)
		{
			memcpy(pDecryptPerPageBuffer, SQLITE_FILE_HEADER, offset);
		}

		//aes decrypt
		EVP_CIPHER_CTX* ectx = EVP_CIPHER_CTX_new();
		EVP_CipherInit_ex(ectx, EVP_get_cipherbyname("aes-256-cbc"), NULL, NULL, NULL, 0);
		EVP_CIPHER_CTX_set_padding(ectx, 0);
		EVP_CipherInit_ex(ectx, NULL, NULL, key, pTemp + (DEFAULT_PAGESIZE - reserve), 0);

		int nDecryptLen = 0;
		int nTotal = 0;
		EVP_CipherUpdate(ectx, pDecryptPerPageBuffer + offset, &nDecryptLen, pTemp + offset, DEFAULT_PAGESIZE - reserve - offset);
		nTotal = nDecryptLen;
		EVP_CipherFinal_ex(ectx, pDecryptPerPageBuffer + offset + nDecryptLen, &nDecryptLen);
		nTotal += nDecryptLen;
		EVP_CIPHER_CTX_free(ectx);

		//assert(nTotal == DEFAULT_PAGESIZE - reserve - offset);

		//no necessary ,just like sqlcipher
		memcpy(pDecryptPerPageBuffer + DEFAULT_PAGESIZE - reserve, pTemp + DEFAULT_PAGESIZE - reserve, reserve);

		FILE *fp = fopen("MicroMsg_Decrypt.db", "ab+");
		{
			fwrite(pDecryptPerPageBuffer, 1, DEFAULT_PAGESIZE, fp);
			fclose(fp);
		}

		nPage++;
		offset = 0;
		pTemp += DEFAULT_PAGESIZE;
	}
		return 0;
}
```

在上文的评论中，有一个留言详细的介绍了openssl的编译，以及vs的搭建，可以参考

2. 参考文章 [解密微信聊天数据库](http://techotaku.me/2018/04/13/解密微信聊天数据库)

解密算法需要用到openssl库，win10请用1.0.2版本，为避免各种问题，可以直接使用 [slproweb.com](http://slproweb.com/products/Win32OpenSSL.html)提供的安装版，切勿使用名字含Light的轻量版，安装完毕后，项目属性配置如下，当然将包含的头文件和依赖的lib文件复制到项目目录更好，最后需要注意的是不要忘了`libeay32.dll`，安装openssl库之后默认路径`C:\Windows\SysWOW64\libeay32.dll`，编译成可执行文件发布请附带`libeay32.dll`。

3. 使用OD获取数据库密钥的详细过程

参考 [解密PC版微信数据库  使用OllyDbg获取解密密钥](https://luozikuan.github.io/computer/2018/10/07/get_wechat_decrypt_key)

需要指出的是最后获取的密钥是32字节

4. VS调试代码过程 

参考：[vs2013 error LNK2019 无法解析的外部符号](http://flamepeak.com/2019/04/09/visual-studio-error-lnk2019/)

---
