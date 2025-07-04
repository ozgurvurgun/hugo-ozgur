+++
categories=['Development', 'Test','Turkish']
date= '2024-01-29'
description= 'Yazdığınız kodun çalıştığını nasıl garanti edersiniz? Sürekli gelişen ve değişen karmaşık sistemlerin en büyük sorunu ise geliştirmeyi yapan kişinin iş üzerindeki kontrolünün zaman içinde kaybolmasıdır.'
slug= 'otomatize-testler-ve-test-cesitleri'
tags=['test','make', 'phpunit']
title= 'Otomatize Testler ve Test Çeşitleri'
+++


Yazdığınız kodun çalıştığını nasıl garanti edersiniz? 

Bir web uygulaması yaz ve bitir işi değildir, sürekli olarak gelişen ve değişen bir sistemdir.
Sürekli gelişen ve değişen karmaşık sistemlerin en büyük sorunu ise geliştirmeyi yapan kişinin iş üzerindeki kontrolünün zaman içinde kaybolmasıdır. 
Pek çok parçadan oluşan bir uygulamada, yeni kısımlar eklenip diğer kısımlar değiştikçe nerede ne olduğunu takip etmek zorlaşır ve uygulamanızın çalışır durumda olduğunu kanıtlanmanız gerekir. 

Tabi ki bunu yapmadan da bir uygulama geliştirebilirsiniz. 
Ancak test olmadan geliştirilen bir uygulama çok kısa bir vade içerisinde ölmeye mahkumdur. 
Çünkü geliştirme süreci, bir noktada kontrol kaybedildiği için tıkanmaya başlar ve artık hiç bir değişiklik yapamaz olursunuz.

Otomatize testler sizi bu sorundan korur. Başkalarının iddia ettiği gibi test yazmak sizin uygulama geliştirme hızınızı yavaşlatmaz.
Eğer öğrenmeye başladığınız ilk günden itibaren test yazmayı alışkanlık haline getirirseniz, uygulamanızı geliştirirken diğer herkesten daha hızlı olabilirsiniz. 
Çünkü teknik borçlanmanız olmayacaktır. 
Teknik borçlanma bir işi yaparken "sonra yaparız" dediğiniz şeylerin tümüdür. Bir noktada teknik borçlar ödenemezse uygulama ölüme sürüklenir. 

Teknik borçlanmayı sıfırda tutamazsınız, ancak minimize etmenin en temel yolu test yazmaktır. 
Test yazmak da öyle çok karmaşık bir iş değildir. Normalde yaptığınız denemelerin otomatize edilmesinden başka birşey değil. 
Ayrıca bazıları otomatize testlerin her durumu yakalayamayacağını, ille de manuel testlere ihtiyaç olduğunu iddia ederler. Tamamen doğru değildir. 
Yakalayamadığınız durumları manuel denemelerle yakalayıp bunları da otomatize edebilirsiniz. İnsanın elle yapabildiği hemen hemen herşey otomatize edilebilir. 

PHP dünyasında otomatize test yazmak diğer dillerden çok daha kolaydır. Mantığını anladığınızda çok basit kodlar ile dahi test yazabilirsiniz. 


Örneğin bir internet sitesinin hala çalıştığını garanti etmenin yolu nedir? 
Http protolokünde belirtildiğine göre, istek attığınızda Http Header'ı olarak 200 kodunu geri dönüş olarak almaktır.
Bunu otomatize etmek için herhangi bir dili kullanabilirsiniz. 
```
.PHONY: test

test:
	@STATUS=$$(curl -s -o /dev/null -w "%{http_code}" https://www.google.com); \
	if [ $$STATUS -eq 200 ]; then \
		echo "Test passed. Status code is 200."; \
	else \
		echo "Test failed. Status code is $$STATUS."; \
		exit 1; \
	fi
```

Bu örnekte Make'i kullanarak, curl denen linux tabanlı işletim sistemlerinde dahili olarak bulunan bir aracı kullanarak basit bir test yazmış olduk. 
Hatta bu testi elle yazmadım bile, chatgpt'e bu testi yaz dediğimde bana bu sonucu verdi. Ancak bizi pek çok yükten kurtarabilecek bir iş bu. 
Deployment yaptıktan sonra sitemiz çalışıyor mu çalışmıyor mu için tarayıcıyı açıp elle deneme yapmamız gerek yok artık.
`make test` komutunu çalıştırıp sonucu hemen görebiliyoruz. Böyle bir komutun varlığı, bizim CI/CD (devamlı entegrasyon / devamlı yayınlama) diyebileceğimiz süreçle artık ek birşey yapmadan kullanabilmemize de imkan tanıdı.


