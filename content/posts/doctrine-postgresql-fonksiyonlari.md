---
title: Doctrine postgresql fonksiyonlari
description:
date: 2020-05-08 
draft: false
tags:  tr #doctrine #symfony #postgresql
---


Symfony ile uygulama geliştirirken tüm veritabanı işlemleri Doctrine üzerinden gerçekleştiriliyor. Doctrine ise tüm veritabanlarına ortada durabilmek adına maksimum ortak paydaya ulaşarak buna uygun programlanıyor. 

Yani bir özellik geliştirileceği zaman, en optimum şekilde, tüm veritabanlarına uyacak kadarını geliştiriyorlar. Bu sebeple PostgreSQL'in pek çok güzel özelliği içerisinde gelmiyor.
<!--more-->
Tabi ki `native sql` yazarak Doctrine içerisinden direkt saf PostgreSQL sorgusu çalıştırabiliyorsunuz. Fakat benim bahsetmek istediğim Doctrine'in diğer bir güzel özelliği `kullanıcı tanımlı DQL fonksiyonları` kullanabiliyor olmak. 

Çok detaya inmeden bahsetmek gerekirse; Doctrine SQL sorgularını yazılımcıdan DQL formatında talep edip, kendisi veritabanına uygun hale getirip SQL üretiyor. DQL formatında ise yukarıda bahsettiğim sebepten ötürü sadece belirli veritabanı özelliklerini destekleyebiliyor. Fakat DQL formatı genişletilebilir bir format olduğu için biz kendi DQL özelliklerimizi yazıp, Doctrine'e kazandırabiliyoruz. 

PostgreSQL fonksiyonlarını kullanabilmek için de bu özellikten yararlanacağız. Doctrine kendi[*](https://www.doctrine-project.org/projects/doctrine-orm/en/2.7/cookbook/dql-user-defined-functions.html#dql-user-defined-functions) sitesinde çok güzel şekilde nasıl kendi DQL fonksiyonları yazabileceğinizi anlatmış. 

Peki ben niye konuşuyorum?

Tabi ki [Martin Georgiev](https://github.com/martin-georgiev/postgresql-for-doctrine)'in zaten yazmış olduğu, PostgreSQL fonksiyonlarının DQL genişletmelerini içeren paketten bahsetmek için. 

Composer ile paketi yükleyebiliyorsunuz.

`composer require martin-georgiev/postgresql-for-doctrine`

Sonrası ise kullanacağınız fonksiyonları, framework'ünüze özel olarak tanımlamak kalıyor. Bu bir Doctrine paketi olduğu için Symfony bağımlı değil, diğer frameworklerle veya frameworksüz de kullanabilirsiniz.

Symfony 5 için aşağıdaki gibi tanımlayabiliyorsunuz:

```yaml
doctrine:
    dbal:
        types: 
          text[]: MartinGeorgiev\Doctrine\DBAL\Types\TextArray
        mapping_types:
          text[]: text[]
          _text: text[]
    orm:
        dql:
          string_functions:
            ANY_OF: MartinGeorgiev\Doctrine\ORM\Query\AST\Functions\Any
```

Sadece `text[]` türünü ve `ANY` fonksiyonunu yükledim bu örnekte. Tüm fonksiyon ve türler için [postgresql-for-doctrine](https://github.com/martin-georgiev/postgresql-for-doctrine/blob/master/docs/INTEGRATING-WITH-SYMFONY.md) dökümanına göz atabilirsiniz.

Ayarlarınızı yaptıktan sonra ise kullanması kalıyor. Normalde her şeyi `Expr` ile oluşturmayı seviyorum ancak kullanıcı tanımlı bu fonksiyonlar için `Expr` ifadeleri bulunmuyor. 

```php
    $qb = $this->createQueryBuilder('u');
    $qb->andWhere(":role <> ANY_OF(u.roles)")
       ->setParameter('role','ROLE_ADMIN');
    return $qb->getQuery()->getResult();
``` 

Yukarıda gördüğünüz gibi, sorgu olarak direkt kullanabiliyoruz. 

Dikkat etmeniz gereken şey, PostgreSQL ifadelerinden bazıları DQL'de aynı isimde geçmiyor. PostgreSQL'de bulunan `ANY` ve `ALL` gibi fonksiyonları `ANY_OF` ve `ALL_OF` gibi DQL'e geçmiş olmasının sebebi, zaten DQL içerisinde `ANY` ve `ALL` adında iki fonksiyonun bulunması. Ama tabi ki amaçları bambaşka. 

