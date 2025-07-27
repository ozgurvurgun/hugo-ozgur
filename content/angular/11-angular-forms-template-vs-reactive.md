---
title: "Angular Forms – Template vs Reactive"
date: 2025-07-25
draft: false
weight: 11
---

## Giriş: Form Nedir? Sadece Input mu?

Form = Kullanıcıdan veri almak. Ama sadece `<input />` koymakla iş bitmez. İhtiyacın olan şey:

- Validasyon (giriş kontrolü)
- Veri toplama
- Dinamik alanlar (birden fazla e-posta vs.)
- Submit işlemi
- Temizleme, resetleme
- Form state takibi (dirty, touched, valid...)

> İşte Angular burada iki yol sunar:
> Template-Driven ve Reactive Forms

## Angular’ın Bakışı: “Form bir bileşen değil, bir state’tir.”

Bir input'a sadece veri bağlamazsın.
- Değeri ne?
- Değişti mi?
- Hata var mı?
- Zorunlu mu?

İşte forumu baba yapan bu takip yapısıdır.

## Template-Driven Forms (HTML Odaklı Yaklaşım)

> HTML içinde yazarsın, Angular arkada otomatik bir Form nesnesi oluşturur.

> Sen direkt masaya siparişi yazarsın, garson kendi sistemine kaydeder.

### 1. Module İmport:
```ts
import { FormsModule } from '@angular/forms';
```

### 2. HTML(component.html):

```HTML
<form #formRef="ngForm" (ngSubmit)="submit(formRef)">
  <input name="name" ngModel required />
  <button type="submit">Gönder</button>
</form>
```

### 3. Component (component.ts):

```ts
submit(form: NgForm) {
  console.log(form.value);        // { name: 'Özgür' }
  console.log(form.valid);        // true / false
}
```

## Reactive Forms (TypeScript Odaklı Yaklaşım)
> Form nesnesini kodla tanımlarsın, HTML ona bağlanır.
> Sipariş sistemini sen kurarsın. Her şeyi sen kontrol edersin.


### 1. Module İmport:

```ts
import { ReactiveFormsModule, FormGroup, FormControl } from '@angular/forms';
```


### 2. Component:

```ts
form = new FormGroup({
  name: new FormControl('', [Validators.required])
});

submit() {
  console.log(this.form.value);
}
```

### 3. HTML:

```html
<form [formGroup]="form" (ngSubmit)="submit()">
  <input formControlName="name" />
  <button type="submit">Gönder</button>
</form>
```

## Temel Farklar

```txt
| Özellik             | Template-Driven        | Reactive Forms           |
| ------------------- | ---------------------- | ------------------------ |
| Tanım Yeri          | HTML içinde            | TypeScript içinde        |
| Kolaylık            | Başlangıç için kolay   | Karmaşık formda güçlü    |
| Validasyon          | Direkt HTML üzerinden  | Kod üzerinden            |
| Test Edilebilirlik  | Zor                    | Kolay                    |
| Dinamik Alan        | Zor                    | Kolay                    |
| Performans          | Görece yavaş           | Daha hızlı               |
| Form State Kontrolü | Angular otomatik yapar | Sen manuel takip edersin |

```


## Validasyonlar

Template:

```html
<input name="email" ngModel required email />
<div *ngIf="formRef.submitted && emailRef.invalid">
  Geçerli e-posta giriniz
</div>
```

Reactive:

```ts
new FormControl('', [Validators.required, Validators.email])
```

```html
<input formControlName="email" />
<div *ngIf="form.get('email')?.errors?.['email']">
  Geçerli e-posta giriniz
</div>
```


## Dinamik Form Alanları (Reactive ile)

```ts
form = new FormGroup({
  emails: new FormArray([
    new FormControl('')
  ])
});

addEmail() {
  (this.form.get('emails') as FormArray).push(new FormControl(''));
}
```

## Ne Zaman Hangisini Kullanmalı?

