---
title: A certificate chain could not be built to a trusted root authority
date: 2021-01-09 10:27:55
tags: [windows]
categories: [Tools]
---

---




windows 7 出现这个情况，一般很多软件安装不上，中间异常退出。例如：net framework， vmware等等



解决方法如下：

1. 从微软官方下载证书: [MicrosoftRootCertificateAuthority2011.cer](http://go.microsoft.com/fwlink/?linkid=747875&clcid=0x409)

或者从下面地址下载

[MicrosoftRootCertificateAuthority2011.cer - github](https://github.com/yanglr/WindowsDevTools/raw/master/dotNET/MicrosoftRootCertificateAuthority2011.cer).

2. 双击证书，开始安装
3. 点击下方**安装证书**按钮
4. 下一步
5. 选择证书存放位置，手动选择第二项，点击**浏览**或Browse，选中**可信任的根证书机构**或**Trusted Root Certification Authorities**
6. 继续下一步，直至完成
7. 如果后面弹框的话，就接受。



参考：https://stackoverflow.com/questions/47176239/a-certificate-chain-could-not-be-built-to-a-trusted-root-authority/60812129#60812129






