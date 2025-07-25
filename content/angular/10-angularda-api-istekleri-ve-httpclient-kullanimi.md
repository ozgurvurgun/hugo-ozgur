---
title: "Angular’da API İstekleri ve HttpClient Kullanımı"
date: 2025-07-25
draft: false
weight: 10
---


## Angular'da API ile Konuşmak: HttpClient

Bir frontend uygulaması coğu zaman tek başına bir frontend değildir. Backend ile konuşur, veri çeker, gönderir, günceller.

Angular'da bu işi yapmak için `HttpClient` servisi kullanılır.

> Angular'da HTTP demek, `HttpCLient` demektir.

## HttpClient Nedir?

Angular bize `@angular/common/http` paketiyle gelen `HttpClient` sınıfını sunar.

Bununla şunları yapabilirsin:

- GET, POST, PUT, DELETE, gibi HTTP istekleri atmak
- Header, query param, body gibi seçenekler eklemek
- RxJS sayesinde asenkron istekleri yönetmek
- `Interceptor`' larla istek öncesi/sonrası işlemler eklemek

Ama önce `HttpClientModule`' ü app module'e dahil etmelisin:

```ts
import { HttpClientModule } from '@angular/common/http';

@NgModule({
  imports: [HttpClientModule]
})
export class AppModule {}
```


## Temel Kullanım

```ts
constructor(private http: HttpClient) {}

ngOnInit() {
  this.http.get('https://api.example.com/users')
    .subscribe(data => console.log(data));
}
```

## Options Ekleme (headers, params)

```ts
const headers = new HttpHeaders().set('Authorization', 'Bearer token');
const params = new HttpParams().set('page', '1');

this.http.get('https://api.example.com/users', { headers, params });
```

Ya da kısaca:

```ts
this.http.get('https://api.example.com/users', {
  headers: { 'Authorization': 'Bearer token' },
  params: { page: '1' }
});
```

## Opsiyonları Değişken Olarak Tanımlamak

```ts
const options = {
  headers: { 'Custom-Header': 'abc' },
  params: { filter: 'active' }
};

this.http.get('/api/items', options);
```
Bu, özellikle çok sayıda istekte options tekrarını önler.

## Daha Kısa Yazım

HttpClient metotları Promise değil, Observable döner.

Bu nedenle:

```ts
this.http.get('/api/users')
  .subscribe(console.log);
```

şeklinde sadeleştirme yapılabilir. Ama dikka, subscribe olmadan istek atılmaz.


## Generic HTTP Service Yazmak

Her component'e tekrar tekrar http.get yazmak yerine, ortak bir servis kullanmak iyidir:

```ts
@Injectable({ providedIn: 'root' })
export class ApiService {
  constructor(private http: HttpClient) {}

  get<T>(url: string, options = {}) {
    return this.http.get<T>(url, options);
  }

  post<T>(url: string, data: any) {
    return this.http.post<T>(url, data);
  }

  // vs.
}
```

Artık tüm component'lerde:

```ts
this.api.get<User[]>('/api/users').subscribe(users => this.users = users);
```

## Interceptor Nedir?

Interceptor, her HTTP isteğine ortak işlem eklememizi sağlar. Örnek: Header'a token eklemek, hata loglamak, loader göstermek...

## Örnek: Auth Interceptor

```ts
@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    const token = localStorage.getItem('token');

    if (token) {
      const cloned = req.clone({
        setHeaders: { Authorization: `Bearer ${token}` }
      });
      return next.handle(cloned);
    }

    return next.handle(req);
  }
}
```

AppModule:

```ts
providers: [
  { provide: HTTP_INTERCEPTORS, useClass: AuthInterceptor, multi: true }
]
```

## Hataları Interceptor’da Yakalamak (RxJS catchError)

```ts
return next.handle(req).pipe(
  catchError((error: HttpErrorResponse) => {
    if (error.status === 401) {
      // Oturum süresi dolduysa logout
    }
    return throwError(() => error);
  })
);
```

## Async/Await Kullanmak

Angular HttpClient Observable döndürür ama `firstValueFrom()` ile Promise'e çevirebilirsin:

```ts
import { firstValueFrom } from 'rxjs';

async ngOnInit() {
  const users = await firstValueFrom(this.http.get<User[]>('/api/users'));
}
```

## Route Öncesi API Çağrısı (Resolve)
Bir route açılmadan önce veri çekmek istiyorsan `Resolver` kullan:

```ts
@Injectable({ providedIn: 'root' })
export class UserResolver implements Resolve<User> {
  constructor(private http: HttpClient) {}
  
  resolve(route: ActivatedRouteSnapshot): Observable<User> {
    const id = route.paramMap.get('id');
    return this.http.get<User>(`/api/users/${id}`);
  }
}
```

Routing:

```ts
{
  path: 'profile/:id',
  component: ProfileComponent,
  resolve: { user: UserResolver }
}
```

## Özet
- HttpCLient ile Angular'da API ile konuşuruz
- headers, params, options kolayca eklenebilir
- Interceptor ile global işlemler yapılabilir
- Resolve ile veri önceden yüklenebilir
- Observable kullanımı sayesinde reactive yapı korunur