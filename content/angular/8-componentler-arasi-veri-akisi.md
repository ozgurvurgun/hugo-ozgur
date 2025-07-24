---
title: "Componentler Arası Veri Akışı"
date: 2025-07-23
draft: false
weight: 8
---

##  Angular'da Bileşenler Konuşur mu?

Bileşenlerinizi oluşturuyorsunuz:
- `AppComponent`  
- `NavbarComponent`  
- `UserCardComponent`  
- `TodoListComponent`

Ama bir sorun var:
Bunlar yalnızca görüntü mü veriyor, yoska birbirleriyle konuşabiliyorlar mı?
Cevap: Evet konuşabilirler. Ama sadece belirli yollarla.

## Angular'ın Felsefesi: Tek Yönlü Veri Akışı

> Angular'da veri akışı: Parent ➡️ Child

Yani üst bileşen, alt bileşene veri gönderir. Tersini yapmak istersen, child bileşen sadece bir event fırlatabilir.

> "Benim içimde bir şey oldu, haberin olsun" der.

Bu mekanizma için 2 araç kullanırız:
- `@Input()` parent’tan child’a veri gönderir
- `@Output()` child’tan parent’a event fırlatır

## @Input() – İçeri Veri Almak

- Kullanım senaryosu:
`UserCardComponent` içinde kullanıcı bilgisini göstermek istiyorsun. Ama bu bilgi `AppComponent`' te duruyor.

Child Component (`user-card.component.ts`)

```ts
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-user-card',
  template: `<p>{{ user.name }}</p>`
})

export class UserCardComponent {
  @Input() user: any;
}
```

Parent Component
```html
<app-user-card [user]="activeUser"></app-user-card>
```
> [user] Angular’a der ki: “Bu attribute sabit bir string değil, bir değişken.”

## @Output() – Dışarı Event Göndermek

Şimdi de tersine bakalım:

`UserCardComponent` içinde bir buton var ve basıldığında parent bir aksiyon alsın istiyorsun.

Child Component

```ts
import { Component, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-user-card',
  template: `<button (click)="selectUser()">Seç</button>`
})
export class UserCardComponent {
  @Output() selected = new EventEmitter<void>();

  selectUser() {
    this.selected.emit(); // olay fırlatıldı
  }
}
```

Parent Component
```html
<app-user-card (selected)="handleUserSelected()"></app-user-card>
```

Parent bileşen olayı dinler ve `handleUserSelected()` fonksiyonunu çalıştırır.


## Veri Akışı Özet Dİyagram

- Parent ➡️ @Input ➡️ Child
- Child ➡️ @Output ➡️ Parent

## Parametreli @Output

Evet sadece boş değil, veri taşıyarak da fırlatılabilir.

```ts
@Output() selected = new EventEmitter<number>();

selectUser() {
  this.selected.emit(this.user.id);
}
```

```html
<app-user-card (selected)="handleSelection($event)"></app-user-card>
```

Parent bileşen `$event` parametresi, emit edlien değeri taşır.

## Sık Yapılan Hatalar

- `@Input` ile gelen veriyi doprudan değiştirmek (anti pattern)
- `@Output`' u event gibi değil, veri gibi kullanmaya çalışmak
- `EventEmitter`'ı servis içinde kullanmak (gereksiz)
- Parent verisi daha geç geliyorsa `undefined` hatası -> `?` operatörü kullan: `user?.name`

## @Input İle @ Output Ne Zaman Yeterli Gelmez?
Eğer veri paylaşımı:

- Çok sayıda bileşen arasında oluyorsa
- Aralrında parent-child ilşkisi yoksa
- App-wide state gerekiyorsa

O zaman Service + Dependency Injection kullanmak gerekiyor.
(Hatta daha büyük projelerde state management library (NGRX, Akita) gibi çözümler)


## Alternatif Yöntemler

| İhtiyaç                                  | Çözüm                           |
| ---------------------------------------- | ------------------------------- |
| Parent ↔️ Child iletişimi                | `@Input()` & `@Output()`        |
| Sibling ↔️ Sibling iletişimi (kardeşler) | Ortak Service + Subject / RxJS  |
| Component ➡️ Global iletişim             | State Management (NGRX, Signal) |



## Özet

- `@Input` -> veri gönder
- `@Output` -> event fırlat
- Tek yönlü veri akışı Angular'ın temel ilkesidir
- Karmaşık iletişimde Service kullanılır