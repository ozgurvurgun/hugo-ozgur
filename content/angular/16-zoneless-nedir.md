---
title: "Zoneless Nedir?"
date: 2025-07-27
draft: false
tags: #tr
weight: 16
---

Angular deyince akla genelde component’ler, service’ler falan gelir ama içerde bir mimari var ki, senin haberin bile olmadan bütün uygulamanın kaderini etkiliyor:


## Zone.js

Bu yazıda Zone.js nedir, ne iş yapar, neden Angular artık onsuz olmaya çalışıyor ve bu neye çare olabilir… hepsine tek tek gireceğiz.

## Zone.js Nedir?

Kısaca:

> Zone.js, JavaScript'teki async olayları izleyen bir kütüphanedir.


Yani sen `setTimeout`, `fetch`, `Promise`, `addEventListener` falan kullandığında, Angular’ın "bir şey oldu galiba" deyip yeniden UI güncellemesi yapmasını sağlar.


Örnek:

```ts
setTimeout(() => {
  this.sayac++;
}, 1000);
```

Burada sayac++ çalıştıktan sonra Angular otomatik olarak DOM'u günceller. Ama sen ona "sayac değişti" demedin ki?


İşte bunu Zone.js izliyor. Her async işlemi kendi “bölgesi” içine alıyor ve sonrasında Angular’a diyor ki:

> Hocam biri bir şey yaptı, sen bir change detection çalıştır yine de.


## Peki Angular’daki Yeri Ne?

Angular, yıllarca Zone.js üzerine kurulu bir otomatik değişiklik algılama (change detection) sistemi kullandı.

Bu şu demek:

- Her `setTimeout` `click` `fetch` `input`... tetiklenince Angular DOM’u tekrar tarar.

## Avantaj:
Kafana göre async iş yap, Angular kendisi anlar.
## Dezavantaj:
Aşırı fazla gereksiz render olur. DOM’u tekrar tekrar kontrol etmek ciddi performans yükü oluşturur.


## Zoneless Ne Demek?
> Zoneless = Zone.js kullanmadan çalışmak

Yani artık Angular:
- Otomatik olarak “her async işten sonra DOM güncellemesi yapayım” kafasından çıkıyor.
- Bunun yerine, sen söylersen güncellerim felsefesine geçiyor.

Bu değişim Angular Signals ile mümkün oldu. Signals, reaktif ve “ne zaman neyin değiştiğini açıkça bilen” bir sistemdir.


## Signals + Zoneless = Kontrollü Performans

Zone.js olmadan çalışmak için bir mekanizma gerekiyordu. Signals bunu getirdi.

Nasıl?

Signal kullandığında veri değişimlerini manuel bildiriyorsun:

```ts
count.set(count() + 1);
```

Bu ifade hem veriyi değiştirir, hem de "bu veri değişti" sinyali verir. Angular da “heh tamam DOM’u güncelleyeyim” der.

Böylece Zone.js gibi bütün her şeyi izlemek yerine, sadece senin işaret ettiğin şeyleri takip eder.


## Spagettiye Dönmeyelim mi?

Tabii ki bu sistemle şunu yapmıyoruz:

```ts
count++;
detectChanges();
```

Bu tam manuel olurdu ve Angular’ı jQuery’ye çevirirdi. Signal’ler zaten içsel olarak change detection yapar ama minimumda. İşin özü: kontrollü otomasyon.


## Neden Zoneless?

```txt
| Sebep                   | Açıklama                                                           |
| ----------------------- | ------------------------------------------------------------------ |
| Performans              | Zone.js yüzünden gereksiz yüzlerce CD tetikleniyor                 |
| Predictability (Öngörü) | Zone.js her şeyi izlerken ne zaman ne olur belli değil             |
| Basitlik                | Zoneless + Signals ile sistem daha sade                            |
| Mobil uyumluluk         | Özellikle NativeScript / Tauri gibi yapılarda Zone sorun çıkarıyor |
| Test Kolaylığı          | Her şey izlenmediği için side-effect kontrolü daha kolay           |
```

## Peki Ne Kaybederiz?

- Kolaylık: Eskiden hiçbir şey yapmadan DOM güncellenirdi.
- Geriye dönük uyumluluk: Tüm kodları signal’a geçirmek zaman alabilir.


## Ne Zaman Zoneless Kullanmalı?

```txt
| Durum                              | Zoneless Tercihi? |
| ---------------------------------- | ----------------- |
| Büyük-scale SPA (admin panel, CRM) | Evet              |
| Performans hassasiyeti olan UI     | Evet              |
| Basit tek sayfalık site            | Gerekmez          |
| 3rd-party lib’lerle çok iç içeysen | Zor olabilir      |
```

## Zoneless Nasıl Aktif Edilir?

Angular 17+ ile gelen yeni `provideZone: false` opsiyonu:

```ts
bootstrapApplication(AppComponent, {
  providers: [
    provideHttpClient(),
    provideRouter(routes),
    { provide: NgZone, useValue: 'noop' }
  ]
});
```

Ya da:

```ts
import { provideZoneChangeDetection } from '@angular/core';

bootstrapApplication(AppComponent, {
  providers: [provideZoneChangeDetection('noop')]
});
```
> Bu, Zone.js’yi pasif hale getirir. Artık her şey senin kontrolünde.

## Signals’sız Zoneless Olmaz mı?

Zor. Çünkü veri değişimini Angular’a bildirmen gerekir.
Eğer signals kullanmıyorsan, değişiklik sonrası şu işi sen yaparsın:

```ts
this.cdRef.detectChanges();
```
Ama bu yine manuel. Yani ya Signals’a geç, ya da sürekli CD tetikle.

## Son

Zoneless Angular bir "ileriye dönüş" hareketidir.
Eskiden her şey otomatikti, ama kontrolsüzdü. 
Şimdi ise geliştirici daha bilinçli davranıyor, neyin değiştiğini açıkça söylüyor.