Otomatize testler söz konusu olunca pek çok kategoride, çok çeşitli amaçlara özelleşmiş testler bulunmaktadır. 
Siz yazacağınız uygulamanın gerekliliklerine göre hangi türde testler yazacağınızı zaman içinde kendiniz belirleyebileceksiniz. 
Ancak başlangıç olarak şu test türlerini bilmeniz iyi olacak. Hepsi için ingilizce isimlerini kullanacağım, terimleri türkçeleştirmekle vakit kaybetmenizi önermiyorum. Türkçelerini de bilin ancak bahsederken veya araştırma yaparken ingilizce isimlerini kullanın. 

### Unit Testing
Birim testi olarak çevirilen bir test türü. 
Uygulamanızı birimlere bölüp test etmeye verilen isim.
Fonksiyonlar olabilir, sınıflar olabilir, tek bir kod satırı olabilir. 
Unit test'in amacı, yazdığınız kodun istediği sonucu verip vermediğinde emin olmaktır. 
Örneğin 1+1=2  sonucunu hesaplayan toplama isimli fonksiyonunuz için yazacağınız unit test, o fonksiyona gerekli parametleri verip sonucu kontrol etmektir. Gerçek olmayan bir kodla şöyle diyebiliriz: 

```
function toplama(arg1,arg2){
return arg1 + arg2;
}
// test kodu
echo toplama(1,1)==2 ? "Test gecerli":"Test gecersiz";
```

Gördüğünüz gibi kodu yazdım ve altında testini yazdım. artık bu fonksiyonun bu şekilde çalışacağının bir garantisine sahibim. Temel seviyede amacımıza ulaştık.
Tabi test kodunu ayrı bir dosyaya yazacağım, o dosyada testlerin tümü tek seferde çalıştıracağım ve tüm sistemdeki diğer parçalarla birlikte otomatize bir şekilde bu fonksiyonun da çalıştığını garanti edebileceğim. 

Böyle bakınca ne gerek var bunu test etmeye diye düşündüğünüzü görür gibiyim.
Düşünmeyin, yeterli bilgiye sahip olmadığınız konularda mantık yürütmekten uzak durun. 
Çünkü biz burada basit kodlar üzerinde testleri gösteriyoruz, aklınızda "gerçek dünyada ne işe yarayacak bu " soruları elbet olacaktır, bunların ne işe yarayacağını test yazdıkça göreceksiniz. 
Eğer aklınızda oturmayan birşeyler olursa da sormaktan çekinmeyin.

### Integration Testing
Bu test türünün amacı ise, uygulamanızdaki büyük parçaların birbiriyle entegre olurken karşılaşabileceğiniz senaryoları test etmektir. 
Örneğin, veritabanı bağlantısı yapacaksınız, uygulamanız ve veritabanı ayrı iki sistem. Veritabanına bağlantı yapılırken karşılacağınız senaryoları test etmeniz gerekir. (Network katmanına geçerek veya geçmeden)
Veritabanına bağlantı için yazdığınız kodların düzgün çalışıp çalışmadığı, veritabanındaki güncellemelerden kaynaklı sizin uygulamanızın geri kalıp kalmadığını kontrol etmeniz gibi durumları test edeceksiniz. 

### Functional Testing
En çok yüzgöz olacağımız test türlerin biri işlevsellik testleri. 
Uygulamanın işlevsellik sağlayan parçalarının test edilmesidir.
Örneğin bir forum düşünün, bu forumun özel bir alt sayfasına inmek için adres satırından o alt forumun ID 
veya SLUG denen parametresini vermek gerekir. 
site.com/forum/yeniler-forumu , burada yeniler-forumu kısmına SLUG diyoruz ve bu bir parametre. 

Yazdığımız test bu adrese istek yapıp gerçekten yeniler-forumu içeriklerini getirip getirmediğini kontrol eder.
Bu ve benzeri testlere işlevsellik testleri diyoruz. Formlara bilgi girişi yapılıp kaydet tuşuna basılması vb şeyler de işlevsellik testlerine girer.

Şimdilik 3 ana test türüyle devam edelim.
Bunlar dışında pek çok farklı test türü var, zaman içerisinde ihtiyaçlarınıza en uygun test türlerini seçmeniz gerekecek. 
Çoğu zaman birden fazla test türünü beraber kullanacaksınız. Diğer test türleriyle ilgili daha detaylı bilgiler için kaynaklar kısmına gözatabilirsiniz. 

## Test neden önemli? 

Otomatize testler, kalite garantisidir. Zaman içerisinde uygulamanızın özellikleri ve davranışları değişse bile kalitesinden ödün vermemek için testleriniz olması gerekir. 

Otomatize testler, test hızınızı arttırır. Bir kod değişikliği yapıp, o değişikliğin nereleri etkilediğini kendiniz test etmekten daha hızlı sonuç alırsınız. 

Otomatize testler, maliyeti azaltır. Uzun vadede kodunuzun kalitesi bozulmayacağı için hata çıkma ihtimali düşük olur ve size ileride mali olarak bir yük getirmez. 
Ayrıca çıkabilecek hataları otomatize testler sayesinde daha hızlı yakalayıp çözeceğiniz için maliyetleriniz daima düşük kalacaktır. 

