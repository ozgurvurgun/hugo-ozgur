---
title: Tmuxa hafif bir giris
description:
date: 2020-05-06 
draft: false
tags:  tr #linux #tmux
---


<iframe src="https://anchor.fm/delirehberi/embed/episodes/Bir-Sunucu-ve-Onlarca-Terminal----Tmux-edm2sn" height="102px" style="width: 100%" frameborder="0" scrolling="no"></iframe>


Hiç daha önce internette otel aradınız mı?

Evet gayet ciddi bir soru. Tabi otel için değil ancak daha önce mutlaka başınıza şu durum gelmiştir veya gelebilir. Varyasımsal olarak, diyelim ki; sağlıklı olmayan bir internet bağlantısına sahipsiniz. Hani Türkiye'de olmaz da, diyelim ki oldu yani.
<!--more-->
Sonra bir gün sunucularınızdan birine SSH ile bağlanıyorsunuz ve uzun sürmesi muhtemel bir iş başlatıyorsunuz. Sonra bu iş tamamlanmadan internet bağlantınız koptu. Noldu şimdi? Sunucudaki oturumunuz düştü, çalıştırdığınız iş tamamlanmamıştı halbuki. Haydaaa. Bağlantı gelince tekrar bağlanmanız ve işi tekrar çalıştırmanız gerekecek. Üstelik bu iş eğer statefull bir uygulamaysa, yani işi yapıp biryerlere bir şeyler kaydediyorsa, o kayıt bozuk halde kalmış olabilir. Öyle olmasa bile baştan başlamanız gerekiyor. Bunu yaşadıktan sonra bu gibi durumlar için çözümler arayıp, `nohup` veya `screen` gibi çözümlere ulaştınız. 

Artık en azından internetiniz kesilse bile işinizin yarıda kalmayacağını biliyorsunuz. Fakat pek sağlık çözümlermiş gibi gelmiyor değil mi? İş çıktılarını göremediğiniz için bir dosyaya yönlendirmeniz gerekiyor vb. 

Tmux sizi büyük bir yükten kurtarak 10 milyondan fazla sunucudaki sessionları tarayabilir.

Ah keşke...

Hayır tabi ki Tmux öyle bir şey değil.

Tmux gavurcada `multiplexer` denen, bizim çoklayıcı diyebileceğimiz bir terminal aracı. Ek olarak yukardaki bahsettiğim senaryoda bizim imdadımıza yetişen ise, `session management` yani oturum yönetimi desteği.

Sunucuya bağlandınız, `tmux new-session -s oturumadi` komutu ile Tmux'ı başlattınız, uzun iş yapacak uygulamanızı açtınız, yeni `pane` denen (aşağıda bahsedeceğim) şeylerden ve `window` denen sekmelerden açtınız, tek bağlantı ve tek terminal üzerinde bir çok işi birden yaptınız. Uzun süren işiniz devam ederken `tmux detach` komutunu çalıştırdığınız ve Tmux'ı arkaplana attınız. Artık sunucudan kopabilirsiniz. İnternetin kopması veya bilgisayarınızı kapatıp başka işlerinize bakmanız sorun değil artık.

Daha sonra sunucunuza bağlanarak `tmux attach-session -t oturumadi` yazdığınızda, daha önce çalıştırdığınız uygulama ve diğer `pane`leri hiç bir sorun yok gibi kullanmaya devam ettiniz.

Çok güzel değil mi? Uzun çalışan uygulamanız yoksa bile, düşünün ki sunucuya her girdiğinizde yaptığınız sıradan işler var. Belli bir klasöre gitmek, mysql client'ı açmak, redis-cli'ı açmak vs. Normal bir durumda her defasında sırayla bu işleri yapmanız gerekirken. Daima açık bir Tmux `session`'ı içerisinde bu `pane`leri açık bırakarak her sunucuya bağlanmanızda sadece tmux oturumunu geri yükleyip işinize devam edebilirsiniz.