Angular iki form yaklaşımı sunar:
Template-Driven ve Reactive Forms.
Ama bu "biri iyi, biri kötü" meselesi değildir.
Mesele ihtiyacın ne kadar karmaşık olduğudur.

## Template-Driven - Hızlı, Basit, HTML Odaklı
HTML formunu yaz, Angular arkada form objesini kendisi oluştursun.

Ne zaman tercih edilmeli?
- Form çok basit: 2-3 input, belki bir select
- Sadece required, minlength, email gibi basit validasyonlar yeterli
- Form alanları statik (kullanıcıya göre alan değişmiyor)
- Ekstra state takibi (touched, dirty, valid) önemsiz
- Test yazmayacaksan
- Formdan gelen değerleri direkt modele atayacaksan (ör: `[(ngModel)]="user.name"`)

Örnek kullanım senaryoları:

- Basit bir "İletişim Formu"
- E-bülten abonelik formu
- Login formu (çok fazla validasyon istenmiyorsa)


Avantajları:

- Daha az TypeScript kodu
- Başlangıç seviyesi geliştirici için sezgisel
- Hızlı prototipleme


Dezavantajları:

- Karmaşık validasyonlar zorlaşır
- Dinamik alanlar oluşturmak zahmetli
- Test etmesi güç
- Form state'e erişim daha kısıtlı (NgForm, NgModel üzerinden dolanman gerekir)


## Reactive Forms – Programatik, Güçlü, Kontrol Senin Elinde

Formu sen tanımlarsın, HTML sadece bağlanır.

Ne zaman tercih edilmeli?
- Form dinamik olacaksa (örneğin kullanıcı ekledikçe yeni alanlar açılacaksa)
- Validasyon karmaşık: koşullu zorunluluklar, async validatörler
- Formun state’ini ince ayarla takip etmek istiyorsan (`form.get('email')?.errors`)
- Validasyonları ortak servislerde ya da dış kaynaklardan almak istiyorsan
- Form dataları başka servislerle manipüle edilecek, merge edilecekse
- Unit test yazman gerekiyorsa
- Geliştirici sayısı fazla, proje büyüyorsa


Örnek kullanım senaryoları:

- Kayıt formu (adres, telefon, e-posta… çok alanlı ve kontrol edilmesi gereken yapı)
- Admin panellerindeki içerik yönetimi formları
- Çok adımlı wizard formlar
- Form alanlarının kullanıcı rollerine göre değiştiği durumlar
- Formdan gelen verinin backend ile sürekli etkileşimde olduğu sistemler


Avantajları:
- Tüm form kodla tanımlandığı için daha okunabilir
- Validasyon, kontrol, erişim daha detaylı
- Test etmesi kolay
- Form builder'lar sayesinde modüler hale getirilebilir
- Form değişimi komponent lifecycle'ına değil veri akışına bağlanabilir

Dezavantajları:
- Kod miktarı daha fazla
- İlk başta karmaşık gelebilir


## Karar Verirken Şu 3 Soruya Cevap Ver:
- Formun büyüme potansiyeli var mı?
    - Varsa reactive al.
- Validasyon sade mi, yoksa koşullu & dinamik mi?
    - Dinamikse reactive şart.
- Formdan gelen veriler sadece gösterilecek mi, yoksa işlenecek mi?
    - Sadece görüntülenecekse template ile geç.
    - API'ye gönderilecek, başka servise dağıtılacaksa reactive tercih et.


## Geçiş Mümkün mü?
Elbette. Angular’da bir component’te Template-Driven kullanırken, sonra Reactive'e geçmek mümkün, neden mümkün olmasın ki lol.

Ama bu geçiş:
- Form modelini sıfırdan yazmayı gerektirir
- HTML binding’leri değiştirmeyi gerektirir (`[(ngModel)]` -> `[formControlName]`)

Bu yüzden başta doğru karar vermek uzun vadede hayat kurtarır.


