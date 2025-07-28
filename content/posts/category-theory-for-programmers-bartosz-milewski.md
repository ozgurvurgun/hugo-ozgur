---
title: Category Theory for Programmers - Bartosz Milewski
date: 2025-07-28
draft: false
tags:  #tr #science #math #software
---

Teorik fizikçi, programcı ve matematikçi (adamın olmadığı masa yok lol) Bartosz Milewski'nin yazdığı *Category Theory for Programmers* kitabını okuyorum ve bu blog serisinde bölüm bölüm özetleyeceğim.

Kendi adıma: bağlamdan kolay kopuyorum ve hızlı unutuyorum. Bu yüzden kitabı her 20 sayfalık bloklar halinde okuyarak, anladıklarımı burada toparlayacağım. Böylece hem kendim için kalıcı hale getirmiş olacağım hem de başkalarına faydalı olursa ne mutlu.

Bu yazıda, 20 sayfalık bölümler hâlinde notlarım ve özetlerim yer alacak. Vakit buldukça güncelleyerek ilerleyeceğim.

---

## Önsöz - Yok mu bir kategori teorisi anlatan abi?

Bartosz Milewski, bu kitabı yazma motivasyonunu şöyle açıklıyor: "Ben bu işi bilim insaları için değil, doğrudan programcılar için yapıyorum". Yani hedef kitlesi bilgisayar bilimcileri değil, sistem kuran, kod yazan, üretmeye çalışan mühendisler.

Kitap boyunca anlatacağı şey matematiğin en soyut dallarından biri olan kategori teorisi ve bu senin benim gibi çinko karbon bir developer için ilk bakışta oldukça korkutucu. Ama yazarın hedefi tam olarak bu ön yargıyı kırmak.

## Programcılara Neden Kategori Teorisi?

Yazarın üç temel gözlemi var:

- **Kategori teorisi çok fatdalı şeyler içeriyor ama henüz çok yayılmış değil.** 
Haskell gibi fonksiyonel dillere bu fikirler çoktan sızdı ama yayılım hızı düşük. "Bunu hızlandırmamız gerekiyor" diyor. Çünkü bu fikirler sadece teori değil, doğrudan işimize yarıyor.
<br /><br />
- **Matematik sıkıcı olmak zorunda değil:**
Belki kalkülüs ya da cebir sevmiyorsun, ama kategori teorisi başka bir şey. Bu alan yapı odaklı ve zaten yazılımcı olarak biz de yapılarla ilgileniyoruz: modüller, bileşenler, interface'ler bla bla...
<br /><br />
- **Matematik kasabında bir kasap bıçağı var:**
Milewski diyor ki: "Matematikçi gibi davranmayacağım." Yani her tanımı formel, her ispatı sıkıcı bir şekilde yapmayacak. Çünkü matematikçilerin yazdıkları metinleri anlamak genelde sen-ben için işkencedir. Onun yerine, fizikçiler gibi sezgisel açıklamalar yapacak.

## Haskell mi C++ mı?
Yazar, konuları kod üzerinden anlatacak. Fonksiyonel diller (özellikle Haskell), kategori teorisini anlatmak için çok uygun çünkü zaten "matematiğe yakınlar". Ama kitap sadece Haskell ile sınırlı değil. 
- **Haskell:** Kısa ve öz, fikirleri anlamak için müko.
- **C++:** Gerçek dünyadaki programcılar için kaçınılmaz. Örnekleri çoğu zaman C++ ile verecek. Ama burada Haskell'i yine de anlayacak kadar bilmek gerekiyor. Yavaş yavaş Haskell'i tanıtacak.


Özetle: Haskell bilmek zorunda değilsin ama öğrenirsen çok rahat edersin.

## Bu kadar yıldır kategori teorisi olmadan kod yazıyorduk?

Yazar, burada biraz tarihsel gidişata değiniyor: Fonksiyonel pradigmalar artık her dili etkliyor. [Java bile lambdaları aldı](#lambda-ifadesi),  [C++ zaten değişiyor](#cpp-degisimi). Bu rastgele değil, büyük bir kırılma geliyor: yazılımda paradigma değişimi.


Bu değişimi tetikleyen ana etkenlerden bazıları:
- **Çok çekirdekli işlemciler:** [Paralel programlama OOP ile yürümüyor](#paralel-oop). Veri gizleme, paylaşım ve mutasyon bir aradaysa, orada kilitlenme ve yarış koşulları kaçınılmaz.
- **Artan yazılım karmaşıklığı:** Yan etkili fonksiyonlar küçğkken problem değil, ama büyüyen sistemlerde kontrolden çıkıyorlar. Yan etkiler ölçeklenemiyor.

Ve işin bam teli burada geliyor:
Bartosz’un dediği gibi, biz aslında yavaş yavaş kaynayan bir yazılım kazanının içindeyiz.

> Kurbağayı kaynar suya atarsan zıplar çıkar. Ama soğuk suya koyup yavaş yavaş ısıtırsan, fark etmeden haşlanır.

Yani:
- Her şey bir anda değişmedi ama...
- Yıllardır OOP yazarken, sistemlerin altı ısıtılıyor.
- Paradigma değişimi sessizce yaklaşıyor.
- Sen hâlâ eski usul kod yazıyorsan: belki de kaynamaktasındır `¯\_(ツ)_/¯`
## Programcılık = Modern gotik katedral

Kitabın önsözü çok güzel bir metaforla bitiyor:

> Gotik katedralleri inşa eden insanlar, o dönemin sınırlı fiziğiyle ve yapı bilgisizliğiyle mucizeler yarattı. Biz de yazılımı, oldukça zayıf teorik temellerle bugünlere kadar getirdik.

Ama artık taşıyıcı kolonlar çatırdıyor. Daha sağlam soyut temellere ihtiyacımız var. Kategori teorisi de işte tam burada devreye giriyor.




<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

## Kavram Kutusu

### <a id="lambda-ifadesi">Lambda ifadesi nedir?</a>
Lambda, isimsiz fonksiyon demektir.  
Örneğin:
```java
(x) -> x * 2
```
veya
```c++
[](int x) { return x * 2; }
```
Lambdalar sayesinde fonksiyonlar daha kısa yazılır, özellikle map/filter/reduce gibi işlemlerde faydalıdır.

-------------------------------------------

###  <a id="cpp-degisimi">C++ nasıl değişiyor?</a>
C++11 sonrası birçok modern özellik kazandı:
- `auto` `nullptr` `range-based for`
- Lambda fonksiyonlar
- `std::optional` `concepts` `coroutines` Yani C++ artık daha soyut ve fonksiyonel düşünmeye uygun hale geliyor.


-------------------------------------------

### <a id="paralel-oop">Paralel programlama OOP ile neden yürümüyor?</a>
OOP veriyi gizler ve değiştirir. Ama paralel programlama, aynı anda aynı veriye erişmek ister. Bu çakışma race condition (yarış durumu) ve deadlock (kilitlenme) gibi sorunlara yol açar.

Örneğin:
```c++
counter++;
```
Bu basit satır bile aslında üç adımdır: oku -> artır -> yaz. İki thread aynı anda iş yaparsa biri diğerini ezebilir. Fonksiyonel programlama ise değişmeyen (immutable) verilerle çalışır. Hiçbir şeyi değiştirmediğin için hiçbir şey çakışmaz. Bu da paralelliği kolaylaştırır.

-------------------------------------------


```txt
Bu yazıyı tek bir sayfada kavram kutusu ile beraber yazarsam feci uzun olacak ve çorbaya dönecek gibi. En iyisi yeni bir section açıp orada parça parça paylaşmak olacak
```