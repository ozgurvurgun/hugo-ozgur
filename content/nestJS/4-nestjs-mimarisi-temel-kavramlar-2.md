---
title: "NestJS Mimarisi: Modüller Arası İletişim (2. Bölüm)"
date: 2025-07-29
draft: false
weight: 4
---


## Modüller Arası İletişim Neden Önemlidir?

Modülleri ayırdık ama gerçek hayatta işler böyle izole ilerlemez.


Örneğin:
- Kullanıcı kaydı yapılınca log'a bir şey yazmak isteyebilirsin.
- Bir ödeme alınınca stok sistemini bilgilendirmek isteyebilirsin.

Yani modüllerin birbiriyle konuşması gerekir.  
NestJS bunu `imports`, `exports` ve dependency injection sistemleriyle sağlar.

## Temel Kurgu: Service Kullanmak

Senaryomuz şu:

- `UserService` adında bir servisimiz var (`UserModule` içinde)
- `AuthModule` bu servisi kullanmak istiyor (örneğin kullanıcıyı veritabanında sorgulamak için)


Şöyle bir şey yapmak istiyoruz:

```ts
// auth.service.ts
constructor(private userService: UserService) {}
```
Ama bu çalışmaz. Çünkü NestJS, bir modülde tanımlı provider'ı başka modüllere otomatik olarak açmaz.

## Ne Yapmam Lazım?

Adım adım gidelim:

1. Kullanmak istediğin servisi export et:
```ts
// user.module.ts
@Module({
  providers: [UserService],
  exports: [UserService],  // dışa açıyoruz
})
export class UserModule {}
```

2. Kullanan modülde, ilgili modülü import et:
```ts
// auth.module.ts
@Module({
  imports: [UserModule],  // içeri alıyoruz
  providers: [AuthService],
})
export class AuthModule {}
```

3. Artık kullanabilirsin:
```ts
// auth.service.ts
@Injectable()
export class AuthService {
  constructor(private userService: UserService) {}
}
```

Bitti.


## Analoji

Bunu bir ofis binası gibi düşün:
- Her modül bir departman
- `UserService` -> İnsan Kaynakları'nda çalışan biri
- `AuthModule` -> Güvenlik departmanı

Eğer HR'dan bir personeli kullanmak istiyorsan:

1. HR onu dış dünyaya “tanıtmalı” (export)
2. Güvenlik birimi HR ile “protokol imzalamalı” (import)
3. Ancak o zaman o kişiyi çağırıp iş yaptırabilirsin


## Circular Dependency Sorunu (Kabus)

Şimdi işler karışıyor.
Diyelim ki hem `AuthService`, `UserService`’i kullanıyor… Hem de `UserService`, `AuthService`’i çağırmak istiyor.

Yani:
```txt
UserService → AuthService  
AuthService → UserService
```

Bu bir circular dependency’dir. NestJS bunu görünce error verir:
> Cannot resolve dependencies of the UserService

## Çözüm: forwardRef
Bu durumda forwardRef kullanmamız gerekir.
Bu, Nest’e “şimdilik tanımadığın ama sonra tanıyacağın bir şeyi kullan” demektir.


1. Import ederken:
```ts
// user.module.ts
@Module({
  imports: [forwardRef(() => AuthModule)],
  providers: [UserService],
  exports: [UserService],
})
export class UserModule {}
```
```ts
// auth.module.ts
@Module({
  imports: [forwardRef(() => UserModule)],
  providers: [AuthService],
})
export class AuthModule {}
```

2. Inject ederken de `@Inject(forwardRef(...))`:
```ts
@Injectable()
export class UserService {
  constructor(
    @Inject(forwardRef(() => AuthService))
    private authService: AuthService,
  ) {}
}
```

> Bu yapı ilk bakışta karışık gibi ama büyük projelerde olmazsa olmaz bir tekniktir.


## İpucu: exports sadece provider’lar içindir

`exports: [X]` dediğinde sadece servisleri (provider) paylaşabilirsin. Controller’lar dışa açılmaz, onlar sadece istek karşılar. Ayrıca `controllers: []` ve `providers: []` bir yere export edilmezse sadece o modülde erişilebilir olur.


## Eksik/Kritik Nokta: Karmaşık Yapılarda İz Sürmek Zorlaşabilir

Evet, bu sistem sağlam. Ama şunu da kabul edelim:

- Bir modül birden fazla yere import/export edilince neyin nereden geldiğini takip etmek zorlaşabiliyor.
- Circular dependency durumları projeyi kırabilir.
- forwardRef, çözüm getiriyor ama yapıyı daha karmaşıklaştırıyor.
- Export ettiğin bir servisi iki farklı modülde override etmeye çalışırsan kontrol sende olmayabilir.

> Yani bu güçlü yapı, “iyi bir mimari disiplin” ile desteklenmezse başına bela olabilir. 