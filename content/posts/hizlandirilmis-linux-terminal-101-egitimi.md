---
title: Hizlandirilmis linux terminal 101 egitimi
description:
date: 2019-09-22 
draft: false
tags:  unix #tr #bash #linux-101
---


Unix tabanlı işletim sistemi kullanıp nimetlerinden faydalanmayan çok insan görüyorum. Özellikle araştırmacı olup windows'a yahut macos'un arayüzüne mahkum olanlar için hızlıca unix felsefesinden bahsederek nasıl işlerinizi daha etkili bir şekilde halledebileceğinizi anlatmaya çalışacağım.
<!--more-->
### Unix Felsefesi

Unix felsefesi formal bir felsefe değildir. Tecrübeyle geliştirilmiş bir felsefedir. Kusursuz uygulama nasıl yapılır diye bir şey söylemez ya da bir uygulamayı nasıl yapmanız gerektiğiyle ilgilenmez. Unix felsefesi tüm sistemi oluşturan parçalara odaklanır ve bütünleşme üzerine fikirler yürütür.

Unix felsefesinin temelleri şöyledir¹:

- Bir uygulama sadece tek bir şeyi iyi yapmalıdır. Bir iş için yeni bir uygulama yapmak, eski uygulamaya özellik ekleyip karmaşıklaştırmaktan daha iyidir.
- Bir uygulamanın vereceği çıktı, diğer bir uygulama için girdi verisi olarak kullanılabilmelidir. Çıktıyı gereksiz detay bilgileri ile kirletmeyin. (Detay bilgi dönmesi gerekebilen uygulamalar için -v parametresi ile verbose mod desteği ekleyerek uygulamanın arzu edilirse detaylı çalışmasını sağlayabilirsiniz. Ya da başka bir parametre ile.)
- Bir uygulama interaktif girdi beklememelidir. Girdilerini parametre ile ve/veya stdin (başka bir program çıktısı) üzerinden alabilmelidir.

### Pipeline

Temel unix felsefesini oluşturan kuralların işleyebilmesi için bilinmesi gereken en önemli şey "Pipe"(|) işaretinin yaptığı iştir. Çünkü bir programın başka bir programdan girdi alarak ilerleyebilmesinin, yani birleştirilebilir olmasının yöntemidir. Türkçe klavyeler için `alt` işareti ile beraber `-` işaretine basarak ulaşabilirsiniz. Tam olarak yaptığı iş, solundaki program ne dönerse bunu al sağdaki arkadaşın girdisi olarak kullan.

En basit uygulamalardan birisi `ls` ile bir örnek yapmak için `wc` uygulamasını kullanabilirsiniz. `ls` bulunduğunuz klasördeki veya verdiğiniz klasördeki dosya ve klasörleri listeler, `wc` ise verilen girdide kelime sayma işlemi yapmanızı sağlar. 

`ls /usr/bin/|wc -l`

Yukarıdaki örnek benim bilgisayarımdaki `/usr/bin` klasöründe `2369` adet dosya olduğunu söyleyebiliyor. `ls` e parametre olarak verdiğim klasör her satıra bir dosya-klasör çıktısı olacak şekilde bir metin üretiyor. `wc` ise `-l` parametresi ile satırların sayısını istediğimi söylüyor. Sonuçta bana klasörde kaç dosya olduğunu söyleyen bir uygulama elde etmiş oluyorum. İki programın da yapmak için planlanmadığı bir program çıktısı oluştu.

Birisi dosya-klasör gösteren, diğeri ise verdiğin metinde sayma yaparım diyen iki programı birleştirerek, istediğin klasördeki dosya-klasör sayımı yapmamı sağlayan bir program programlayabilmiş oluyorum.

Unix felsefesinin en temelindeki fikir budur. 

### Mantıksal işlemler

Terminal üzerinde mantıksal işlem yapabilmenize imkan sağlayabilen oparatörler mevcut. AND ve OR işlemler yapmak istediğiniz zaman `&&` ve `||` operatörlerini kullanabilirsiniz. Yan yana iki uygulama çalıştırmak istiyorsanız ve birbirlerinden bağımsız hareket etsin isterseniz de `;` operatörünü kullanabilirsiniz.

Örneğin: `cd /missing/dir && ls` komutunu çalıştırırsanız ilk komut çalışmayacağı için ikinci komut es geçilecektir. Ancak `cd /missing/dir || ls` dediğinizde soldaki komut çalışmasa bile `ls` komutu çalışacaktır.


### MAN

Tabi ki unix tabanlı işletim sistemlerinde bu kadar küçük parçalardan oluşan yüzlerce-binlerce küçük uygulama bulunmaktadır. Hepsinin nasıl çalıştığını bilmeniz mümkün değil. Bu yüzden `man` adında bir uygulama daha mevcut. Geliştirilen uygulamalar için hazırlanmış yardım dökümanlarını görüntülemek için kullanabilirsiniz.

