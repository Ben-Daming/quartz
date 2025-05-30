[[Missing semeter]]
## 熵
单位:bit
- 在线遍历上限(限制:网速):熵是40bit,表示可能个数为2^40
- 离线遍历上限(限制:算力):熵是80bit,即有可能个数2^80
## 密码散列函数
将任意大小data映射为同一长度的密码
	hash(array\<byte\>)-->vector(n)
其中n相对是固定的
- Git使用SHA-1,n=160(bit),即40位十六进制数
散列函数性质:
1. 确定性:对任意相同输入,输出相同(映射性质)
2. 强无碰撞(接近单射):输出相同,难以找到两个相同输入
3. 不可逆:难以从hash(object)推出object本身全貌
SHA-1的功能:
{base on 确定性+强无碰撞} 
hash(object)对于object是忠实的.故可作为加密对象的id,以:
	1. 验证镜像站/远程仓库文件是否被篡改
	2. 用作git中的指针
## 密钥(key)生成函数
> harsh(object)生成密码的算法肯定是越快越好.那么对于密钥加密/解密算法是否也一样?

密钥加密/解密算法通常要很慢,原因有二:
1. 用户只需要运行一次密钥匹配,所以无伤大雅;而对于枚举破解者,慢算法可以拖慢枚举效率
2. 慢算法本身时间开销远大于密钥匹配程度带来的开销,因此可以防止破解者获取密钥匹配程度
### 对称加密系统
该系统由几个函数共同组成:
1. key generation 随机生成一个key
2. 加密(encrypt)函数 encrypt(明文plaintext,key)--->密文ciphertext
3. 解密(decrypt)函数 decrypt(ciphertext,key)--->plaintext
有了密钥,既可以encrypt,也可以decrypt.
decrypt(encrypt(plaintext,key),key)=plaintext

- 平台存储用户登陆数据时,不能直接存储password.一旦平台被攻击,用户的password将全部泄露.
- 优化方案:针对每个password生成一个哈希函数,存储每个哈希函数
	- 缺陷:两个用户使用相同password时,他们的harsh函数一定相同!这意味平台无法识别这两个用户谁是谁
- 正规方案:针对每个用户额外生成一组salt.salt同时具有random和unique性质.把salt+password一起做成哈希函数.平台存储salt和harsh(salt+password),本地存储salt
	- 用户验证登陆时,平台将password连上用户对应的salt,重新计算哈希值,并与存储的哈希值比对
	- 可以看作此时用户有了两个id,一个是harsh(salt),一个是harsh(password).
若用户同时在多个平台使用同一个password,针对各个平台各自生成一个salt也能防止用户所有密码被一次性破解
目前主要应用:用户上传文件到不信任的云平台.
### 非对称加密系统
encrypt(plaintext,publickey) --->ciphertext
decrypt(ciphertext,privatekey)--->plaintext
非对称加密是公钥加密的信息,只能由私钥来解;私钥加密的信息,只能由公钥来解.
概念:签名和验证 签名:sign(message,privatekey)--->sign 
验证:verify(message,sign,publickey1)--->true/false
应用广泛:
- 加密电子邮件/私密聊天:我向全网/给特定用户公布公钥,授权用户向我发送消息,只有我有查看权限.没权限的人只能看到乱码.我发送的信息也对持有公钥者加密.
- 软件签名:开发者会在软件上签名,确保你收到的软件来自正确的人.通过分发公钥来规定能下载文件的人(网盘?)
以上这些应用都涉及一个关键命题:公钥如何分发? 每个平台有自己的信任规则
## SSH
	ssh-keygen
生成一对非对称密钥.公钥会被分发,可以明文存储.但私钥必须加密存储.ssh-keygen提示用户输入密码,根据密码生成一个密钥.ssh-keygen使用对称加密算法由这个密钥加密私钥
用户公钥一般存储在HOME目录下.ssh/authorized_keys文件中.当服务器已知用户所持公钥,会选择一个随机数字发送给客户端,客户端使用私钥对这个数字签名后发给服务器,服务器再使用用户公钥来verify.