
+++
title = "เขียน React Native ด้วย ClojureScript และ re-frame"
tags = [
    "ClojureScript",
    "React Native",
    "re-frame",
    "reagent",
    "expo"
]
date = "2017-03-18T20:30:00+07:00"
author = "ziko"
description = ""
+++

ClojureScript นั้นเป็นถาษาตระกูล Lisp ภาษานึงซึ่งจะคอมไพล์โค้ดไปเป็น Javascript เราจึงสามารถเขียนโปรแกรม Javascript ใดๆได้ด้วยเจ้าภาษานี้ โดย ClojureScript เป็นภาษาที่ดีมากๆ สำหรับการนำมาพัฒนาส่วน UI ของแอปพลิเคชันทั้งหลาย ไม่ว่าจะเว็บหรือบนมือถือก็ตาม สิ่งที่ทำให้ผมคิดว่ามันเป็นภาษาที่ดีมากๆสำหรับการพัฒนา UI ก็คือการมี Hot Reload ([Figwheel](https://www.youtube.com/watch?v=j-kj2qwJa_E)) ที่ดีและเสถียรมาก ซึ่งเป็นสิ่งที่ทำให้ Feedback loop ของการพัฒนาเราเร็วขึ้น เพราะว่าเมื่อเราแก้ไขโค้ดแล้วทำให้เราสามารถเห็นสิ่งที่เราแก้ไขได้ทันทีโดยไม่ต้อง reload หรือต้องกดปุ่มต่างๆจนมาถึงหน้าจอที่เรากำลังแก้อยู่ แล้วก็ยังมี REPL ที่เราจะสามารถทดลองโค้ด ทดลองรันฟังก์ชันก่อนที่เราจะเอาไปใช้งานจริงได้อีกด้วย
ในที่นี้ผมจะมาพาเขียน React Native (Mobile App) โดยจะใช้ ClojureScript framework ที่ชื่อว่า [re-frame](https://github.com/Day8/re-frame)


# Pre-requisite
 - NodeJs runtime [ดาวน์โหลดได้จากที่นี่](https://nodejs.org/en/download/)
 - JDK สำหรับรัน Clojure [ดาวน์โหลดได้จากที่นี่](http://www.oracle.com/technetwork/java/javase/downloads/index.html)  
 - Leiningen เป็น Build tools สำหรับ Clojure [ดาวน์โหลดได้จากที่นี่](https://leiningen.org/)  

# Setup
เราจะสร้าง React Native โปรเจ็กท์ ที่ใช้ [Expo](https://docs.expo.io/versions/v15.0.0/index.html) เพื่อความง่ายในการรันโปรเจ็กท์ขึ้นมา  

1. ติดตั้ง expo cli โดยใช้คำสั่ง

        npm install -g exp

1. สร้างโปรเจ็กท์ โดยใช้คำสั่ง

        lein new exponent mymobileapp

    [Expo+Cljs tutorial](https://docs.expo.io/versions/v15.0.0/guides/using-clojurescript.html)  
1. เข้าไปใน mymobileapp แล้วติดตั้ง node-modules ของ React Native และ Expo โดยใช้คำสั่ง

        yarn
        หรือ
        npm install

1. หลังจากนั้นทดสอบรันโปรเจ็กโดยการใช้คำสั่ง

        exp start --android
        หรือ
        exp start --ios
    ซึ่งจะได้ผลลัพธ์แบบนี้  
    ![app0](/img/cljs/app0.png)

1. ที่มือถือให้ลงแอ็ปชื่อ [Expo](https://expo.io/) แล้วลองสแกน QR Code ดู
จะพบว่าใช้ไม่ได้ 😝 เพราะว่าเรายังไม่ได้คอมไพล์โค้ด ClojureScript ของเราเลย 😝  

1. คอมไพล์โค้ดโดยการรัน Figwheel Server ด้วยคำสั่ง `lein figwheel` ซึ่งหน้าจอจะไปหยุดอยู่ที่แบบด้านล่าง เพื่อรอการเชื่อมต่อกับแอ็ปในมือถือ

        ;Prompt will show when Figwheel connects to your application


1. โดยปกติ exp จะรันในโหมด tunnel ซึ่งจะทำให้เราเปิดแอ็ปได้จากที่ไหนก็ได้ แต่ในที่นี้เราต้องการให้แอ็ปเราสามารถต่อกับ Figwheel Server ได้ เราจึงต้องให้มือถือต่อ Wifi เดียวกับคอม และตั้ง exp ให้รันในโหมด lan โดยการแก้ไฟล์ `.expo/settings.json` แล้วเปลี่ยนค่าตรง `hostType` เป็น  

        "hostType": "lan"

1. รัน exp อีกครั้ง สแกน QR Code ที่ได้ แล้วรอโหลดสักครู่ จะได้หน้าจอแบบนี้  
![app1](/img/cljs/app1.png)

1. ที่ figwheel repl ก็จะกลายเป็นแบบนี้เมื่อแอ็ปเราสามารถเชื่อมต่อกับ Figwheel Server ได้  
![app2](/img/cljs/app2.png)
1. สุดท้ายก่อนจะเริ่มเขียนโค้ด เขย่ามือถือเพื่อเรียก Developer Menu ขึ้นมา แล้วปิด Live Reload และ Hot Reloading เพราะ Figwheel ได้จัดการพวกนี้ไว้ให้แล้ว




# Let's Code
## (very) Bacis ClojureScript  
ผมจะขออธิบายคอนเซ็ปคร่าวๆ ของสิ่งที่จะใช้ในบทความนี้ 3 อย่างของ ClojureScript คือ  

1. Vector - ในภาษา Programming อื่นๆก็คือ Array ซึ่งใน ClojureScript นั้นก็ใช้สัญลักษณ์ [ ] เช่นกัน

        ; Vector ที่มีสมาชิกเป็น 1, 2, 3, 4, 5
        [1 2 3 4 5]
        ; ถ้าหากเทียบกับ Javascript ก็จะหน้าตาแบบนี้
        [1, 2, 3, 4, 5]


1. Map หรือ HashMap ซึ่งจะคล้ายกับ Object ใน Javascript  

        ; Cljs
        {
            :name       "ClojureScript"
            :creator    "Rich Hickey"    
        }

        // Js
        {
            "name": "ClojureScript",
            "creator": "Rich Hickey"
        }

1. การเรียกฟังก์ชัน - สมมติว่าเรามีฟังก์ชันบวกเลขที่รับ Parameter 2 ตัว

        ; Cljs
        (add 1 2)

        // Js
        add(1, 2)

จริงๆแล้วหากเราสามารถใส่ , ลงไประหว่างค่าแต่ละค่าได้ หากคิดว่าโค้ดอ่านยาก ซึ่งตัว ClojureScript compiler นั้นจะมองว่า , เหมือนกับการเว้นวรรคเปล่าๆ

## Reagent
ก่อนจะไปรู้จักกับ re-frame เราจะต้องรู้จักกับ Reagent ซะก่อน เจ้า Reagent นั้นก็คือ ClojureScript React wrapper ที่จะทำหน้าที่ในการแปลงโค้ด ClojureScript  ที่เราเขียนไปอยู่ในสิ่งที่ React รู้จักแล้วนำไปแสดงผลได้ โดย syntax ที่เราจะใช้ในการเขียน Reagent นั้นจะเหมือนกับว่าเราเขียน data-structure ของ ClojureScript เลย ซึ่งจะเป็นการสร้าง Vector (array) และ Map (hashmap) ธรรมดา  
systax จะมีโครงสร้างแบบนี้

    [component attributes child child]  ; โครงสร้างปกติ
    [component]                         ; โครงสร้างที่สั้นที่สุดที่ทำงานได้
    [Text "Hello World"]                ; ตัวอย่าง: Text คือ component, "Hello World" คือ child

ตัวอย่างโค้ดในแบบ JSX

    <View style={{flex:1, justifyContent: "center", alignItems: "center"}}>
        <Text>Hello React Native</Text>
    </View>

ถ้าเป็นใน Reagent ก็จะหน้าตาแบบนี้

    [View                               ; component
        {:style {                       ; attributes
            :flex 1
            :justify-content "center"
            :align-items "center"
        }}
        [Text "Hello React Native"]]    ; child


## re-frame
re-frame คือ reagent framework ซึ่งจะมีหน้าที่ในการจัดการข้อมูลต่างๆ ทั้ง App State หรือ  Handle ต่างๆ จากรูปด้านล่างหน้าที่ของ re-frame คือจัดการข้อ 1 ถึง 4 (จาก [6-domino cascade](https://github.com/Day8/re-frame#it-is-a-6-domino-cascade))  
![reframe](/img/cljs/reframe-dominos.png)

## Figwheel - Hot Reloading
ทดลอง Hot Reloading โดยการแก้ไขคำบนปุ่มเป็นคำว่า Hello และแก้ข้อความใน Alert เป็น Hello from Button
{{< gist jigkoxsee e4478ca1ad4e42112afb06742d0962d2>}}
เมื่อเรากด Save ไฟล์ ข้อความบนปุ่มจะเปลี่ยน โดยที่ไมไ่ด้มีการ reload และเมื่อกดปุ่มข้อความใน Alert box ก็จะถูกเปลี่ยนเรียบร้อยแล้ว

## REPL
ทดสอบการใช้งาน REPL โดยการสั่ง Alert  

1. ไปที่ Figwheel REPL  
1. เปลี่ยน namespace ไปที่ๆ มีโค้ด alert อยู่ โดยพิมพ์ดังนี้  

        (in-ns '[mymobileapp.core])

1. ทดลองสั่ง alert  (ฟังก์ชัน alert ถูกเขียนไว้ในบรรทัดที่ 8 - 9)

        (alert "Hi from REPL!")


- เพิ่มเติม - [คลิปตัวอย่างใช้ REPL] (https://www.facebook.com/jigkobenja/videos/vb.100000154047359/1638596939488758/?type=2&theater)


# Note - Command list
- สร้างโปรเจ็กท์

        lein new exponent <project-name>

- รัน Exp

        exp start --android
        exp start --ios

- รัน Figwheel Server

        lein figwheel

# Next
เนื้อหาในบทความนี้เป็นเพียงแค่การเริ่มต้นรันโปรเจ็กท์คือมาเล่นเท่านั้น ซึ่งอาจจะยังไม่เห็นภาพหรือยังไม่เข้าใจมากพอ 😆 รอบทความหน้าผมจะอธิบายถึงการใช้งาน re-frame โดยการทำแอ็ป Todo ซึ่งจะทำให้เห็นภาพการใช้งานที่เหมือนจริงมากขึ้นครับ 😁