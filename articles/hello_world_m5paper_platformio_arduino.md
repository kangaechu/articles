---
title: "M5Paper + PlatformIO + Arduino ã§HelloWorld"
emoji: "ð"
type: "tech"
topics: ["M5Paper", "PlatformIO", "Arduino"]
published: true
---

åæ¥ã²ãããªãã¨ã§æã«å¥ãã[M5Paper](https://www.switch-science.com/catalog/6749/)ã
e-inkã§é»æ± ãããæã¡ãããªã®ã§å®¶ã®æ¸©åº¦ã¨ããè¡¨ç¤ºãããã
éçºç°å¢ãæ´ããã ãã§ãã®ãããå¤§å¤ã ã£ãã®ã§HelloWorldã§ããã¾ã§ãã¾ã¨ãã¾ããã

## ç°å¢

- Macbook Air (M1, 2020)
- macOS Big Sur 11.2.3
- Visual Studio Code 1.55.0 Darwin arm64
- M5Paper

## æé 

### ãã©ã¤ãã®ã¤ã³ã¹ãã¼ã«

ä½æãããã­ã°ã©ã ãM5Paperã«éãããããã©ã¤ããå¿è¦ã«ãªãã¾ãã

[M5Stack](https://m5stack-store.myshopify.com/pages/download) ã®ãã¼ã¸ã«ç§»åãã¾ãã
CP2104 Driverã®ãDownloadããã¯ãªãã¯ããã¨ãã©ãããã©ã¼ã ãé¸ã¹ãã®ã§ãåããã¦ããç°å¢ãé¸æãããã©ã¤ãããã¦ã³ã­ã¼ããã¾ãã

![](https://storage.googleapis.com/zenn-user-upload/nily67bcszp06up6ylvs4yslu27u)

ãã¦ã³ã­ã¼ããããã©ã¤ããå®è¡ããã¤ã³ã¹ãã¼ã«ãå®æ½ãã¾ãã

### PlatformIOã®ã¤ã³ã¹ãã¼ã«

Arduinoã®éçºç°å¢ã¨ãã¦ãè¿å¹´ããä½¿ããã[PlatformIO](https://platformio.org/)ãã¤ã³ã¹ãã¼ã«ãã¾ãã

å¬å¼ãµã¤ãã«ããã¨ãPlatformIOã¯ä»¥ä¸ã®ç¹å¾´ãããã¾ãã

- ã¯ã­ã¹ãã©ãããã©ã¼ã 
- å¤ãã®ç°ãªãã½ããã¦ã§ã¢éçºã­ããï¼SDKï¼ã¾ãã¯ãã¬ã¼ã ã¯ã¼ã¯ããµãã¼ã
- é«åº¦ãªãããã°ï¼Debuggingï¼
- åä½ãã¹ãï¼Debuggingï¼
- èªåã³ã¼ãåæï¼éçã³ã¼ãåæï¼
- ãªã¢ã¼ãç®¡çï¼ãªã¢ã¼ãéçºï¼

PlatformIOã¯Visual Studio Codeã®ãã©ã°ã¤ã³ã¨ãã¦æä¾ããã¦ãã¾ãã

æ©éã¤ã³ã¹ãã¼ã«ãã¾ããããã¾ãã¯Visual Codeãèµ·åãã¾ãã

![m5_01.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/36587/83d95a93-c9cc-6712-b9a1-f95e6e122377.png)


1. å·¦å´åè§ã®ã¢ã¤ã³ã³ãExtensionsããé¸æ
2. æ¤ç´¢ãã¼ãããplatformioãã§æ¤ç´¢
3. ãPlatformIO IDEããé¸æ
4. ãInstallããé¸æ

ããã§PlatformIO IDEã®ã¤ã³ã¹ãã¼ã«ã¯å®äºã§ãã

### PlatformIO ãã­ã¸ã§ã¯ãã®ä½æ

ã¾ãã¯PlatformIOã®ãã­ã¸ã§ã¯ããä½æãã¾ãã

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/36587/00d06c7c-3e10-23bd-689c-a605c641387a.png)

ä¸ã«ããå®¶ã®ã¢ã¤ã³ã³ãé¸æããã¨ãPIO Homeã®ç»é¢ãéãã¾ããQuick Accessã®ãNew Projectããé¸æããæ°ãããã­ã¸ã§ã¯ããä½æãã¾ãã

Project Wizard ãéãã®ã§ãä»¥ä¸ã®ããã«è¨­å®ãã¾ãã

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/36587/98a00a6e-9386-7522-07e9-7b3b97db659b.png)

- Name: **ï¼ä»»æã®ååï¼**
- Board: **M5Stack Fire**
- Framework: **Arduino**

åé¡ãªããã°ãFinishããæ¼ãã¾ãã

ãã­ã¸ã§ã¯ããä½æããã`platformio.ini` ã®ç·¨éç»é¢ãè¡¨ç¤ºããã¾ããã

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/36587/53424336-a596-7c93-22eb-cb8c91536fea.png)


### ã©ã¤ãã©ãªã®è¿½å 

M5Paperã®ç»é¢è¡¨ç¤ºã«å¿è¦ãªã©ã¤ãã©ãªãè¿½å ãã¾ãã

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/36587/730444f8-4796-bff5-4c19-e0709131e8d0.png)

1. ä¸ã«ããå®¶ã®ã¢ã¤ã³ã³ãé¸æããPIO Homeãéã
2. ãLibrariesããé¸æããã©ã¤ãã©ãªç»é¢ãéã
3. Registryã®æ¤ç´¢ãã¼ãããM5EPDãã§æ¤ç´¢
4. æ¤ç´¢çµæãããM5EPDããé¸æ

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/36587/71151f00-4a3d-9b3a-bc0d-742f89d4eae4.png)

