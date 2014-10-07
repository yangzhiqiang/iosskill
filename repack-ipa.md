###重新打包ipa文件

作为一个app应用程序开发者，在app应用程序在苹果商店上架前总需要将安装包安装到ios机器上进行测试，这个时候我们就需要打包in house版本的ipa了，打包in house实际上是一个将ipa应用程序重新签名的一个过程。一般来说打包in house需要以下东西:MAC机器，一般打包ipa都是在MAC机上打包的，一个后缀名为.mobileprovision概要配置文件,一个后缀名为P12的证书，还有一个后缀名为.cer的证书，还有就是你想重新签名的ipa.

####如何给ipa重新签名

* 步骤1 : 
unzip YourApp.ipa
* 步骤2:  
rm -rf Payload/YourApp.app/_CodeSignature
* 步骤3:  
cp InHouse.mobileprovision Payload/YourApp.app/embedded.mobileprovision
* 步骤4:  
/usr/bin/codesign -f -s "iPhone Distribution: YourCompanyName" --resource-rules Payload/YourApp.app/ResourceRules.plist Payload/YourApp.app
_在10.10中，会提示--resource-rules废弃了，删掉这个选项即可_
* 步骤5: 
zip -r YourApp_inhouse.ipa Payload

*InHouse.mobileprovision 是你要用来签名的provision文件*
*iPhone Distribution: YourCompanyName 是指该签名对应的证书的名字。这个可以在keychain中找到*