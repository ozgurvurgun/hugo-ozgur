---
title: "Service"
date: 2025-07-17
draft: false
weight: 5
---

## Angular Service Nedir?

Angular Service dediğimiz şey, uygulamada ortaklaşa kullanılan işleri ve verileri tek bir yerde toplayan TypeScript sınıflarıdır. Misal:

- API'den veri çekmek  
- Bileşenler arasında veri paylaşmak  
- Ortak bir hesaplama, filtreleme, validasyon işlemini yapmak

gibi işler için service yazarsın. Böylece aynı kodu her component’te yeniden yazmazsın.

---

## Ne Diye Service Denen Zımbırtıları Kullanıyoruz?

Bir örnek: Hem `AComponent` hem `BComponent` aynı veriyi kullanmak istiyor. Gidip her birine ayrı ayrı veri koyarsan kopukluk olur. Değişiklik olduğunda senkron tutmak eziyet.

O yüzden:

- Ortak veri ve iş mantığı bir Service'e atılır.
- Component’ler bu servise erişip ortak veriyi görür ve hatta birbirlerine haber uçurabilir (RxJS ile).

**Yani Service = Ortak akıl.**

---

## Nasıl Yazılır?

Bir Service yazmak, düz bir TypeScript class yazmak gibidir. Ama `@Injectable()` dekoratörü ile Angular'a "bunu enjekte edilebilir hale getir" deriz.

```ts
// src/app/my.service.ts

import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root' // bu servis uygulamada singleton olarak kullanılacak
})
export class MyService {
  private count = 0;

  getCount() {
    return this.count;
  }

  increase() {
    this.count++;
  }
}
```

## Nasıl Yazılır?

Servis, component'e dependency injection yöntemiyle sokulur.

```ts
// src/app/my-component.component.ts

import { Component } from '@angular/core';
import { MyService } from './my.service';

@Component({
  selector: 'app-my-component',
  template: `
    <p>Count: {{ count }}</p>
    <button (click)="increase()">Artır</button>
  `
})
export class MyComponent {
  count = 0;

  constructor(private myService: MyService) {}

  increase() {
    this.myService.increase();
    this.count = this.myService.getCount();
  }
}

```

Component servisle iletişim kurar, servis veriyi saklar. Bu sayede başka bir component de o veriye erişebilir.

## Özet

Mevzu basit düz mantık. OOP yazarken her yere aynı metodu yazmazsın, bir sınıfa koyarsın, çağırırsın.

Angular’da da tekrar eden işleri, veri paylaşımlarını ya da dış kaynak işlemlerini (API gibi) Service'e koyarsın. Gerektiği yerde çağırırsın. Hem kodun temiz olur hem de yönetilebilir olur.

> Mesela diyelim ki bir projede farklı yerlerde türev alman gerekiyor. Ne yani, her seferinde f(x) = ... yazıp elle mi türev alacaksın?
Tabii ki hayır. Gidersin bir MathService yazarsın, içine derivative() diye bir metod koyarsın. Sonra lazım oldukça this.mathService.derivative(...) diye çağırırsın, olay biter.

Angular Service işte tam da böyle bir şey

Ortak bir işlev varsa, tek bir yere koyarsın sonra ihtiyaç duyan her component oradan kullanır. Ne karmaşa olur, ne tekrar.