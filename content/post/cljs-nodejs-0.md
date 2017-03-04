+++
title = "NodeJs Hello World ด้วย ClojureScript"
tags = ["nodejs","cljs"]
date = "2017-03-04T11:01:32+07:00"
author = ""
description = ""

+++

สิ่งที่จะทำวันนี้ก็คือเขียนเริ่มต้น Nodejs application ด้วย ClojureScript, แล้วทำไมต้องเป็น ClojureScript? งั้นไปรู้จักกับ ClojureScript แบบคร่าวๆ กันก่อน  

## [ClojureScript](https://clojurescript.org/) คือ ?
คอมไพเลอร์สำหรับภาษา Clojure ที่ คอมไพล์โค้ดจาก Clojure ไปเป็นโค้ด JavaScript  

## [Clojure](https://en.wikipedia.org/wiki/Clojure) คือ ?
Functional programming ภาษาหนึ่ง โดยที่มี Syntax เหมือนกับภาษา [Lisp](https://en.wikipedia.org/wiki/Lisp_(programming_language)) ซึ่งเจ้า Clojure นี่แหล่ะมีสิ่งที่น่าสนใจอยู่หลายอย่างเลยทีเดียว แต่ในที่นี้จะขอข้ามไปก่อน 🤣  


# สิ่งที่ต้องมี
 - NodeJs runtime [ดาวน์โหลดได้จากที่นี่](https://nodejs.org/en/download/)
 - JDK สำหรับรัน Clojure [ดาวน์โหลดได้จากที่นี่](http://www.oracle.com/technetwork/java/javase/downloads/index.html)  
 - Leiningen เป็น Build tools สำหรับ Clojure [ดาวน์โหลดได้จากที่นี่](https://leiningen.org/)  

```
    # สำหรับ Mac สามารถลงได้โดยใช้คำสั่งนี้
    brew install leiningen
```

# เริ่มกันเลย
1.สร้างโปรเจ็คต์ ในที่นี่จะใช้ชื่อว่า demo, ด้วยคำสั่ง `lein new app demo`  จะได้โฟลเดอร์แบบนี้  

```
./
└── demo
    ├── CHANGELOG.md
    ├── LICENSE
    ├── README.md
    ├── doc
    │   └── intro.md
    ├── project.clj
    ├── resources
    ├── src
    │   └── demo
    │       └── core.clj
    └── test
        └── demo
            └── core_test.clj

7 directories, 7 files
```

2.แก้ชื่อไฟล์ `src/demo/core.clj` เป็น `core.cljs` แล้วใส้โค้ด Hello World ของเราลงไป
{{< gist jigkoxsee 2a114cf4641d689fdfe377ec6bb2eab6>}}

3.ต่อไปคือการ Build โปรเจ็กต์ ซึ่งต้องมีการตั้งค่ากันสักเล็กน้อย เริ่มต้นด้วยการใส่ `[org.clojure/clojurescript "1.9.473"]` ลงไปใน Dependency lists ของโปรเจ็กต์ที่ไฟล์ `project.clj`  

4.ลงปลั้กอิน `lein-cljsbuild` โดยใส่ `:plugins [[lein-cljsbuild "1.1.5"]]` ลงไปในไฟล์ `project.clj`  

5.ตั้งค่าการ Build ClojureScript โดยการใส่เซ็กชัน `:cljsbuild` ลงไปในไฟล์ `project.clj`  
สุดท้ายแล้วจะได้ไฟล์หน้าตาประมาณนี้  
{{< gist jigkoxsee 02220b056cb2e266e2722a49349986e4 >}}

6.Build โดยใช้คำสั่ง `lein cljsbuild once`
```
$ lein cljsbuild once
Compiling ClojureScript...
Compiling "out/main.js" from ["src"]...
Successfully compiled "out/main.js" in 14.002 seconds.
```

7.ทดลองรันโค้ดที่ได้ด้วย node ด้วยคำสั่งนี้ `node out/main.js`
```
$ node out/main.js
Hello world! from ClojureScript
```

# ปิดท้าย
หลายๆคนอาจจะตกใจหนีหายไปก่อนจะอ่านถึงตรงนี้ สำหรับคนที่อ่านถึงอาจจะสงสัยว่าทำไมหลายขั้นตอนจัง ClojureScript มันยุ่งยากขนึ้นี้เลยหรอ จริงๆแล้วขึ้นอยู๋กับว่าเราจะทำอะไร ซึ่งถ้าต้องการเขียน ClojureScript เพื่อทำ Frontend dev เนี่ย ง่ายมากๆ แค่ `lein new figwheel demo` ก็ได้โปรเจ็กต์ที่พร้อมสำหรับการเขียนแล้วการ build แล้ว  
แต่ถ้าเพื่อไปรันบน nodejs เนี่ย ก็ได้มีคนทำ [lein template](https://github.com/honza/cljs-node) ไว้แล้ว จริงๆแค่ `lein new cljs-node demo` ก็ได้โปรเจ็กต์พร้อมใช้แล้วแล่ะ แต่ทว่า lein template ตัวนี้มันไม่ update มาหลายปีแล้ว 😅  
ในที่นี้ผมเลยใช้วิธีการสร้างโปรเจ็กต์เองตั้งแต่ต้น ซึ่งวิธีการที่ผมใช้นี้ถ้าดัดแปลงเพียงเล็กน้อยก็จะสามารถทำให้เราเขียน Node module แล้วเอาไปใช้ร่วมกับโค้ด Nodejs เดิมได้ไม่ยาก 😎