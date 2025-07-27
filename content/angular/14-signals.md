---
title: "Signals"
date: 2025-07-26
draft: false
weight: 14
---

## Angular Signals - Yeni Nesil Reaktivite

Neden Böyle Bir Yapıya İhtiyaç Duyuldu?

Angular uzun yıllardır reaktif programlama ihtiyaçlarını karşılamak için RxJS kütüphanesini kullanıyordu. `Observable` `Subject` `pipe` `map` `switchMap` `subscribe` `unsubscribe` gibi yapılar bu sistemin parçalarıydı.

Ancak bu yapı:
- Öğrenilmesi karmaşık
- Hatalara açık
- Basit senaryolarda gereksiz yere ağır

hale gelebiliyordu. Angular ekibi bu sebeple daha basit, daha doğal, daha az kodla daha çok şey yapılabilen bir yapı üzerinde çalıştı. Ve Signal ortaya çıktı.

## Signal Nedir?

Signal, Angular uygulamasındaki bir değeri saklayan ve bu değer değiştiğinde kendisine bağlı her şeyi otomatik olarak güncelleyen bir yapıdır.


Tek cümleyle:
> Bir signal, “değer odaklı” ve “otomatik güncellenen” bir veri birimidir.

## Temel Kullanımı

```ts
import { signal } from '@angular/core';

const sayi = signal(0);

console.log(sayi()); // 0
sayi.set(10);
console.log(sayi()); // 10
```

Açıklama:

- `signal()` ile bir değer tanımlanır.
- `sayi()` ile değeri okunur.
- `sayi.set(...)` ile değeri güncellenir.

Signal'ler getter-fonksiyon gibi davranır. Bu, Angular'ın change detection (değişim algılama) sistemine uygun olarak çalışmasını sağlar.


## Signal Neden Kullanılır?

- Basit state yönetimi sağlar.
    - Component içinde değişken tanımlar gibi kullanılabilir.
- Reaktif davranış üretir.
    - Bir signal değiştiğinde, bağlı olduğu her yerde güncelleme tetiklenir.
- Performanslıdır.
    - Değişiklik yalnızca ilgili kısımları etkiler (fine-grained reactivity).
- RxJS’e kıyasla daha az karmaşıklık içerir.

## computed(): Türev Değer Oluşturmak

`computed()` fonksiyonu, bir veya daha fazla signal’a bağlı olarak otomatik hesaplanan bir değer üretir.

```ts
import { computed, signal } from '@angular/core';

const a = signal(2);
const b = computed(() => a() * 5);

console.log(b()); // 10

a.set(3);
console.log(b()); // 15 (otomatik güncellenir)
```

`computed()`, bağımlı olduğu signal’leri dinler. Onlarda değişiklik olduğunda kendi değeri de güncellenir.



## effect(): Yan Etkiler Üretmek
`effect()` fonksiyonu bir veya daha fazla signal değiştiğinde bir işlem yapmanı sağlar.

```ts
import { effect } from '@angular/core';

effect(() => {
  console.log("Sayı değeri değişti:", sayi());
});
```

Bu yapı, RxJS'teki subscribe mantığına benzer, ancak unsubscribe gerekmez. Angular otomatik olarak yönetir.

## linkedSignal(): İki Yönlü Veri Bağlantısı

Eğer bir signal başka bir signal’e bağlı olacak ama aynı zamanda yazılabilir olacaksa `linkedSignal` kullanılır.

```ts
import { linkedSignal } from '@angular/core';

const isim = signal("Ali");

const isimVeSoyisim = linkedSignal({
  get: () => isim() + " Yılmaz",
  set: (yeniDeger) => {
    const parcalar = yeniDeger.split(" ");
    isim.set(parcalar[0]);
  }
});

console.log(isimVeSoyisim()); // "Ali Yılmaz"

isimVeSoyisim.set("Ahmet Kaya");
console.log(isim()); // "Ahmet"
```

Bu yapı özellikle form alanlarında veya input-output bağlantılarında işe yarar.

## Signals vs Observables (RxJS)

```txt
| Özellik        | Signal                        | Observable (RxJS)               |
| -------------- | ----------------------------- | ------------------------------- |
| Kullanım Amacı | UI state yönetimi             | Asenkron veri akışı             |
| Temizlik       | Gerekmez                      | unsubscribe() gerekir           |
| Öğrenme Eğrisi | Düşük                         | Yüksek                          |
| Reaktiflik     | Fine-grained (hassas)         | Broad (yaygın)                  |
| Performans     | Yüksek (template’te optimize) | Genelde yeterli, ama daha genel |
```

Signals UI tabanlı reaktivite için tasarlanmıştır. RxJS ise daha çok veri akışı, socket, HTTP gibi dış kaynaklar için uygundur.


## resource() ve httpResource(): Signal ile Asenkron Veri

Signal yapısını HTTP gibi async kaynaklarla da kullanmak mümkün:

```ts
const kullaniciVerisi = resource(() =>
  fetch("/api/kullanici").then(res => res.json())
);
```


Ya da Angular 17 ile gelen `httpResource()`:

```ts
const kullanici = httpResource(() =>
  this.http.get('/api/kullanici')
);
```

Template'te şu şekilde erişilir:

```html
<p>{{ kullanici().data.ad }}</p>
```

Bu yapı, loading/error durumlarını da içerir.


## Angular API’leriyle Entegrasyon (Input, Output, ViewChild)

Signal mimarisi Angular’ın diğer alanlarına da entegre edilmeye başladı:

```ts
@Input() veri = input.signal<string>();
@Output() secildi = output.signal<void>();
@ViewChild('element') el = viewChild.signal<ElementRef>();
```

Yani signal sadece bir “değer yapısı” değil, Angular’ın genel mimarisine yerleşen bir temel parça haline gelmeye başladı.


## Ne Zaman Signal, Ne Zaman RxJS?

- UI state yönetimi, sayfa içi basit reaktivite: Signal
- Asenkron veri akışı, stream, socket, API: Observable (RxJS)
- Birden fazla data kaynağı birleşecekse (merge, switchMap): RxJS

> İkisini birlikte kullanmak da mümkündür. Örneğin Observable’dan signal oluşturmak için toSignal() fonksiyonu vardır.


Sonuç

Angular Signals:

- Daha sade bir reaktif yapı sunar.
- Performansı arttırır.
- Öğrenme ve kullanımı kolaylaştırır.
- Angular’ın geleceğinde merkezi bir yer tutacaktır.


RxJS ise hâlâ güçlüdür ve signals yapısı onu tamamen ortadan kaldırmaz. Ancak Signals, özellikle component içi UI reaktivitesi için daha uygun bir alternatiftir.