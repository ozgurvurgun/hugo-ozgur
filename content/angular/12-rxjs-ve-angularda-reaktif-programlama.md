---
title: "RxJS ve Angular’da Reaktif Programlama"
date: 2025-07-23
draft: false
weight: 12
---


## Reaktif programlama Nedir?

Şöyle düşün:

> Bir şey değiştiğinde, başka bir şey otomatik değişsin istiyorum

Bu fikir temel olarak reaklam panosuna benzer. Panoya fiyatı asarsın, her yerde o fiyat görünür. Ama panoya yeni fiyatı yazdığında tüm afişler otomatik güncellenir.

Buna veri akışına tepki verme yani reaktif olma denir.

## RxJS Nedir?

RxJS, "Reactive Extensions for JavaSript" anlamına gelir. Yani: "JavaScript'e reaktif yetenek ekleyelim" projesidir.

Angular, fromdan HTTP'ye router'dan event'lere kadar her şeyi `Observable` sistemiyle yönetir. Çünkü RxJS:

- Akışlar oluşturmanı sağlar (data stream)
- Akışa abone olmanı sağlar (observer)
- Akışı dönüştürmeni sağlar (operatörler)
- Memory leak riskini yönetmeni sağlar (unsubscribe)


## Observable Nedir?

Observable, gelecekte bir veya daha fazla veri gönderebilen bir kaynaktır. Bir `Promise`, sadece bir defa veri döner. Ama Observable, sonsuz veri döndürebilir.

## Analoji

- Observable bir gazete bayisidir
- Abone olursun (subscribe)
- Her gün gazete gödnerir
- Canın sıkılırsa aboneliği iptal edersin (unsubscribe)

Basit Observable

```ts
import { Observable } from 'rxjs';

const myStream = new Observable(observer => {
  observer.next('Merhaba');
  observer.next('Dünya');
  observer.complete();
});

myStream.subscribe(data => console.log(data));
```

## Angular Neden RxJS kullanır?

Çünkü Angular her şeyin bir akışı olduğunu düşünür:

- HTTP isteği:  `this.http.get(...)` -> `Observable`
- Router parametresi: `this.route.params` -> `Observable`
- Form değişikliği: `form.valueChanges` -> `Observable`
- Timer: `interval(1000)` -> `Observable`

Bu sayede sabit değil, sürekli değişebilen bir dünyayı kolayca yönetebilirsin.


```txt
| Özellik            | Promise  | Observable          |
| ------------------ | -------- | ------------------- |
| Kaç kez veri verir | Bir kez  | Sonsuz kez olabilir |
| Cancel edilebilir? | Hayır    | Evet                |
| Operatör var mı?   | Yok      | Evet                |
| Lazy mi?           | Evet     | Evet                |
| Chain yapısı       | then()   | pipe()              |

```


## Angular'da Günlük RxJS Kullanımı

HTTP

```ts
this.http.get('/api/users')
  .subscribe(data => this.users = data);
```


Router Param Takibi

```ts
this.route.params
  .subscribe(params => console.log(params['id']));
```

Form valueCHanges

```ts
this.form.valueChanges
  .subscribe(value => console.log(value));
```

## Operatörler (map, filter, switchMap vs.)

RxJS'te veriyi işlememizi sağlayan yapı `pipe()` içinde kullanılan operatörlerdir.

En yaygınları:

```txt
| Operatör       | Ne yapar?                                     |
| -------------- | --------------------------------------------- |
| map            | Veriyi dönüştürür                             |
| filter         | Belirli koşullara göre süzer                  |
| switchMap      | Yeni observable başlatır, öncekini iptal eder |
| debounceTime   | Belirli süre boyunca bekler                   |
| catchError     | Hataları yakalar                              |
```

Örnek:

```ts
this.searchControl.valueChanges.pipe(
  debounceTime(300),
  filter(value => value.length > 2),
  switchMap(query => this.api.search(query))
).subscribe(results => this.results = results);
```

## Unutma Unsubscribe Gerekebilir

Observable'lar sonsuz olabilir. Eğer sen aboneliği iptal etmezsen memory leak oluşabilir.

## Manuel Unsubscribe

```ts
subscription: Subscription;

ngOnInit() {
  this.subscription = this.form.valueChanges.subscribe(...);
}

ngOnDestroy() {
  this.subscription.unsubscribe();
}
```

Ya da `takeUntil`, `async pipe`, `untilDestroyed` gibi yöntemlerle otomatik kontol edebilirsin.


## Sonuç: Kod Yazmak Değil, Akışla Yaşamak

RxJS ve reaktif programlama sana şunu öğretir:
> Veriyi zorla alma, ona tepki ver

Angular'da veri sadece bir değişken değil, bir akıştır.
Sen o akışı dönüştürür, filtreler, şekillendirir ve ona abone olursun.

