---
title: "Pipe"
date: 2025-07-23
draft: false
weight: 6
---

## Pİie Nedir? Neden Vardır?

Şöyle düşün:
Bir değerin ham hali ile gösterilecek hali genellikle aynı değildir.

- Tarih formatı farklı gösterilsin istersin.
- Sayının sonuna TL, $ gibi birim gelsin istersin.
- Tüm harfleri büyük/küçük yapmak istersin.
- listeyi filtrelemek, slice etmek istersin.

Bunların hepsini component içinde yapmak mümkün. Ama zamanla kod şişer, tekrar eder, okunmaz hale gelir.

Angular burada şöyle der:
> Veri değişmeyecek. Sadece görüntüsü değişecekse, o zaman bunu HTML' de hallet.

Bu yaklaşımın adı: **Pipe**

## Pipe = Boru

Pipe kelime anlamıyla da "boru" demek.
Bir veriyi alır, içinden geçirir, dönüştürülmüş halini dışarıya verir.

```html
<!-- Orijinal -->
<p>{{ price }}</p>

<!-- Pipe ile -->
<p>{{ price | currency:'TRY' }}</p>
```

Burada `price` değişmedi ama çıktısı TL formatında gösterildi.

## Hazır Gelen Pipe'lar
| Pipe        | Açıklama                     | Örnek                                 |
| ----------- | ---------------------------- | ------------------------------------- |
| `date`      | Tarihi formatlar             | \`{{ today   \| date:'shortDate' }}\` |
| `uppercase` | Büyük harf                   | \`{{ name    \| uppercase }}\`        |
| `lowercase` | Küçük harf                   | \`{{ name    \| lowercase }}\`        |
| `currency`  | Para birimi                  | \`{{ price   \| currency:'USD' }}\`   |
| `percent`   | Yüzde                        | \`{{ ratio   \| percent }}\`          |
| `slice`     | Dizi veya string keser       | \`{{ message \| slice:0:10 }}\`       |
| `json`      | JSON stringi olarak gösterir | \`{{ user    \| json }}\`             |


> Tüm Pipe'lar peş peşe zincir gibi de yazılabilir:
```html
<p>{{ text | slice:0:5 | uppercase }}</p>
```

## Kendi Pipe'ını Yazmak
Diyelim ki her string'in sonuna `™` işareti eklemek istiyorsun. Bu Angular'da özel bir Pipe yazarak yapılır.

Oluştur:
```bash
ng g pipe trademark
```

Kod:
```ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'trademark'
})

export class TrademarkPipe implements PipeTransform {
  transform(value: string): string {
    return value + '™';
  }
}
```

Kullanım:

```html
<p>{{ 'Angular' | trademark }}</p>
<!-- Çıktı: Angular™ -->
```

> `PipeTransform` arayüzü sayesinde `transform()` fonksiyonu otomatik tanınır.

## Pipe Ne Zaman Kullanılır?

- Tek işlevi veriyi görsel olarak dönüştürmekse.
- Aynı mnatık birçok yerde tekrar ediyorsa.
- Verinin kendisi değişmeyecekse.

## Pipe Ne Zaman Kullanılmaz?

- Veriyi değiştireceksen (ör: butonla bir state değiştirilecekse)
-  Ağır hesaplamalar içeriyorsa ve sık değişen dataya uygulanıyorsa
(Bunun için pure vs impure kısmına bak)

## Pure vs Impure Pipe

Pure Pipe
- Sadece input değişince çalışır.
- Performanslıdır.

```ts
@Pipe({
  name: 'myPipe',
  pure: true  // default budur
})
```

Impure Pipe
- Sürekli tekrar çalşır (her change detection'da)
- DOM'a dokunan, zamanla değişen işler için kullanılır ama performans maliyeti vardır.


```ts
@Pipe({
  name: 'volatile',
  pure: false
})
```

## Bazı Notlar

- Pipe’lar Angular module’üne `declarations` olarak eklenmelidir.
- Pipe’lar HTML’de çağrılır ama mantığı TypeScript’te tanımlanır.
- Pipe içine parametre gönderilebilir: `{{ name | slice:0:10 }}`

## Sonuç: Tesisat Temiz, Kod Temiz

Pipe'lar sayesinde:
- Kod okunur olur.
- Template'ler sadeleşir.
- Component’ler sadece iş mantığına odaklanır.

Yani pipe, görünüm katmanına uygun bir soyutlamadır.

