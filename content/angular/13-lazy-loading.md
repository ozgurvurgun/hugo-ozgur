---
title: "Lazy Loading"
date: 2025-07-23
draft: false
weight: 13
---

## Lazy Loading Nedir?

Lazy Loading, Türkçesiyle tembel yükleme, uygulamamızdaki bazı modülleri ilk başta yüklememek, sadece ihtiyaç duyulduğunda (örn. bir route ziyaret edildiğinde) yüklemek demektir.

Ana fikir:
> “Her şeyi başta yükleme, kullanıcı neye tıklarsa onu getir.”


## Neden Lazy Loading?

Angular projeleri büyüdükçe, bundle (paket) boyutu artar. Bu neye yol açar?

- İlk yükleme süresi uzar 
- Kullanıcı daha uygulamayı görmeden bile MB’larca veri iner
- Özellikle mobil kullanıcılar için felaket olabilir

Lazy Loading buna çare olur:
> Uygulamayı parçalara ayır, parça parça yükle.


Bir Web Sitesi Gibi Düşün

Diyelim ki bir restoranın websitesi var:
- Anasayfa
- Menü sayfası
- Rezervasyon sayfası
- Hakkımızda sayfası

Kullanıcının ilk ziyaret ettiği sayfa Anasayfa.
Normalde tüm sayfaların JavaScript kodları ilk anda yüklenir.
Ama kullanıcı belki sadece anasayfada dolaşacak?

İşte burada Lazy Loading devreye girer:

- Anasayfa yüklensin
- Diğer sayfalar sadece tıklandığında yüklensin


## Teknik Açıdan Ne Olur?
Angular'da modülleri ikiye ayırırsın:

```txt
| Tür              | Ne zaman yüklenir?            |
| ---------------- | ----------------------------- |
| AppModule (root) | Uygulama başlarken            |
| LazyModule       | İlgili route ziyaret edilince |
```

Bir `FeatureModule`’ü `AppRoutingModule` içinde şöyle tanımlarsın:

```ts
const routes: Routes = [
  {
    path: 'admin',
    loadChildren: () =>
      import('./admin/admin.module').then(m => m.AdminModule),
  }
];
```

Yani:

> “Biri `/admin` sayfasına giderse, admin modülünü o zaman yükle.”


## Route Tabanlı Lazy Loading

Lazy Loading en çok routing bazlı ayrımlarda kullanılır:
- `admin/` -> Admin paneli modülü
- `user/` -> Kullanıcı profili modülü
- `checkout/` -> Sepet / ödeme modülü

Kullanıcı eğer admin sayfasını hiç ziyaret etmezse, bu modül hiç yüklenmez.


## Ne Tür Projelerde Kullanılır?

Lazy Loading küçük projelerde şart değildir.
Ama şu durumlarda gereklidir:

- Uygulama çok büyükse (10+ sayfa / feature)
- Her sayfa farklı kullanıcı tiplerine hitap ediyorsa (örn. kullanıcı vs admin)
- Mobil performansı önemliyse
- İlk yükleme süresi kritikse (örn. pazarlama siteleri)


## Tiyatro Perdesi Analojisi

Her sahneyi en başta açıp herkese göstermeye çalışırsan kaos olur.
Ama tiyatroda:

- Perde perde ilerlersin
- Her sahne geldiğinde arkada hazırlanır, önceden değil

Lazy Loading de budur.
Tüm gösteriyi baştan oynatmazsın, gerektiğinde getirirsin.



## Avantajlar

- İlk yükleme süresini azaltır
- Modülerliği artırır (her modül bağımsız)
- Performansı artırır
- Route bazlı kontrol imkânı verir
- Kullanılmayan modüller boşuna yüklenmez

## Dezavantajlar / Dikkat Edilecekler

- Fazla parçalamak karmaşıklık yaratır
- Her modülün kendine ait route'u olmalı
- Ortak servisleri dikkatli yönetmen gerekir (singleton / shared vs)
- Route'ların yanlış tanımlanması modülü yüklemeyi engeller


##  Ne Zaman Kullanmalı?

```txt
| Proje Durumu               | Lazy Loading Kullanmalı mı? |
| -------------------------- | --------------------------- |
| 2-3 sayfalık küçük proje   | Gerek yok                   |
| Çok sayfalı SPA            | Kesinlikle                  |
| Admin + User panel ayrıysa | Tavsiye edilir              |
| Mobil uyumlu, hızlı site   | Gerekli                     |
```

## Bonus: Preloading Strategy

Lazy loading yavaşlık yaratmasın diye Angular ön yükleme stratejisi de sunar: 

```ts
@NgModule({
  imports: [
    RouterModule.forRoot(routes, {
      preloadingStrategy: PreloadAllModules
    })
  ]
})
```

Bu ne yapar?

> Kullanıcı sayfada gezmeye devam ederken, arka planda modülleri yüklemeye başlar.

Yani:L azy ama akıllı :d