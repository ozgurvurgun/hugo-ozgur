---
title: Functor nedir haskell
description:
date: 2019-10-28 
draft: false
tags:  
---


Son bir kaç aydır Haskell öğrenmeye çalışıyorum. Neden böyle bir maceraya giriştiğimle ilgili bir yazı yazmak istiyorum ilerde. 

Şimdilik öğrendiğimi düşündüğüm konuları hem not etmek, hem de türkçe kaynak oluşturmuş olabilmek adına, olabildiğince bloguma eklemeye çalışacağım.
<!--more-->
Functor, Haskell'de bir TypeClass olarak tanımlanmıştır. Yaptığı iş, kapsayıcıya/kapsama (context) müdahale etmeden, içerdeki veride işlem yapabilmeye olanak sağlamaktır.

Basitçe toplama işlemi için `(+3) 2` dediğimizde, `2` ile `3 ile toplayan fonksiyon`u işleme sokarak `5` sonucuna ulaşmış oluyoruz. `+` iki argüman bekleyen bir fonksiyon olduğu için birinci fonksiyonu vererek `3 ile toplayan fonksiyon`a çevirme işlemine `currying` deniyor. Başka bir yazıda detaylıca gireceğim.

Ancak `context`e müdahale etmeden işlem yapmak istediğimiz zamanlar olacaktır. Örneğin; Haskell'de `Maybe` adında bir `type` mevcut. `Just` ile kapsanmış bir değer yahut `Nothing` değerine sahip olabilir. Eğer elimizde `Maybe Int` türünde bir `Just 2` değeri varsa bunu `(+3)` fonksiyonumuza nasıl verebiliriz? `+` fonksiyonu iki argüman bekliyor, ikisi de `Num` olmalı. Ancak birisi doğruyken diğeri bir kapsayıcı içerisinde.

Üstelik biz işlem sonucunda elimizdeki `Just 2` değerinin türünün değişmesini istemiyoruz. 

Çok şanslıyız çünkü `Maybe` türü bir `Functor`. 

Peki bir `Functor`ı nasıl işleme sokabiliriz? Elimizdeki `Functor`lar ile işlem yapmamız için çok güzel bir fonksiyon var ve adı `fmap`. 

Bir `Functor` ile işlem yapmak istediğimizde, `fmap` fonksiyonunu kullanabiliriz. Örneğin; `fmap (+3) (Just 2)` dediğimizde bize sonuç olarak `Just 5` verecek ve kapsayıcımız bozulmamış olacak.


`Maybe` türü için `Functor` tanımlaması ise şöyle yapılmış durumda: 

```haskell
instance Functor Maybe where
    fmap func (Just val) = Just (func val)
    fmap func Nothing = Nothing
```

Gördüğünüz gibi net bir şekilde `Functor`  `typeclass`ından tanımlanmış `instance`lar ile `Functor`ların `fmap` fonksiyonu ile nasıl çalışabileceği belirlenmiş durumda. Tüm `Functor`lar için `instance`lar bu şekilde tanımlanmıştır. `Maybe` için eğer beklenen değer `Nothing` gelirse verilen fonksiyon uygulanmadan `Nothing` dönmektedir.

Ayrıca Haskell'de listeler de birer `Functor` `instance`ına sahiptir. Bunu da diğer yazıda paylaşacağım.

#haskell #tr #functor #functionalprogramming #fonksiyonelprogramlama


