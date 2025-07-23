---
title: "Guard"
date: 2025-07-23
draft: false
weight: 7
---


## Guard Nedir? Neden Var?

Şöyle düşün:

Bir apartman giriş kapısı var ama herkese açık değil. Kapıda bir güvenlik görevlisi var ve sana sorular soruyor:

- Kimsin?
- Giriş iznin var mı?
- Bu kata çıkabilir misin?
- Bu daireye özel yetkin var mı?

İşte bu **Angular Gaurd** tam olarak bu işi yapar. Uygulamanın rotaları arasında geçiş yapılırken kim geçebilir, kim geçemez sorularına cevap verir.

## Guard Ne İş Yapar?

Angular'daki Router sistemi sayesinde sayfalar arasında geziniriz (`/login`, `/dashboard`, `/admin` vs). Ama bazı sayfalar:

- Giriş yapmış kullanıcıya özel olabilir
- Yalnızca admin'lere açık olabilir
- Form doldurmadan geçiş yapılmayabilir
- Dışarıdan çıkarken “kaydetmediğin değişiklikler var” diye 
uyarı verebilir

İşte bu geçişlere engel olmak ya da izin vermek için **guard** yazılır.

## Angular’da 5 Tür Guard Vardır

| Guard Türü           | Ne zaman devreye girer?                         |
|----------------------|-------------------------------------------------|
| `CanActivate`        | Route'a gitmeden önce **erişim izni** kontrolü |
| `CanActivateChild`   | Child route’lara geçmeden önce                 |
| `CanDeactivate`      | Route’tan çıkmadan önce, **çıkış izni**        |
| `CanLoad`            | Lazy-loaded modül yüklenmeden önce             |
| `Resolve`            | Route açılmadan önce veri hazırla (guard değil ama aynı yapıda) |


## İlk Guard'ımızı Yazalım: `CanActivate`

Guard Oluştur

```bash
ng g guard auth
```

Bu komut sana şu dosyayı oluşturur:

```ts
@Injectable({
  providedIn: 'root'
})
export class AuthGuard implements CanActivate {
  canActivate(): boolean {
    return true;
  }
}
```

Kullanım (Route'a uygula)

```ts
const routes: Routes = [
  {
    path: 'dashboard',
    component: DashboardComponent,
    canActivate: [AuthGuard]
  }
];
```
> `canActivate` Bu route’a girilmeden önce çalışır.

## Örnek: Giriş Yapmamışsa Reddet

```ts
export class AuthGuard implements CanActivate {
  constructor(private authService: AuthService, private router: Router) {}

  canActivate(): boolean {
    if (this.authService.isLoggedIn()) {
      return true;
    } else {
      this.router.navigate(['/login']);
      return false;
    }
  }
}
```

## Diğer Guard Türleri

CanActivateChild
> Bir parent route'un altında alt sayfalar varsa, sadece child'a geçişi kontrol eder.

```ts
{
  path: 'admin',
  component: AdminComponent,
  canActivateChild: [AdminGuard],
  children: [
    { path: 'users', component: UserListComponent },
    { path: 'logs', component: LogComponent }
  ]
}
```

CanDeactivate
> Kullanıcı sayfadan çıkarken seni uyarabilir.

```ts
canDeactivate(component: FormComponent): boolean {
  if (component.form.dirty) {
    return confirm("Değişiklikler kaydedilmedi. Yine de çıkmak istiyor musun?");
  }
  return true;
}
```

CanLoad
> Lazy-load edilen modüllerin, daha yüklenmeden önce engellenmesini sağlar.

```ts
{
  path: 'admin',
  loadChildren: () => import('./admin/admin.module').then(m => m.AdminModule),
  canLoad: [AdminGuard]
}
```

> Not: `CanActivate` çalışır ama modül olarak çoktan yüklenmiş olabilir. `CanLod` daha erkenden devreye girer.

Resolve
Bu teknik olarak guard değil ama aynı konsepttedir:
> Sayfa açılmadan önce gerekli veriyi hazırla.

```ts
{
  path: 'profile/:id',
  component: ProfileComponent,
  resolve: { user: UserResolver }
}
```
Component içinde:
```ts
this.route.data.subscribe(data => {
  this.user = data.user;
});
```

## Guard Dönüş Tipleri

Guard'lar şunları dönebilir:

- `true` geçişe izin ver
- `false` geçişi engelle
- `UrlTree` yönlendir
- `Observable<boolean | UrlTree>` asenkron izin
- `Promise<boolean | UrlTree>`

## Performans ve Best Practices
- Auth işlemleri her yerde değil, merkezi Guard içinde tut.
- CanLoad, sadece lazy-loaded modüllerde işe yarar.
- CanDeactivate ile formları koru, kullanıcıyı uyarmayı unutma.
- Her Guard'ı tek sorumluluk ilkesiyle yaz (SRP).

## Gerçek Güvenlik Nerede Başlar?

Guard kullanarak bir kullancıyı `/admin` sayfasına girmekten alıkoyabilirsin. Ama unutmaman gereken çok kritik bir gerçek var:
> Frontend, her zaman manipüle edilebilir.

Kullanıcı:
- Chrome DevTools ile localStorage’daki token’ı değiştirebilir.
- Network isteğini taklit edebilir.
- Route'a elle erişmeye çalışabilir.
- Guard kodunu içeren JS bundle'ı analiz edebilir.

Guard, sadece şunu sağlar:
- “Normal kullanıcı” senaryosunda istenmeyen erişimi önler.
- Uygulama içinde "mantıklı bir akış" sağlar.


## Nihai Güvenlik Backend’dedir

Gerçek güvenlik önlemleri her zaman sunucu tarafında (backend) alınmalıdır:
- Bir kullanıcı `/admin` sayfasına gitse bile, örneğin API `/admin/data` isteğine cevap vermemeli.
- Backend, token'ı doğrulamalı, yetkiyi kontrol etmeli.
- Hangi kullanıcının hangi role sahip olduğu orada karar verilmelidir.
- Sunucu, frontend'den gelen her veriyi şüpheli kabul etmeli.

## Özet

- Guard, bir rotaya erişim izni veya çıkış kontrolü yapar.
- 5 tür guard var: CanActivate, CanActivateChild, CanDeactivate, CanLoad, Resolve
- Kullanıcı girişi, yetki, form koruma gibi durumlarda devreye girer.
- Doğru yerde doğru guard kullanımı güvenli, kontrollü, kullanıcı dostu app demektir.

