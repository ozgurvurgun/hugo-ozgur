---
title: "Tarayıcıda Neden C++ Çalışmazdı?"
date: 2025-07-26
draft: false
---

Şunu web kariyerimizin ilk noktalarında çoğumuz düşündük:

> Madem C++ çok hızlı bir dil, neden tarayıcılar doğrudan C++ çalıştırmıyor da JavaScript gibi bir ara katmanla uğraşıyoruz?

Çok makul bir soru. Ama cevabı sadece teknik değil; biraz tarih, biraz felsefe, biraz da insanlık hali.


Web "Devrimle" Değil, Yamayla Gelişti

Web’in ilk amacı sadece belge sunmaktı. 90’larda insanlar sadece `<h1>Hoşgeldiniz</h1>` yazıyordu.  
Dinamiklik? Yoktu.


Zamanla:


- Formlar geldi
- Butonlar kondu
- “Şuna tıklayınca bu olsun” denmeye başlandı

Ve çözüm arandı. Netscape’te Brendan Eich’e dediler ki:  
> “Bize hızlıca bir dil yaz.”  
Adam 10 günde JavaScript’i doğurdu.

Yani:

> JavaScript bir mühendislik mucizesi değil, ihtiyaca hızlı verilen bir cevaptı.

C++ gibi derlenen, platforma özel, karmaşık bir dil değil.  
Yama gibi… ama işe yarayan yama.

## Güvenli C++ Diye Bir Şey Yoktur

C++ çok güçlüdür. Ama çok tehlikelidir.

Bir kullanıcı tarayıcıda C++ kodu çalıştırsa:
- Bellek taşması yaratabilir
- Dosya silebilir
- Sistemi hackleyebilir

> “Ama bunları engelleyen güvenli bir C++ versiyonu yapılmaz mıydı?”

Yapılsaydı elimizde kalan zaten C++ olmayan bir şey olurdu.

“Şurası yasak, pointer yok, malloc yok” diye diye sonunda sıfırdan bir dil doğardı. Ki Google Dart ile bunu denedi. Çok şey vaat etti. Ama olmadı.


## Web Geliştirici Profili = Her Seviyeden İnsan

Frontend dünyasında sadece süper yazılımcılar yok.
- Öğrenci var
- Tasarımcı var
- No-code geliştirici var


C++ ile kim uğraşacak?

Ama JavaScript?

```js
console.log("Hello world");
```

Her tarayıcıda çalışıyor. Kurulum yok.
JavaScript = Evrensel geliştirici dili


## C++’ın Tarayıcıda Çalışması Teknik Olarak Zor
Tarayıcıda C++ çalıştıralım diyorsun, peki:
- Binary dosyayı nerede çalıştıracaksın?
- Hangi CPU mimarisi için derleyeceksin? (x86, ARM, M1?)
- Dosya sistemine erişim verecek misin?

JavaScript ne yapıyor?

```html
<script src="app.js"></script>
```

Ve her yerde çalışıyor.
İşte Web’in gücü bu: Evrensel yürütülebilirlik.


## Çözüm: WebAssembly

Tarayıcıda C++ çalıştırmanın bugünkü yolu ne?
**WebAssembly (WASM)**
- C/C++ kodunu derliyorsun
- Bytecode (wasm) elde ediyorsun
- Tarayıcıda JS ile çağırıyorsun

Örnek:

```c
// topla.c
int topla(int a, int b) { return a + b; }
```
```bash
emcc topla.c -o topla.wasm
```
Ve JavaScript'ten çağırıyorsun.
Ama dikkat: yine JavaScript üzerinden çağırıyorsun.


## Felsefi Sonuç: Soyutlama Kaçınılmazdır

> “En hızlısı C++, niye araya katman soktuk?”
Çünkü yazılımın özü soyutlamadır.

- Donanım -> Assembly -> C -> C++ -> JavaScript
- Her adımda bir şey kaybedersin (hız, kontrol)
- Ama bir şey kazanırsın: uyumluluk, erişilebilirlik, üretkenlik

> Yazılımda “biraz yavaş ama herkes yazabiliyor” bazen “çok hızlı ama kimse kullanamıyor”dan daha değerlidir.


Ve şunu unutma:

> Yazılım, sadece bilgisayarlarla değil, insanlarla da çalışmalıdır.