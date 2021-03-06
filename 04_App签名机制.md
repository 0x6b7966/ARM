#App的签名机制 【Xcode是如何将App安装到手机的】
#####[App的签名机制](https://juejin.im/post/5bd26fe2e51d456d54258088)

#首先先科普一下XCode签名需要用到的理论知识
- RSA加密算法,一种非对称的加密算法，用于通信。这种算法通常是客户端持有公钥，服务端持有私钥。客户端利用公钥加密。服务端可以用私钥解密。服务利用私钥加密数据发给客户端，客户端可以利用公钥解密出来。【简单来说就是：公钥加密的数据，利用私钥可以解密；私钥加密的数据，公钥同样能解密】
- 数字签名 客户端把【数据】，并利用公钥加密【数据的MD5】，然后把这【数据】与【机密后的MD5】发给服务器 服务器获取到数据包后，先求出【数据】的MD5，并解密【数据的MD5】，然后两者进行对比，如果不一样就代表数据被截获串改了

#XCode如何将App安装到手机的【首先这个流程会涉及到2次数字签名】
1. Mac电脑本地生成公钥和私钥，通过CSR(cerfification signing request) 把自己的公钥打包成CSR文件发给服务器。
2. 苹果服务器利用自己的私钥，对 Mac电脑的公钥进行数字签名，生成证书与描述文件，将证书与描述文件返回给Mac电脑。 一次签名
3. Mac电脑利用私钥对App的可执行文件的Hash值进行加密，生成App的签名  二次签名
4. Mac 将 App的可执行文件、App的签名、证书【关联Mac的私钥】、描述文件 打包成一个App传输给手机 
5. 手机将会使用苹果的公钥，对证书解析，获得Mac的公钥。利用Mac的公钥，解析App签名，获取Hash值进行认证，认证成功则App成功安装上，认证失败就无法安装。

#可以通过 openssl asnlparse -i -in xxx.certSigningRequest 查看csr文件信息
[youtube视频](https://www.youtube.com/watch?v=j76PvU9P59I&list=PL4XMD13FgeTTa4B1MKNRI7lrPlr4izRBg&index=16)

#授权机制(Entitlements)
- 简单说它就是一个沙盒的配置列表，上面列出了哪些行为被允许，哪些会被拒绝
  - codesign -d -entitlements -xx.app 查看授权列表 
    - 常见的 get-task-allow true

#配置文件(Provisioning)
- appid
- 使用的证书
- 功能授权列表
- 可安装的设备列表
- 苹果签名