Ayrıca otomatize testleri yeterince iyi yazmışsanız bir nevi uygulamanızın dökümanını da yazmışsınız demektir. Hatta bazı güzel araçlar testlerinizde otomatik döküman dahi oluşturabilmektedirler. Ki uygulamayı dökümanlaştırmak başlı başına bir iş olduğuna göre, testler size ek fayda sağlamış oluyor. 

Otomatize kod yazdığınızda içiniz rahat bir şekilde uygulamanıza yeni bir özellik ekleyebilir ve refaktor edebilir olursunuz. 
Testi olmayan bir uygulamada bir yeri düzeltirken haberiniz bile olmadan başka bir yeri bozmak çok kolaydır. Ancak otomatize testleriniz varsa içiniz rahattır.

Otomatize testler uygulamanız büyüdükçe zorlaşacak manuel test yükünü de kolayca çözmenizi sağlar. 
Çünkü kod büyüse de karmaşıklaşsa da otomatize testler ekstra bir yük oluşturmayacaktır. 

Otomatize testler tutarlıklık sağlar. İnsan hatasını en aza indirirerek değişiklikler arasında her zaman aynı testlerin çalıştırılacağını garanti eder.
Manuel test ederken kontrol edilmesi unutulan pek çok yer olabilir. Veya daha kötüsü manuel test sırasında yaptığınız işlemlerin sırası hep farklı olacağından, aynı işlemler için farklı sonuçlar alırsınız.
Ancak otomatize testler hep aynı sırada çalışır ve aynı işlemi test ettiği garantidir. 

Karmaşık işlere odaklanıp, basit tekrarlı testlerle boğuşmak istemiyorsanız, yani yaptığınız işten keyif almak istiyorsanız
tekrarlı işleri otomatize etmek en doğru yoldur. Testleri otomatize ederek gereksiz tekrarlı testlerle uğraşmak zorunda kalmazsınız. 

Henüz kodlamamışken bile testin ne olduğu ve neden gerekli olduğunu anlayıp içselleştirmenin önemli olduğunu düşünüyorum. 

PHP uygulamalar için birim testleri yazabilmeyi sağlayan en bilinen yöntem bir test framework'ü olan PHPUnit'i kullanmaktır. Bununla ilgili ileride bir yazı yazmış olacağım. 


## Test Yazmasak Olmaz mı?

Eminim içinizden soruyorsunuzdur, test yazmasak da sadece kodumuzu yazsak olmaz mı diye.
Çoğu eğitim testleri merkezine almak yerine kodlama dersleri ile başlayıp en son konulara testleri yerleştiriyor ve bu alışkanlığın yazılımcılarda oluşmasına fırsat verilmeden kodlama dersleri bitmiş oluyor. 

Test yazmadan yapılmış binlerce, milyonlarca proje var. Ancak bu bölümde anlattığım herşeyi düşündüğünüzde anlayacaksınız ki, test yazmamak demek ölü bir projeye başlamak demektir.
Projeniz gelişirken kontrolü kaybedeğiniz garantidir. Sizden sonra gelecek yazılımcılar ve yazılım ekiplerinin iş üzerinde kontrolünün olmayacağının garantisidir. 

Özellikle PHP gibi dinamik ve derleyicisi bulunmayan dillerde kodun neresinde ne yapıldığının otomatize bir sistem tarafından sürekli kontrol edilmesi gerekmektedir. 
Haskell gibi tam tür desteği olan pür bir fonksiyonel programlama dilinde dahi, yaptığınız işlerin otomatize testlerinizi yazarsınız. 
Dile ne kadar güvenseniz de, mantıksal hataları yakalamak için buna ihtiyacınız vardır. Ancak nispeten çok az test yazarak işinizi garantiye alabilirsiniz. 

Fakat PHP, Python, Ruby, C ve benzeri dillerde ortam o kadar kaotiktir ki, yazabildiğiniz kadar test kodu yazarak uygulamanızın nefes aldığından emin olmanız gerekir. 

Eğer uygulamanız bir kere yapılacak ve bir daha hiç bir kod değişikliği vs yapılmayacaksa, 
test yazmasanız da olur tabi ki. Ancak ben 20 yıldır hiç bir uygulamanın yazıldıktan sonra güncellenmediğini görmedim.
Uygulamalar değişir ve gelişir, bu değişim ve gelişim sürecinin ilerleyebilmesinin en mecburi gereksenimi ise otomatize testlerdir. 

İleri okumalar: 

1. [Atlassian's guide on different types of software testing](https://www.atlassian.com/continuous-delivery/software-testing/types-of-software-testing)
2. [IBM's overview of software testing](https://www.ibm.com/topics/software-testing)
3. [Perfecto's guide on types of software testing](https://www.perfecto.io/resources/types-of-testing)
4. [Software Testing Help's detailed guide on types of software testing](https://www.softwaretestinghelp.com/types-of-software-testing/)
5. [Codecademy's article on software testing](https://www.codecademy.com/resources/blog/what-is-software-testing/)
