---
title: angular.json
date: 2025-07-27
draft: false
tags:  #tr
weight: 15
---

## Bu Dosya Nedir?

`angular.json`, bir Angular projesinin yapılandırma dosyasıdır. Angular CLI, bu dosyaya bakarak:

- Projenin nerede başladığını
- Hangi derleyici ayarlarının kullanılacağını
- Build, test, lint, serve komutlarında ne yapılacağını
- Uygulama yollarını, stilleri, scriptleri


gibi yüzlerce detayı öğrenir ve uygular.

Bu dosya olmadan Angular CLI, projeyi nasıl çalıştıracağını bilemez.


## angular.json Nerede Bulunur?

Her Angular projesinde, projenin kök dizininde bulunur:

```txt
/app
├── src/
├── angular.json
├── package.json
├── tsconfig.json
```

## angular.json’un Genel Yapısı

```json
{
  "$schema": "./node_modules/@angular/cli/lib/config/schema.json",
  "version": 1,
  "projects": {
    "my-app": {
      ...
    }
  },
  "defaultProject": "my-app"
}
```

Bu dosya JSON formatında bir yapılandırma ağacıdır.


## Temel Alanlar

- $schema
    - Hangi şemaya uygun olduğunu belirtir.
    - Kod editörleri (VS Code gibi) otomatik tamamlama ve validasyon sağlar.

- version
    - angular.json dosyasının sürümünü belirtir.
    - CLI’nin hangi formatı beklediğini anlaması içindir.

- projects
    - Bu bölümde birden fazla uygulama ve kütüphane tanımlanabilir.
    - Genellikle projenin ismiyle başlar.
    - Altında `architect` adında dev bir yapılandırma alanı bulunur.


projects -> architect -> build

```json
"architect": {
  "build": {
    "builder": "@angular-devkit/build-angular:browser",
    "options": {
      "outputPath": "dist/my-app",
      "index": "src/index.html",
      "main": "src/main.ts",
      "polyfills": "src/polyfills.ts",
      "tsConfig": "tsconfig.app.json",
      "assets": ["src/favicon.ico", "src/assets"],
      "styles": ["src/styles.css"],
      "scripts": []
    },
    ...
  }
}
```

```txt
| Alan         | Açıklama                                        |
| ------------ | ----------------------------------------------- |
| builder      | Hangi derleyicinin kullanılacağını belirtir     |
| outputPath   | Build sonrası çıktı nereye yazılacak            |
| index        | Ana HTML dosyası                                |
| main         | Giriş noktası (main.ts)                         |
| polyfills    | Tarayıcı uyumluluğu için gerekli ek dosyalar    |
| tsConfig     | TypeScript yapılandırma dosyası                 |
| assets       | Kopyalanacak klasör ve dosyalar (ör. resimler)  |
| styles       | Global stiller                                  |
| scripts      | Ek script dosyaları (jQuery gibi manuel JS’ler) |
```

## Diğer Architect Komutları

```txt
| Komut          | Ne işe yarar                        |
| -------------- | ----------------------------------- |
| serve          | ng serve komutunu tanımlar          |
| test           | ng test (Karma + Jasmine)           |
| lint           | ng lint (ESLint/TSlint)             |
| extract-i18n   | Çeviri dosyalarını çıkarır          |
| e2e            | Uçtan uca test (protractor/cypress) |
```

## configurations -> production

```json
"configurations": {
  "production": {
    "optimization": true,
    "outputHashing": "all",
    "sourceMap": false,
    "namedChunks": false,
    "extractCss": true,
    "budgets": [
      {
        "type": "initial",
        "maximumWarning": "2mb",
        "maximumError": "5mb"
      }
    ]
  }
}
```

Bu ayarlar, `--configuration production` parametresi verildiğinde devreye girer.


```txt
| Alan            | Açıklama                                      |
| --------------- | --------------------------------------------- |
| optimization    | Kodun minimize edilmesini sağlar              |
| outputHashing   | Cache problemi olmaması için dosya hash’lenir |
| sourceMap       | Üretimde kaynak haritası gizlenir             |
| budgets         | Dosya boyutu limitlerini kontrol eder         |
```

## Neler Özelleştirilebilir?
- Kendi build target’larını ekleyebilirsin (my-custom-build)
- Script/style dosyalarını değiştirebilirsin
- Çoklu proje (monorepo) desteği için projects alanına ekleme yapabilirsin
- `test`, `lint` gibi komutlara farklı `tsconfig` dosyaları atayabilirsin

## Ne Değildir?

- Bu dosya kod dosyası değildir, sadece yapılandırma içerir.
- Mantıksal kod yazılmaz.
- Runtime’da etkisi yoktur. Sadece build-time etkisi vardır.


## Neden Önemlidir?

- Projeyi CLI ile yönetmenin anahtarıdır.
- Build’in neyi nasıl yapacağını belirler.
- Takım içinde standart bir yapı sağlar.

## Sonuç
`angular.json`, Angular CLI’nin seni tanıdığı yerdir. Projeyi nasıl başlatacağını, nasıl derleyeceğini, hangi dosyaları dahil edeceğini hep buradan öğrenir.

Angular ile ciddi bir iş yapacaksan, bu dosyayı tanımadan olmaz ;)



