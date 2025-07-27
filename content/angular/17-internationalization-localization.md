---
title: "Internationalization ve Localization - Angular Çok Dillilik Meselesi"
date: 2025-07-27
draft: false
tags: #tr
weight: 17
---

Uygulamanı yazdın, harika çalışıyor. Ama bir gün Product Manager gelip şöyle diyor:

> Yani uygulama çok güzel ama... bunu Almanca da yapabilir miyiz?

Yani "Kullanıcıya Giriş Yap değil de Anmelden yazsın.

Senin iç sesin:

> Hay aq... O kadar JSON'u kim yazacak, her yere
this.text = translations[this.currentLang]["blabla"];
diye mi koyacağım

Ama korkma dostum, Angular bu konuda çözümler sunmuş.

## Önce Temel Kavramları Oturtalım

```txt
| Terim                           | Anlamı                                                               |
| ------------------------------- | -------------------------------------------------------------------- |
| Internationalization (i18n)     | Uygulamanı çok dilli hale getirme hazırlığıdır. Altyapıyı kurma.     |
| Localization (l10n)             | Hazırladığın altyapıyı alır, bir dile uyarlar. Gerçek çeviri işlemi. |
```

- Yani i18n = “çok dilliliğe uygun kod yazmak”
- l10n = “o kodu Almanca, Fransızca, Kürtçe, Klingonca çevirmek”


## Angular’da Internationalization Nasıl Yapılır?

Angular’da i18n deyince iki yöntem öne çıkar:
- Built-in i18n (AOT tabanlı - derleme sırasında)
- Runtime i18n (ngx-translate gibi paketlerle çalışma anında)


Biz burada Angular’ın kendi mekanizmasıyla başlayalım. Çünkü temel olan o.


## Built-in Angular i18n Sistemi

Angular derleme sırasında, farklı dil versiyonları oluşturmanı sağlar. Yani:

```bash
ng build --localize
```
...derken aslında `dist/en`, `dist/de` gibi farklı dizinlere farklı dil çıktıları alınır.

## Etiketleme - i18n Direktifi

HTML üzerinde çeviri yapılacak yerleri Angular’a bildirirsin:

```html
<h1 i18n>Giriş Yap</h1>
```
Yani:
> Bu metni çevirilebilir hale getir.

Bu etiketi gören Angular, `.xlf` (veya `.xlf2`) formatında bir çeviri dosyası çıkarır.



## Çeviri Dosyası (.xlf)
Derleme sonrası şu komutla çeviri dosyası çıkarılır:

```bash
ng extract-i18n
```
Sonuç:
```xml
<trans-unit id="login_header" datatype="html">
  <source>Giriş Yap</source>
  <target>Anmelden</target> <!-- Alman versiyonu -->
</trans-unit>
```

Her `i18n` işaretli alan için bir `trans-unit` çıkar.


## Dil Seçenekleri Nasıl Tanımlanır?

angular.json içinde şöyle bir yapı oluşturursun:

```json
"projects": {
  "my-app": {
    ...
    "i18n": {
      "sourceLocale": "en-US",
      "locales": {
        "de": "src/locale/messages.de.xlf",
        "tr": "src/locale/messages.tr.xlf"
      }
    }
  }
}
```

Ve build sırasında:

```bash
ng build --localize
```

Bu sana `dist/de`, `dist/tr` gibi çıktı verir.


## Runtime'da Dil Seçmek?

Built-in i18n sistemi statik çalışır.
Yani kullanıcı uygulamayı açtığında hangi dilde derlendiyse onu görür.

Kullanıcı anlık olarak dil değiştiremez.
> Bu sistem çok dilli ama tek seferlik.

Anlık dil değiştirmek istiyorsan runtime çözümler kullanılır.


## Runtime i18n: ngx-translate

Eğer kullanıcı “şimdi Türkçe, sonra Fransızca” diye dil değiştirsin istiyorsan `@ngx-translate/core` gibi kütüphaneler devreye girer.

Kullanımı kolaydır:

```ts
translate.use('de');
translate.get('login.title').subscribe((res: string) => {
  this.title = res;
});
```

Ve çeviri dosyaları basittir:

```json
{
  "login": {
    "title": "Giriş Yap",
    "button": "Devam Et"
  }
}
```

Avantajı:
- Kullanıcı anlık dil değiştirir.
- Lazy loading ile dil dosyaları yüklenebilir.
- Dynamic content’ler de çevirilebilir.


## Peki Hangisi?

```txt
| Durum                    | Angular i18n (Built-in) | ngx-translate   |
| ------------------------ | ----------------------- | --------------- |
| SEO Gerekiyor            | y                       | n (client-side) |
| Anlık dil değiştirme     | n                       | y               |
| Performans (static HTML) | y                       | n               |
| Uygulama küçükse         | y                       | y               |
| Çok dinamik içerik varsa | n                       | y               |
```

## Dikkat Edilmesi Gerekenler

Tarih/sayı biçimleri dil ve ülkeye göre değişir. Bunun için Angular `LOCALE_ID` kullanır.

```ts
providers: [{ provide: LOCALE_ID, useValue: 'de-DE' }]
```

Pipe’lar da dile duyarlıdır:

```html
<p>{{ today | date }}</p>
```
- `de-DE` seçilirse: `27.07.2025`
- `en-US` seçilirse: `Jul 27, 2025`


## String’i Sadece Değiştirmek Değil

- Localization sadece "string çevirisi" değildir:
- Tarih formatı
- Para birimi
- Yazı yönü (RTL / LTR)
- Sayı formatı
- Kültürel farklılıklar

Yani:
> "Yılın tarihi" = sadece sayı değil, kültürel bağlam meselesidir.


## Son

Çok dilli uygulama yazmak, "her şeyi stringe çevirmek" değil. Bu uygulamanı başka bir kültüre adapte etmek, empatiyle kullanıcı deneyimi kurmaktır. Angular sana bunun için hem build-time hem runtime çözümler sunar.

Ama asıl mesele şu:
> Kodun uluslararası olmalı ama kullanıcının mahallesine hitap etmeli ;)