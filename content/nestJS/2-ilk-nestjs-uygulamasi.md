---
title: "İlk NestJS Uygulaması"
date: 2025-07-29
draft: false
weight: 2
---

## NestJS Projesine İlk Adım

Artık teoriyi kenara bırakıp işe girişme zamanı. Bu yazıda, sıfırdan bir NestJS projesi oluşturup çalıştıracağız. “Hello World” endpoint’imizi yazacak, klasörleri tanıyacak, ve uygulamanın yaşam döngüsünü anlayacağız.


> Amaç: NestJS'e dair temel taşları yerine oturtmak. Ezberlemeden, sindirerek ilerlemek.


## 1. NestJS CLI ile Proje Oluşturma

NestJS, Angular'dan esinlenmiş bir yapı sunduğu için, benzer şekilde bir CLI (Command Line Interface) aracı sunar.

Kurulum:


```bash
npm i -g @nestjs/cli
```

Yeni proje oluştur:
```bash
nest new ilk-projem
```
CLI sana birkaç şey sorar:

- Hangi paket yöneticisi? (npm diyebilirsin)
- Kurulum bitsin mi? (Evet)


Sonuç:
- `src/` klasörü olan, içinde `main.ts`, `app.module.ts`, `app.controller.ts` ve `app.service.ts` gibi dosyalar bulunan hazır bir yapı seni karşılar.


## 2. Proje Klasör Yapısı ve Dosyalar

NestJS projesi modülerdir. Başlangıçta şu dosyalarla karşılaşırsın:
```txt
src/
├── app.controller.ts
├── app.service.ts
├── app.module.ts
└── main.ts
```

- `main.ts`: Entry point. Nest uygulamasını burada başlatırız. Yani aslında `Express` veya `Fastify` burada ayağa kalkar.

- `app.module.ts`: Uygulamanın ana modülü. Tüm alt modüller, servisler buradan yönetilir.

- `app.controller.ts`: HTTP isteklerini karşılayan kısım. `/` adresine gelen GET isteği örneği burada.

- `app.service.ts`: Controller'ın çağırdığı asıl işi yapan servis.


## 3. NestJS Uygulama Yaşam Döngüsü

> “NestJS çalışınca ne oluyor?” sorusunun cevabını görelim.

1) `main.ts` içindeki `NestFactory.create(AppModule)` çalışır.
2) `AppModule` içindeki servisler, controller'lar ve provider'lar yüklenir.
3) `bootstrap()` fonksiyonu `listen()` çağırarak sunucuyu başlatır.
4) Artık belirli endpoint’lere gelen HTTP istekleri ilgili controller’lara düşer.


Burada `@Module`, `@Controller`, `@Injectable` gibi decorator'lar Nest'in sihrini sağlar. Bu yapılar sayesinde Dependency Injection ve modülerlik otomatik işler.


## 4. Basit Bir "Hello World" Endpoint’i

Controller’a bir göz atalım:

```ts
@Controller()
export class AppController {
  @Get()
  getHello(): string {
    return 'Merhaba NestJS';
  }
}
```

Açıklaması:

- `@Controller()` -> bu sınıf bir HTTP controller.
- `@Get()` -> bu metod GET isteğini karşılıyor. (Yani tarayıcıdan / isteği gelince çalışır)
- `getHello()` -> cevap olarak string döndürüyor.


Tarayıcıda localhost:3000 adresine gidince:
```txt
Merhaba NestJS
```

## 5. Projeyi Çalıştırma ve Derleme

NestJS projeleri TypeScript ile yazılır ama Node, doğrudan TypeScript çalıştıramaz. O yüzden önce derleme (transpile) gerekir.

Ama CLI her şeyi hallediyor:
```bash
npm run start
```
Alternatif:

```bash
npm run build   # Sadece derler
node dist/main  # Derlenmiş halini çalıştırır
```
Çalışma sonunda `dist/` klasörü oluşur. Tüm `.ts` dosyaları `.js`'ye çevrilmiş olur.


## 6. Hot Reload ile Geliştirme

Her `.ts` dosyası değiştiğinde sunucuyu durdurup yeniden çalıştırmak can sıkıcı. Bu yüzden Hot Reload modu var:

```bash
npm run start:dev
```

> `ts-node-dev` modülü sayesinde otomatik yeniden başlatma yapılır. Kod değişti mi? Sunucu anında yeniden ayağa kalkar.

## 7. Debug Modunda Çalıştırma

Hata ayıklamak için `console.log()` güzeldir ama bazen yeterli olmaz.

NestJS'i VS Code gibi bir editörde debug modda çalıştırmak mümkündür. `package.json` altına eklemeye gerek kalmadan şu şekilde başlatabilirsin:

```bash
npm run start:debug
```

Bu komut şunu yapar:

- Port 9229 üzerinde debugger başlatır.
- VS Code -> Run & Debug -> “Attach” ile bağlanabilirsin.
- Breakpoint koy -> uygulama orada durur.