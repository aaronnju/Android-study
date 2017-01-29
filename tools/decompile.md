
## Android

**阅读代码**  
直接使用jadx看apk，或者反编译成jar包使用jd-gui看，或者解压到java文件用IDE看  
1. java -jar ./t/apktool_2.0.3.jar d -f MyApp.apk  
2. 解压apk： unzip MyApp.apk -d MyApp Apk  
3. dex2Jar： ./t/dex2jar-2.0/d2j-dex2jar.sh classes.dex  
4. 可以直接使用JD-GUI查看jar包，或者解压之后反编译成jara文件使用IDE查看
	1. unzip classes-dex2jar.jar -d MyAppJar
	2. find MyAppJar -name *.class | xargs ./t/jad -s .java -r -ff -clear -dead -debug -space -b -d MyAppJava  



**修改APK**  
1. 反编译dex: java -jar ./t/baksmali-2.1.1.jar -o ./MyApp/classout/ ./MyApp/classes.dex  
2. 修改.smali代码: vi *.smali  
3. 编译dex: java -jar ./t/smali-2.1.1.jar ./MyApp/classout/ -o ./MyApp/classes.dex  
4. 用压缩工具替换apk包里面的dex文件  
5. 签名 java -jar ./t/signapk.jar ./key/certificate.pem ./key/key.pk8 MyAppCrack.apk  MyAppCrack_signed.apk  
6. 安装： adb install -r MyAppCrack_signed.apk


批处理：编译->安装->打开

	java -jar ../t/smali-2.1.1.jar classout_cracked -o classes.dex
	zip -u cracked_logged.apk classes.dex
	rm classes.dex
	java -jar ../t/signapk.jar ../key/certificate.pem ../key/key.pk8 cracked_logged.apk cracked_logged_signed.apk
	adb install -r cracked_logged_signed.apk
	adb shell am start -n com.xxx.xxx/com.xxx.xxx.WelcomeScreen

GenKEY:
- openssl genrsa -out key.pem 1024
- openssl req -new -key key.pem -out request.pem
- openssl x509 -req -days 9999 -in request.pem -signkey key.pem -out certificate.pem
- openssl pkcs8 -topk8 -outform DER -in key.pem -inform PEM -out key.pk8 -nocrypt



**查看.so 文件**  

	objdump -T libTest.so > x
	nm -D libTest.so 