Örnek olarak `man ls` yazıp çalıştırırsanız, size `ls` uygulaması hakkında tüm detayı verecektir. Hangi parametreyi alır, hangi işleri yapar, nasıl kullanılır gibi.

Eğer küçük bir hilekarsanız, "bu dökümanlar çok uzun, nasıl kullanıldığını bilsem yeter" derseniz diye tembel yazılımcıların oluşturduğu hile servislerini kullanabilirsiniz. Örnek olarak cheat.sh size yardımcı olabilir. Bunun için `curl` adındaki, internetten veri almanıza olanak sağlayan bir uygulamayı kullanabilirsiniz.

`curl cheat.sh/ls` komutunda olduğu gibi adres satırının sonuna öğrenmek istediğiniz uygulamanın adını koymanız yeterlidir. cheat.sh/ls adresine tarayıcınızdan da ulaşabilirsiniz.


```bash
$ curl cheat.sh/

# Displays everything in the target directory
ls path/to/the/target/directory

# Displays everything including hidden files
ls -a

# Displays all files, along with the size (with unit suffixes) and timestamp
ls -lh 

# Display files, sorted by size
ls -S

# Display directories only
ls -d */

# Display directories only, include hidden
ls -d .*/ */

```

### Nasıl kullanayım?

Bu noktaya kadar aslında öğrenmeniz gereken her şeyi öğrendiniz. `/usr/bin` ve `/usr/local/bin` klasörleri altındaki çoğu uygulamayı bu yöntemle istediğiniz amaçlar için kullanabilirsiniz. 

Örneğin ben sunucumdaki anlık bağlantı sayısını görüntülemek için aşağıdaki gibi bir kod inşa edebilirim:

`curl -s http://sunucu_ip/nginx_status|head -n 1|sed -e 's/Active connections: //g'`

`curl` ile sunucumdan anlık veriyi çekiyorum, bana detaylı bir veri çıkartıyor.

```
Active connections: 300
server accepts handled requests
 1544990299 1544990299 1308915160
Reading: 0 Writing: 2 Waiting: 286
```

Bu veri içerisinden ben `head` uygulaması ile sadece en üstteki satıra ihtiyacımı belirtiyorum. Buradan dönen sonucu da `sed` adlı kelime işlemci ile `Active connections:` yazan kısmı temizleyip sadece sayıya ulaşabiliyorum ki, 20 snyde bir çalışıp çıktısını bilgisayarımın bir köşesinde sürekli göreceğim için elimde sadece sayı olsun. 


Araştırmacılar için çok fazla potansiyel sunan unix dünyası, işlerinizi çok hızlı bir şekilde yapmanızı sağlayacaktır. Her araştırma alanı için mevcut olan bir çok unix-felsefesini benimsemiş uygulama mevcut. Örneğin [Zorro](https://sourceforge.net/projects/probmask/files/) adındaki uygulama fasta formatında dna verileri için biyologların anlayacağı bir işlem yapıp sonucunu metin olarak dönüyor. Temel kuralları uyguladığı için zorro ile pipelining yaparak birleştirebileceğiniz diğer programlar işlerinizi daha kolay yapabilir hale geleceksiniz. 

Buna benzer uygulamaları alanınıza göre zamanla öğrenerek, işlerinizi daha kolay halledebilir hale geleceksiniz.

### Alan bağımsız

Tabi alanınızdan bağımsız olarak kurcalamanızı önereceğim bazı, her unix türevinde bulunan uygulamalar mevcut. Her uygulamada `man` için hazırlanmış dökümanlar olmayabiliyor. Ancak her uygulama `-h` parametresi ile çalıştırıldığında temel bir yardım dökümanı sunuyor.

`cd` klasör değiştirmenizi, `ls` dizin içeriği görüntülemenizi, `pwd` nerede olduğunuzu bilmenizi sağlar. `mkdir`,`touch`,`cp`,`rm`,`mv`,`ln` dosyalara ve klasörlere müdahale etmenizi sağlar. `head`,`tail`,`less`,`more`,`cat` dosyaların içeriklerini almanızı sağlar. `curl`,`wget` internetteki dosyalara erişmenize yardım eder.

`uniq`,`sort`,`wc`,`diff`,`cmp`,`cut`,`sed` metinlerle işlem yapmanızı sağlarken `awk`,`grep` ise metinler içerisinde desen aramanızı ve/veya işlem yapmanıza olanak sağlar.

`/usr/bin` ve `/usr/local/bin` klasörünüzü kurcalayarak diğer uygulamalar da ulaşabilirsiniz.



1: https://homepage.cs.uri.edu/~thenry/resources/unix_art/ch01s06.html