M5EPDã®ã©ã¤ãã©ãªã®æå ±ãè¡¨ç¤ºãããã®ã§ããAdd to Projectãã§ãã­ã¸ã§ã¯ãã«è¿½å ãã¾ãã

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/36587/00be6372-f5fe-99f1-df84-b56ceb80fa11.png)

Add project dependencyç»é¢ãè¡¨ç¤ºãããã®ã§ãåã»ã©ä½æãããã­ã¸ã§ã¯ããé¸æãããAddããé¸æãã¾ãã

platform.ioã®lib_depsã« `m5stack/M5EPD` ãè¿½å ããããã¨ãç¢ºèªãã¾ãã

```ini:platformio.ini
; PlatformIO Project Configuration File
;
;   Build options: build flags, source filter
;   Upload options: custom upload port, speed and extra flags
;   Library options: dependencies, extra library storages
;   Advanced options: extra scripting
;
; Please visit documentation for the other options and examples
; https://docs.platformio.org/page/projectconf.html

[env:m5stack-fire]
platform = espressif32
board = m5stack-fire
framework = arduino
lib_deps = m5stack/M5EPD@^0.1.1
```

 `.pio/libdeps/m5stack-fire` éä¸ã« M5EPDã®ãã£ã¬ã¯ããªãè¿½å ããã¦ãã¾ããã

```shell-session
$ tree -d .pio -L 4
.pio
âââ build
âÂ Â  âââ m5stack-fire
âââ libdeps
    âââ m5stack-fire
        âââ M5EPD
            âââ examples
            âââ src
            âââ tools
```

### ã½ã¼ã¹ã³ã¼ãã®ä¿®æ­£

ã²ã¨ã¾ããHelloWorldãè¡¨ç¤ºã§ããã³ã¼ããexampleããã³ãããã¾ãã
ã¾ãã¯ãã³ããåã®ã½ã¼ã¹ãéãã¾ãã

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/36587/a12a8461-c24f-a89c-63f8-683a022f7c80.png)

1. ä¸ã«ããå®¶ã®ã¢ã¤ã³ã³ãé¸æããPIO Homeãéã
2. ãLibrariesããé¸æããã©ã¤ãã©ãªç»é¢ãéã
3. ãInstalledããé¸æè¢ãã¤ã³ã¹ãã¼ã«ããã©ã¤ãã©ãªä¸è­ãè¡¨ç¤º
4. ä¸è¦§ãããM5EPDããé¸æ

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/36587/a58e49be-bc31-45c5-6c6b-c35aada0155e.png)

Examplesã¿ãã«ãµã³ãã«ã³ã¼ããè¡¨ç¤ºãããã®ã§ãæ¤ç´¢ãã¼ãããHelloWorldãã§æ¤ç´¢ããã½ã¼ã¹ãã³ãã¼ãã¾ãã
ã³ãã¼ããã³ã¼ãã¯src/main.cppã«è²¼ãä»ãã¾ãã

GUIã®ç»é¢é·ç§»ãé¢åã ã£ãã[GitHub](https://github.com/m5stack/M5EPD/blob/main/examples/Basics/HelloWorld/HelloWorld.ino)ããã³ããããã°è¯ãã§ãããã

```cpp:src/main.cpp
#include <M5EPD.h>

M5EPD_Canvas canvas(&M5.EPD);

void setup()
{
    M5.begin();
    M5.EPD.SetRotation(90);
    M5.EPD.Clear(true);
    M5.RTC.begin();
    canvas.createCanvas(540, 960);
    canvas.setTextSize(3);
    canvas.drawString("Hello World", 45, 350);
    canvas.pushCanvas(0,0,UPDATE_MODE_DU4);
}

void loop()
{

}
```

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/36587/eb7f04a5-b65d-751c-ac57-ecbbfe31ac87.png)

ã©ã¤ãã©ãªãèª­ã¿è¾¼ã¾ããªããã¨ã©ã¼ã¨ãªã£ã¦ããå ´åã¯Visual Studio Codeãåèµ·åãã¾ããï¼å¤åãã£ã¨ããæ¹æ³ãããã¨æãã®ã§ãèª°ãæãã¦ãã ããï¼


### ãã«ã

ã³ã¼ããæ¸ããã®ã§ãããããã«ãã§ãã

ç»é¢ä¸ã®ãã§ãã¯ãã¿ã³ãã¯ãªãã¯ããã¨ãã«ããéå§ãã¾ãã

![](https://storage.googleapis.com/zenn-user-upload/2tr0crnnfrh2196ownscgnqfmnn2)

`SUCCESS` ã¨è¡¨ç¤ºããããã¨ãç¢ºèªãã¾ãã

### ããã­ã¤

ããããããã­ã¤ã§ãã­ã

M5 PaperãUSBã§æ¥ç¶ããç»é¢ä¸ã®âãã¿ã³ãã¯ãªãã¯ããã¨ãã«ããéå§ãã¾ãã

![](https://storage.googleapis.com/zenn-user-upload/ddqs0z0c0tj5m1tde6ppg7e5gjmk)

`SUCCESS` ã¨è¡¨ç¤ºããããã¨ãç¢ºèªãã¾ãã

M5 Paperã«ç»åãè¡¨ç¤ºããã¾ããï¼ãã£ãã­ï¼
![](https://storage.googleapis.com/zenn-user-upload/x2lpp01mulg9jsv23covba9ik76i)