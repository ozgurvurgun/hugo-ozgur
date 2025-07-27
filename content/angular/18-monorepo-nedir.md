---
title: "Monorepo Nedir?"
date: 2025-07-27
draft: false
tags: #tr
weight: 18
---


## Ne Bu Monorepo?

Projeyi yazıyorsun, her şey yolunda. Sonra ekip büyüyor. Frontend bir yerde, backend başka yerde, ortak UI bileşenleri başka bir repo’da...

Bir noktada kendini şöyle derken buluyorsun:
> Abi şu ui-kit’i güncelledim ama 3 farklı repo’da aynı şeyi elle publish’lemek zorundayım

İşte burada Monorepo devreye giriyor.
> Monorepo = Tüm projelerini, kütüphanelerini, app’lerini tek bir Git deposunda toplamak.


Tek repo. Tek hayat. Çok huzur (doğru kullanılırsa).

## Polyrepo vs Monorepo

```txt
| Özellik             | Polyrepo                    | Monorepo                       |
| ------------------- | --------------------------- | ------------------------------ |
| Kod yapısı          | Her proje ayrı repo         | Her şey tek repo içinde        |
| Versiyonlama        | Her repo kendi içinde       | Ortak versiyon yönetimi        |
| Ortak kod paylaşımı | Zor, publish gerekebilir    | import ile direkt erişim       |
| CI/CD               | Her repo için ayrı pipeline | Ortak config ile yönetilebilir |
| Bağımlılık takibi   | Dağınık                     | Merkezi                        |
```


## Ne Zaman Lazım Olur?

- Birden fazla Angular uygulaman var (örn: admin panel, müşteri ekranı)
- Ortak bileşenler (design system, auth servis, form handler) yazıyorsun
- Ekip büyüdü, ama projeleri parçalamak yerine entegre çalışmak istiyorsun

Özetle: birbirine bağlı projeleri ayrı ayrı yönetmek eziyet haline gelmişse, Monorepo sana nefes aldırır.

## Angular’da Monorepo: nx, Angular CLI ile Library’ler

Angular zaten tek bir proje altında çoklu library ve app destekler. Ama biraz daha fazlası lazımsa, `Nx` gibi tool’lar devreye girer.

```bash
npx create-nx-workspace@latest
```
Yada Angular CLI ile:

```bash
ng generate library auth
ng generate application admin
```

Hepsi tek repo'da, `libs/`, `apps/` klasörlerinde yaşar.

## Monorepo'nun Güzel Yanları

- Ortak kodu paylaşmak çok kolay: libs/ui-button
- Refactor yaparken her yere etkisini anında görürsün
- Tek commit ile tüm projeleri senkron tutarsın
- CI/CD daha kontrollü: sadece değişen app için build/test yapabilirsin


## Peki Dezavantajı Yok mu?
Var, tabii ki var. Monorepo cennet değildir:

- Tek repo büyüdükçe clone, build, lint süreleri artar
- Merge çatışmaları daha sık olur
- Doğru yapılandırılmazsa tam bir dependency hell yaşanır

Ama bu sorunların çoğu `Nx`, `Lerna`, `Turborepo` gibi tool’larla aşılabilir.

## Kendi Projende Ne Yapmalısın?

- Küçüksen, polyrepo daha kolaydır.
- Ekip artıyorsa, domain sayısı büyüyorsa, monorepo'ya geçmek mantıklı.
- Angular kullanıyorsan, zaten monorepo uyumlu yapı seni bekliyor.


## Sonuç: Kod Dağınıklığına Son
Monorepo şu sorulara "artık dert değil" cevabı verir:

- “Abi bu UI kit’i 3 app’e nasıl entegre edeceğiz?”
- “Bu auth servisini 4 yerde güncellemem mi lazım?”
- “CI/CD config’i niye her yerde ayrı?”

Artık tek repo, tek yapı, tek kontrol var. Karmaşadan kurtulmak için bazen tek dosya değil, tek repo gerekir.