---
layout: post
title: HTTPS 简明教程
category : https
tagline: "Supporting tagline"
tags : [https]
---

# 前言

越来越多的网站使用 HTTPS 来确保安全。HTTPS 使用 SSL/TSL 技术来加密两个系统之间的通信。
这个教程将帮助读者逐步深入地理解 HTTPS 。本教材会分为几个章节，每个章节包含若干相关的主题。每个主题包含浅显易懂的解释和来自现实生活的例子。

本教材可供想了解  HTTPS 的原理、了解如何在网站使用  HTTPS 的初学者和专业人士参考。

# 第 1 节 什么是HTTPS

HTTPS 代表 Hyper Text Transfer Protocol Secure（超文本传输安全协议），它是一种为两个系统（例如浏览器和 web 服务器）之间的通信提供保护的协议。

下面的图片表明了 HTTP  和 HTTPS 的通信过程的差别：

![https communication vs http communication](https://blog.text.wiki/images/2019/https-communication.png)
<p style="text-align: center;">HTTP 和 HTTPS 的通信过程</p>

如上图所示，[HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol) 在浏览器和 web 服务器之间传输数据时，使用了明文的超文本格式；而 HTTPS 采用了加密的格式。HTTPS 阻止了攻击者在传输过程中来查看、修改数据的攻击行为。即使攻击者截获了通信消息，他们也无法使用，因为报文被加密了。

HTTPS 在浏览器和 web 服务器之间建立了加密连接，使用的技术是 Secure Socket Layer (SSL) 或 Transport Layer Security (TLS) 协议。TLS 是 SSL 的新版本。

## SSL 介绍

SSL 是两个系统之间建立加密连接的标准安全技术。适用范围包括浏览器到服务器、服务器到服务器、客户端到服务器等。SSL 保证了两个系统传输的数据保持加密和私密。

HTTPS 实质上是 HTTP over SSL。SSL 建立加密连接的方式，是使用一种电子证书 --- SSL 证书。

![ssl-link](https://blog.text.wiki/images/2019/ssl-link.png)
<p style="text-align: center;">SSL</p>

## HTTP 和 HTTPS 的比较

| http                 | https              |
| -------------------- | ------------------ |
| 数据以超文本格式传输 | 数据以加密格式传输 |
| 默认端口 80          | 默认端口 443       |
| 不安全               | 使用 SSL 安全技术  |
| 以 `http://` 开头    | 以 `https://` 开头 |

##  HTTPS 的优势

- **安全通信:** HTTPS 通过在两个系统之间建立加密连接来保证安全连接。
- **数据完整性:** HTTPS 通过加密数据来保证数据完整性。即使攻击者捕获了数据，他们也无法查看或修改它。
- **隐私和安全:** HTTPS 通过组织攻击者监听浏览器和服务器之间的通信过程，来保护网站用户的隐私和安全。
- **更快的性能:** HTTPS 比 HTTP 具有更快的数据传输速率，因为数据进行了加密从而使体积减少。
- **搜索引擎优化:** 使用 HTTPS 可以增加搜索引擎优化（SEO）的排名。在 Google Chrome浏览器，如果使用 HTTP，网页会被标记为“**不安全**”。
- **未来:** HTTPS 代表了web 的未来，因为它使互联网更安全。

# 第 2 节 SSL 的工作原理

HTTPS 使用 SSL 协议来传输加密数据。我们来看下 SSL 的工作原理。SSL 使用以下概念：

1. 非对称密码学
2. 对称密码学

## 非对称密码学

非对称密码学（又称为非对称加密或公钥加密）使用数学相关的密钥对来加密和解密数据。在密钥对中，其中一个密钥可以分享给任何人，这个密钥称为公钥（**Public Key**）；另外一个需要保持私密，称为私钥（**Private Key**）。

在非对称加密中，可以使用私钥对数据进行签名，该私钥只能使用成对的相关公钥解密。

![asymmetric-cryptography](https://spetacular.github.com/images/2019/asymmetric-cryptography.png)
<p style="text-align: center;">非对称加密原理图</p>
SSL 使用非对称加密来发起通信，这被称为 SSL 握手。 最常用的非对称密钥加密算法包括EIGamal，RSA，DSA，椭圆曲线技术和PKCS。

## 对称密码学

在对称密码学里，只有一个密钥来加密和解密数据。发送者和接收者都拥有这个密钥，并且应该只限于他们拥有，不能泄漏给第三方。

发起握手之后，SSL 利用会话密钥（session key）来使用对称加密。使用最广泛的对称加密算法是 AES-128，AES-192 和 AES-256。

![symmetric-cryptography](https://spetacular.github.com/images/2019/symmetric-cryptography.png)
<p style="text-align: center;">对称加密原理图</p>
## SSL 上的数据传输

SSL 使用非对称和对称加密来安全地传输数据。下图表明了SSL 通信的步骤:

![ssl-communication](https://spetacular.github.com/images/2019/ssl-communication.png)
<p style="text-align: center;">SSL 通信</p>
如上图，SSL 通信被分为两个步骤：SSL 握手和真正的数据传输。

### SSL 握手

SSL 上的通信通常由 SSL 握手开始。SSL 握手是一种非对称加密，允许浏览器在开始实际数据传输之前验证 web 服务器，获得公钥，建立安全连接。

下图表明了 SSL 握手的步骤：

![ssl-handshack](https://spetacular.github.com/images/2019/ssl-handshack.png)
<p style="text-align: center;">SSL 握手</p>
我们说明一下上图的步骤：

1. 客户端发送 “client hello” 消息。这包括客户端的 SSL 版本号，密码设置，会话专用数据以及服务器使用 SSL 与客户端通信需要的其它信息。
2. 服务器以 “server hello” 消息响应。这包括服务器的SSL版本号，密码设置，会话专用数据，带有公共密钥的SSL证书以及客户端使用SSL与服务器通信需要的其它信息。
3. 客户端从CA（证书颁发机构）验证服务器的SSL证书并鉴定服务器。如果身份鉴定失败，则客户端将拒绝SSL连接并抛出异常。如果认证成功，将继续执行步骤 4。
4. 客户端创建会话密钥，并使用服务器的公共密钥对其进行加密，然后将其发送到服务器。如果服务器已请求客户端身份鉴定（主要是在服务器到服务器的通信中），则客户端会将自己的证书发送到服务器。
5. 服务器使用其私钥解密会话密钥，并将确认消息发送给使用该会话密钥的客户端。

这样在SSL握手结束时，客户端和服务器都具有有效的会话密钥，它们将用于加密或解密实际数据。此后将不再需要公钥和私钥。

## 数据传输

现在客户端和服务器使用一个共享的会话密钥来加密和解密实际的数据，并传输之。由于两端使用同样的会话密钥，所以这是一个对称加密。实际数据传输采用对称加密，是因为对称加密的 CPU 消耗比非对称加密更少。

![ssl-data-transfer](https://spetacular.github.com/images/2019/ssl-data-transfer.png)
<p style="text-align: center;">SSL 数据传输</p>
这样，SSL 使用了非对称加密和对称加密。在现实生活中，实现 SSL 通信的一些基础架构，称为“公共密钥基础架构”（Public Key Infrastructure）。

## 公共密钥基础架构

公共密钥基础架构（[public key infrastructure (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure)）是创建、管理、分发、使用、储存、废弃电子证书和管理公钥加密的一系列角色、策略、程序的集合。

PKI 包括以下元素：

- 证书颁发机构: Certificate Authority，简称 CA。这个机构负责鉴定个人、计算机和其它实体的身份。
- 注册机构: Registration Authority。CA 的一个子机构，代表根证书颁发机构，颁发证书用于特殊用途。
- SSL 证书: SSL Certificate。SSL 证书是一个包含公钥和其它信息的数据文件。
- 证书管理系统: Certificate Management System。这个系统负责存储、校验、废弃证书。

# 第 3 节 什么是 SSL 证书?

SSL 证书，又叫作电子证书，在两系统的安全通信中扮演着重要的角色。

SSL 证书是由 CA 颁发的数据文件。如上所述，SSL 使用非对称加密，利用一个密钥对（公钥和私钥）来建立两个系统之间的加密连接。SSL 证书包含所有者的公钥和其它细节信息。web 服务器通过 SSL 证书将公钥发送给浏览器，而浏览器则使用 SSL 证书来验证和鉴定web 服务器。

读者可以打开任何 HTTPS 网站的证书。例如，在 Google Chrome 浏览器里打开  [https://www.google.com](https://www.google.com) 来检查 google.com 的SSL 证书。如下图所示，任何 HTTPS 网站在地址栏都有一个锁的图样。

![https-in-browser](https://spetacular.github.com/images/2019/https-in-browser.png)
<p style="text-align: center;">浏览器中的 HTTPS</p>

点击锁图样，然后在弹层点击证书，如下图所示：

![open-ssl-certificate](https://spetacular.github.com/images/2019/open-ssl-certificate.png)

<p style="text-align: center;">打开 SSL 证书</p>

打开的证书窗口如下图所示。

![ssl-certificate](https://spetacular.github.com/images/2019/ssl-certificate.png)

<p style="text-align: center;">SSL 证书</p>

在“通用（General）” Tab 下，显示了证书的签发对象、签发者和有效起止时间。“细节（Details）” Tab 下包括其它细节信息。“证书路径（Certificate Path）” 包含了所有中间证书和根 CA 证书的信息。

## X.509

[X.509](https://en.wikipedia.org/wiki/X.509) 是定义电子证书格式的标准。 SSL 使用 X.509 格式。换句话说，SSL 证书实际上是 X.509 证书。

X.509 使用 [Abstract Syntax Notation One (ASN.1)](https://en.wikipedia.org/wiki/Abstract_Syntax_Notation_One) 的格式化语言来表示证书的数据结构。

X.509 格式的 SSL 证书包括以下信息：

* **版本**：Version。证书使用的数据格式采用 X.509 的哪个版本。 

- **序列号**：Serial number。CA 为每个证书指定的唯一标识符。
- **公钥**：Public Key。所有者的公钥。
- **主题**：Subject。所有者的姓名、地址、国家和域名。
- **签发者**：Issuer。签发该证书的 CA 的名字。
- **生效时间**：Valid-From。证书开始生效的日期，在此之前无效。
- **过期时间**：Valid-To。证书过期的日期，在此之后无效。
- **签名算法**：Signature Algorithm。创建签名所使用的算法。
- **指纹**：Thumbprint。证书的哈希值。
- **指纹算法**：Thumbprint Algorithm。创建证书哈希值所使用的算法。

## SSL 证书的类型

根据验证级别和域名数目，SSL 证书有多种类型可以选用。所有类型的证书，其加密级别都相同，但验证级别却不同。

## 基于验证级别的 SSL 证书类型

网站使用 SSL 证书来与访客建立起信用等级（Trust Level）。不同的企业需要不同的信用等级。例如，收集用户重要信息的网站需要安全的传输。金融机构需要验证域真实性以及数据安全性。因此，CA 需要根据信用等级来验证网站所有者的信息。以下三种证书是基于验证级别的：

### 域验证证书（Domain Validated Certificates）

域验证（Domain Validated ，简写DV ）证书要求最低的验证级别，因为 DV 证书的主要用途是保护域名对应的 web 服务器和浏览器之间的通信安全。CA 只验证所有者是否拥有域名的控制权。

### 组织验证证书（Organization Validated Certificates）

组织验证（Organization Validated ，简写OV ）证书要求中等级别的验证等级。在此等级下，CA 不但检查使用该域名的组织的权限，而且会检查组织的信息。OV 证书增强了组织和域名的信用等级。

### 扩展验证证书（Extended Validated Certificates）

扩展验证（Extended Validated ，简写EF ）证书要求高级别的验证等级。在此级别下，CA 根据准则对组织进行严格的背景检查。这包括对实体的法律、物理、经验状况的验证。

## 基于域名数目的 SSL 证书类型

根据受保护的域名的数目，证书可以分为以下几类。

### 单域名证书（Single Domain Certificate）

单域名证书只保护一个完整域名。例如针对 [www.example.com](www.example.com) 的单域名证书，不能用于 [mail.example.com](mail.example.com)。

### 通配符证书（Wildcard SSL Certificate）

通配符证书可以保护一个域名的无限数量的子域名。例如一个针对 example.com 的通配符证书，也可用于 mail.example.com、blog.example.com 等。

### 统一SSL证书/多域SSL证书/ SAN证书（Unified SSL Certificate /Multi-Domain SSL Certificate/SAN Certificate）

统一 SSL 证书使用 SAN 扩展之后，可以至多保护 100 个使用相同证书的域名。它专门用于保护 Microsoft Exchange 和 Office Communication 的通信环境。

选择了合适的 SSL 证书类型之后，需要从知名 CA 购买证书。此操作与技术关系不大，不再赘述。如果读者想安装免费 SSL 证书，请参考笔者以前的博文 [《教程：配置 Let's Encrypt 免费 HTTPS 证书》](https://blog.text.wiki/2017/03/29/lets-encrypt-deploy-step-by-step.html)。



# 第 4 节 SSL 证书格式

前文讲过，SSL 证书使用 X.509 格式。X.509 证书有不同的格式，例如PEM, DER, PKCS#7 和 PKCS#12。PEM 和 PKCS#7 格式 use Base64 ASCII 编码，DER 和 PKCS#12 使用二进制编码。根据不同的格式和编码方式，证书文件有不同的扩展名。

下图说明了 X.509 证书的编码格式和文件扩展名。

![ssl-certificate-format](https://spetacular.github.com/images/2019/ssl-certificate-format.png)

<p style="text-align: center;">SSL 证书格式</p>

## PEM 格式

大多数 CA 提供格式为 PEM 、文件为 Base64 ASCII 编码的证书。证书文件类型有 .pem, .crt, .cer, 和 .key。

* .pem 文件将服务器证书、中间证书和私钥包含在单个文件里。
* .crt 和 .cer 文件分别保存服务器证书和中间证书。
* .key 文件 保存私钥。

PEM 文件使用 ASCII 编码，因为读者可以使用任意文本编辑器打开它们。

PEM 文件中的每个证书包含在 `---- BEGIN CERTIFICATE----` 和 ` ----END CERTIFICATE---- ` 之间。

私钥文件包含在 `---- BEGIN RSA PRIVATE KEY-----` 和 `-----END RSA PRIVATE KEY----- `之间。

CSR 包含在 `-----BEGIN CERTIFICATE REQUEST-----` 和 `-----END CERTIFICATE REQUEST-----` 之间。

## PKCS#7 格式

PKCS#7 格式是加密消息语法标准（Cryptographic Message Syntax Standard）。PKCS#7 证书使用 Base64 ASCII 编码，文件扩展名为 .p7b 或 .p7c。只有证书以此格式保存，不包括私钥。P7B 证书包含在` -----BEGIN PKCS7-----` 和 `-----END PKCS7-----` 之间。

## DER 格式

DER 证书是二进制格式，存储在 .der 或 .cer 文件里。这些证书主要用于 Java 开发的 web 服务器中。

## PKCS#12 格式

PKCS#12 证书是二进制格式，存储在 .pfx 或 .p12 文件里。

PKCS#12 将服务器证书、中间证书和私钥包含在单个 .pfx 后缀、用密码加密保护的文件里。这些证书主要用于 Windows 平台。

CA 提供以上任何格式的证书。

# 结语

本文主要讲解了 HTTPS 的原理、SSL 证书的工作原理、SSL 证书的分类和格式，可以作为入门教程。读完本文，相信读者会对 HTTPS 有一个概览式的了解。读者有兴趣的读者可以研究密码学，有需要安装证书的读者可以自行搜索安装方法。

# 参考文献

本文为笔者处于学习目的而翻译，原文在 [HTTPS Tutorials](https://www.tutorialsteacher.com/https) 。

