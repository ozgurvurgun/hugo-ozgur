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


## Directive Ne İşe Yarar?

Directive, bir HTML elementine meta düzeyde davranış eklemeni sağlar.

Diyelim ki:

- Bir `<div>`'i sadece login olmuş kullanıcılar görsün istiyorsun -> `ngIf`
- Bir `<button>` tıklandığında animasyon başlasın istiyorsun -> özel directive
- Bir yazı sarı arka plana gelsin istiyorsun -> özel directive

Angular burada sana şunu diyor:
> Sen davranışı soyutla, ben onu DOM'a enjekte ederim.

Bu imperative kod yazmak yerine, declerative yapı kurmanı sağlar.


## Angular'ın Directive Türleri

1) **Component:**
Her component aslında bir directive'dir ama kendi şablonu vadır.

2) **Structural Directive:**
DOM  üzerinde yapısal değişiklik yapar (element ekler/siler)

    - `ngIf`
    - `ngFor`
    - `ngSwitch`

3) **Attribute Directive:**
Mevcut bir elementi davranışsal veya görsel olarak değiştirir.

    - `ngClass`
    - `ngStyle`
    - Senin yazacağın kendi directive'in

## Kendi Directive'imizi Yazalım

```bash
ng g d highlight
```

```ts
import { Directive, ElementRef } from '@angular/core';

@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {
  constructor(el: ElementRef) {
    el.nativeElement.style.backgroundColor = 'yellow';
  }
}
```

Kullanım:

```html
<p appHighlight>Benim arka planım sarı!</p>
```

Angular bu etiketi görünce şunu yapar:
> Bu elemente `HighlightDirective` uygulanacak. Gİt davranışı DOM'a enjekte et.

## Ne Zaman Kullanılır?

- Component çok ağır gelir, sadece küçük davranışlar gerekiyordur.
- Tekrar eden DOM manipülasyonları vardır.
- Başkalarının HMTL'ine dokunmadan davranış eklemek istersin (özellikle design system'larda)


## Directive = Hafifletilmiş Meta Programming

Directive kavramı, DOM üzerinde davranışı programatik şekilde değiştirmeye olanak tanıyan bir meta düzey soyutlamadır.

Angular bunu:
- Sınıflar
- Decorator’lar (`@Directive`)
- Lifecycle'lar
- Dependency Injection

ile harmanlayarak verir. Sana sadece ne yapmak istediğini tanımlamak kalır.

## Özetle
- Directive, HTML’in “yetersizliğine” karşı ortaya çıkmış bir çözümdür.
- Angular’da davranışı soyutlamak için kullanılır.
- Hem görsel (attribute) hem yapısal (structural) değişiklikler için kullanılır.
- Kendi directive’ini CLI ile kolayca oluşturup kullanabilirsin.

Bugün DOM’a davranış enjekte ettik, yarın belki DOM’un ta kendisini sorgularız.
Ama mesele hep aynı:
“Statik olanı dinamikleştir, tekrar edeni soyutla.”