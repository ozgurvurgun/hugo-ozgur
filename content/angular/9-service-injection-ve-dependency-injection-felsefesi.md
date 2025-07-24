---
title: "Service Injection ve Dependency Injection (DI) felsefesi"
date: 2025-07-23
draft: false
weight: 9
---



## Neden Kodu Parçalamak Yetmez?

Diyelim ki bir bileşen, API'den veri çekiyor:

```ts
export class UserComponent {
  constructor() {
    fetch('https://api.example.com/users')
      .then(res => res.json())
      .then(data => this.users = data);
  }
}
```

Çalışıyor mu? Evet.

- Ama test edilebilir mi? Hayır.
- Başka yerde tekrar kullanılabilir mi? Hayır.
- API değişirse ne olacak? Her yeri mi değiştireceğiz? Evet. Yani: felaket.

İşte bu yüzden yazılım mühendisi emmiler şöyle der:
> Bileşenler ihtiyaçlarını kendi üretmemeli, dışarıdan almalı.


## Dependency Injection Nedir?

Dependency Injection, bir sınıfın ihtiyaç duyduğu şeyleri (servis, veri, yapı) kendi içinde üretmek yerine, dışarıdan alması prensibidir.

Basitçe:
> Bağımlılığı içeri gömme, dışarıdan ver.

## Kahve Makinesi Analojisi

- Makine içine doğrudan çekirdek koyarsan -> Değiştirmesi zor
- Ama filtre portuna takılabilir bir çekirdek modülü koyarsan -> Her zaman değiştirilebilir, test edilebilir



Angular da aynen böyle çalışır:
> Her bileşen sadece ihtiyaç duyduğu hizmeti (service) dışarıdan alsın.


## Angular'da Service Nedir?

Angular’da Service, genellikle:
- API çağrıları yapmak
- Veriyi paylaşmak
- Uygulama genelinde tek bir işlem merkezi sağlamak

gibi işler için kullanılan bir sınıftır.

```ts
@Injectable({
  providedIn: 'root'  // tüm uygulama genelinde kullanılabilir hale getir
})
export class UserService {
  getUsers() {
    return this.http.get('/api/users');
  }
}
```

> `@Injectable` decorator’ı, bu sınıfın “enjekte edilebilir” olduğunu belirtir.


## Service Nasıl Inject Edilir?

```ts
export class UserComponent {
  constructor(private userService: UserService) {}

  ngOnInit() {
    this.userService.getUsers().subscribe(data => {
      this.users = data;
    });
  }
}
```

Angular şöyle yapar:

- UserComponent bir UserService ister.
- Angular kendi container’ına bakar.
- UserService varsa verir. Yoksa oluşturur ve cache’ler (singleton).


## providedIn: 'root' Ne Demek?

Bu, Angular’a şunu der:
> Bu servisi bir kez oluştur, tüm uygulama genelinde paylaş

Eğer daha lokal bir kapsam istiyorsan:

```ts
@Injectable({
  providedIn: SomeModule
})
```

ya da eski usul:

```ts
@NgModule({
  providers: [MyService]
})
```

## Test Edilebilirlik Kazancı

```ts
constructor(private userService: UserService) {}
```
yerine testte şöyle bir mock verebilirsin:
```ts
TestBed.configureTestingModule({
  providers: [
    { provide: UserService, useClass: MockUserService }
  ]
});
```

Yani:
> Gerçek olanı değil, taklidini ver.
Bu da unit test yazmayı mümkün kılar.

## Service Singleton mıdır?

Evet, eğer aynı injector içinde kullanılıyorsa.
Angular default olarak service’leri singleton olarak kullanır.

Ama farklı module’lerde farklı instance’lar istiyorsan farklı `providers` kullanman gerekir.


## Anti-Pattern: Service Her Şeyi Yapsın

Bir Service her şeyi yapıyorsa:
- Hem API çağrısı
- Hem form validasyonu
- Hem state tutma
- Hem component event yönetimi...

Bu kötü bir tasarımdır.

> Her service tek bir işlev için yazılmalı (SRP: Single Responsibility Principle)


## Ne Zaman Service Kullanmalıyım?

- Ortak veri veya mantık birden fazla component’te kullanılıyorsa
- Component'ler arası iletişim gerekiyorsa
- API, WebSocket, timer, localStorage gibi dış bağımlılık varsa
- Test yazılabilirlik isteniyorsa
- UI’dan bağımsız işlem yapılacaksa


## DI = Angular’ın Kalbi

Angular’da her şey DI ile çalışır:
- Component'ler
- Directive'ler
- Guard'lar
- Interceptor'lar
- Pipe'lar bile!

Hepsi ihtiyacı varsa başka bir şeyi inject eder.


## Sonuç: Kodunu Bağımlılıktan Kurtar

Service Injection ve Dependency Injection sayesinde:

- Kod daha temiz
- Test daha kolay
- Değişiklik daha güvenli
- Komponentler daha sade

Ve Angular şöyle der:
> Her şeyi bilen değil, sadece işini bilen component yaz.
