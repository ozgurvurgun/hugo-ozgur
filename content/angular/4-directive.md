---
title: "Directive"
date: 2025-07-15
draft: false
weight: 4
---


Bir düşünelim: HTML temelde birr takım verinin semantik şekilde gösterilemsinden ibaret. Bir noktada bu statik yapıya takla attrmak istiyorsun. "Bu elementi sadece bazı durumlarda göster", "Şuna tıklandığında şu davranış tetiklensin", "Şu div'i mavi yap ama bazı senaryolarda yeşil olsun" falan diyorsun.

Bir noktada bu davranışların soyutlanması icap ediyor. Yani sadece ne göreceğini değil, nasıl davranacağını da kontrol etmek istiyorsun.

Bu meta programlama dediğimiz yaklaşımın ilk adımıdır:

> "Kodu kodla şekillendirme işi"

JavaScript’te bunu `Object.defineProperty`, `Proxy`, `Reflect`, `eval` gibi araçlarla yaparsın.  
Ama frontend framework’lerinde(backend içinde geçerli) bu işi yapmak için özel daha soyut mekanizmalar geliştirilmişitr: **directive** bunlardan biridir.