Ayrıca aynı oturumu diğer kişilerle de paylaşabilirsiniz? 

İyi de hangi senaryoda?

Varsayımsal konuşmaya devam edelim. Siz diyelim ki bir çırağa mentorluk yapıyorsunuz. O kişiye bir şey anlatmaya çalışıyorsunuz ve bu karmaşık bir dizi komut çalıştırmak ve denemek anlamına geliyor. Ne yaparsınız? Korona olmasaydı dizinizin dibinde bak çocuğum diyerek anlatabilirdiniz.

Ama o da ne? Arada mesafeler var. Hmm, uzak ilişki. Hiç benlik değil.

Elbette ekran paylaşarak vb çözümlerle bu işi gerçekleyebilirsiniz. Ancak daha güzelini söyleyeyim.

Tmux sizi büyük bir yükten kurtararak...

Evet Tmux, oturumları paylaşabiliyorsunuz. İkiniz de aynı sunucuya bağlandınız; siz bir tmux oturumu oluşturdunuz `tmux new-session -s deneme` ve diğer kişi de sunucuya bağlandı. Ardından sizin oturumunuza direkt olarak katılabilir: `tmux attach-session -t deneme` dediğin anda, siz kendi terminalinizde ne yaparsanız girdi ve çıktıya diğer arkadaş da ulaşabilir.

Şahane bence.

Multiplexing özelliğini ise basit komutlarla gerçekleştiriyorsunuz. Bundan sonra yazacakların bir tmux oturumu içerisindeyken yapabileceğiniz şeyler.

Vim'de olduğu gibi Tmux da birden fazla modda çalışıyor. Komut moduna geçmek için `ctrl+b` tuşuna basabiliyorsunuz. Eğer `:` yazarsanız - buna da komut modu diyeceğim ama, bu komutu yazmanızı bekliyor, ctrl+b ile girdiğiniz sizden sessizce komutlar bekliyor. anlatacağım - sizi diğer komut moduna alıyor ve komutlarınızı yazabiliyorsunuz Vim'de olduğu gibi.

`ctrl+b` tuşuna bastıktan sonra `"` tuşuna basarsanız, ekranı yatayda ikiye bölebilirsiniz. `%` tuşuna basarsanız ekranı yatayda ikiye bölersiniz. Bu böldüğümüz şeylerin adı `pane` olarak geçiyor. Yeni bağlantılar yapmaya gerek kalmadan aynı sunucu üzerinde birden fazla terminale sahip oldunuz. `[` tuşuna basarak scroll modunu açabilirsiniz. Bunun gibi daha fazla komut mevcut. Bu komutları `hille-i şerie` denen bir yöntemle öğrenebilirsiniz.

Hmm, sanırım `hille-i şerie` başka bir şeydi. Cheatsheet diyelim, evet hile kağıdı. Google'da `tmux cheatsheet` diye aratarak bulabilirsiniz. Ancak benim önerim `man tmux` yazarak kendi dökümanını okumaktır.

Göreceksiniz ki Tmux bu anlattıklarımla sınırlı değil. Tamamen özelleştirilebilir bir uygulama tabi, ayrıca eklenti desteği de mevcut. Kurulumu ise tabi ki oldukça kolay, sunuculardaki linux dağıtımına göre `paketyoneticisi install tmux` yazarak, örneğin debian tabanlılar için `apt install tmux` yazarak kurabilirsiniz. Mac için `brew install tmux` yeterli olacaktır. Windows için `format c:/` yazarak kurabilirsiniz.

Tmux hayat kurtarır. 

Sağlıklı günler, saygılar.



-- dip not :

Eğer kendi bilgisayarınızda ve sunucuda tmux kullanıyorsanız, iç içe iki tmux olacağı için komutlar karışacaktır. Bunun için en basit çözüm sunucudaki tmux'a komut göndermek için `ctrl+b` tuşuna iki defa basmaktır.


