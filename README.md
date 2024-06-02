# **SQL INJECTION​**

![alt text](https://www.infinitumit.com.tr/wp-content/uploads/2022/11/What-is-a-SQL-injection.png)

## **Uyarı**

Bu proje, yalnızca yetkili olduğunuz ağlarda kullanılmalıdır. İzinsiz ağlara erişim yasa dışıdır ve ciddi yasal sonuçlar doğurabilir. Bu araçları kullanarak yapılan her türlü yasa dışı faaliyetten tamamen kullanıcı sorumludur.

# **1-SQL Injection Nedir?**

SQL enjeksiyonu, veri tabanına dayalı uygulamalara saldırmak için kullanılan bir atak tekniğidir; burada saldırgan SQL dili özelliklerinden faydalanarak standart uygulama ekranındaki ilgili alana yeni SQL ifadelerini ekler. (Örneğin saldırgan, veritabanı içeriğini kendisine aktarabilir).​

**SQL enjeksiyonu**, uygulamaların yazılımları içindeki bir güvenlik açığından faydalanır, örneğin, uygulamanın kullanıcı giriş bilgileri beklediği kısma SQL ifadeleri gömülür, eğer gelen verinin içeriği uygulama içerisinde filtrelenmiyorsa veya hatalı şekilde filtreleniyorsa, uygulamanın, içine gömülmüş olan kodla beraber hiçbir hata vermeden çalıştığı görülür. SQL enjeksiyonu, çoğunlukla web siteleri için kullanılan bir saldırı türü olarak bilinse de SQL veri tabanına dayalı tüm uygulamalarda gerçeklenebilir.​

SQL enjeksiyon saldırıları, saldırganların, sistemdeki kullanıcılardan birinin bilgileriyle giriş yapmasına, mevcut verilere müdahale etmesine, bazı işlemleri iptal etmesine veya değiştirmesine, veri tabanındaki tüm verileri ifşa etmesine, veri tabanındaki tüm verileri yok etmesine, veri tabanı sunucusunda sistem yöneticisi olmasına olanak sağlar.​

​

SQL enjeksiyonu, SQL alt dilini kullanan **PHP**, **ASPX** gibi birçok programlama dili üzerinde görülebilmektedir. Bu atak tekniği, SQL dili kullanılan sistemlerde genellikle **GET** ve **POST** verileri gönderilir ve alınırken yapılmayan filtrelemeler sebebiyle ortaya çıkar.​

# **2-SQL Injection Teknik Uygulamaları**

​## **2.1-Çıkış (Escape) Karakterlerinin Yanlış Filtrelenmesi**
​
2.1-Çıkış (Escape) karakterlerinin yanlış filtrelenmesi​

SQLi'ın bu türü, kullanıcıdan gelen veri escape karaklerlerine göre filtrelenmediği zaman, uygulamaya kullanıcı girişinden yeni SQL ifadeleri eklenmesiyle oluşur. Eklenen SQL ifadeleri, son kullanıcının veri tabanını istediği şekilde manipüle etmesine neden olur.​

Aşağıdaki kod satırı, bu güvenlik açığını göstermektedir:​

sorgu = ```SELECT * FROM kullanicilar WHERE isim =' " + kullaniciAdi + " ';"```

​

Yukarıdaki SQL sorgusu girilen kullancı adı bilgisine ait tüm verileri, kullanıcı tablosundan çekecek şekilde tasarlanmıştır. Sistem kullanıcı adı bekliyorken,kullanıcı adı kısmına kötü niyetli bir kullanıcı tarafından sisteme zarar verecek sql ifadeleri yazılabilir. Örneğin, "kullaniciAdi" değişkenini aşağıdaki şekilde düzenleme yapılabilir.​

' or '1'='1​

​

Bu girdi Sql ifadesinin aşağıdaki şekilde işlenmesine neden olur:​

sorgu = ```SELECT * FROM kullanicilar WHERE isim =' " + kullaniciAdi ' or '1' = '1 + " ';"```


Sorgunun geri kalanını engellemek için yorum satırı karakterleri kullanılır.​

' or '1'='1' -- ​
' or '1'='1' ({ ​
' or '1'='1' /*​

​

Saldırgan bu SQL ifadelerini, uygulamaya kullanıcı girişi olarak girerse, uygulama tabanında çalışacak sorgu aşağıdaki şekilde olur.​

```SELECT * FROM kullanicilar WHERE kullaniciAdi ='' or '1' = '1 + " ';​```

```SELECT * FROM kullanicilar WHERE kullaniciAdi = '' OR '1'='1' -- ';​```

​

kullaniciAdi verisi ne olursa olsun '1'='1' koşulu sağlanacağı için ve aradaki işlemin OR olmasından dolayı sorgu sonucu her zaman olumlu olacaktır. Yorum satırı karakterlerinden sonra gelen tüm karakterler yorum niteliği kazanacaktır ve onların bir önemi kalmayacaktır.​
​

Aşağıdaki SQL ifadesindeki "kullaniciAdi" na girilen değer, kişiler tablosunun silinmesine ve kişiBilgileri tablosundaki tüm verilerin ifşa edilmesine neden olur.​

```DROP TABLE kullanıcilar; SELECT * FROM kullanıciBilgileri WHERE 't' = 't';​```

Bu girdi Sql ifadesinin aşağıdaki şekilde işlenmesine neden olur:​

```SELECT * FROM kullanicilar WHERE kullaniciAdi = 'a';DROP TABLE kullanicilar; SELECT * FROM kullaniciBilgileri WHERE 't' = 't';​```

## **2.2-Yanlış Tip İşleme (Incorrect type handling)​**

SQLI'ın bu türü, kullanıcı tarafından girilen alanın tür kontrolü düzgün yapılmadığında oluşur. Bir sql ifadesinde sayısal değer kullanıldığında, kullanıcının girdisinin de sayısal değer olması gereklidir. Bu kontrol yapılmadığı zaman bir güvenlik açığı oluşur. Örneğin:​

sorgu = ```SELECT * FROM kullaniciBilgileri WHERE id =" + deger + ";```

​

Bu ifadede "id" alanına bir sayının gelmesi amaçlandığı açıktır.Ancak bir string gelmesi bekleniyorsa, son kullanıcı sql ifadesini istediği şekilde değiştirebilir.Örneğin değer yerine aşağıdaki ifade yazılırsa,​

1;DROP TABLE kullanicilar​

​

Sql ifadesi aşağıdaki şekilde olacak ve bu ifade sonucunda kullanicilar tablosu veri tabanından silinecektir.​

```SELECT * FROM kullaniciBilgileri WHERE id=1; DROP TABLE kullanicilar;​```

## **2.3-Körleme SQL Enjeksiyonu**

Körleme SQL enjeksiyonu, bir web uygulaması bir SQLI'na karşı açık olduğunda kullanılır, ancak sonuçları saldırgan tarafından görülemez.Güvenlik açığı bulunan sayfada veriler ifşa edilemez ama SQL ifadesinin içine gömülmüş olan mantıksal ifade nedeniyle değiştirilmiş veriler görüntülenebilir.​

## **2.4-Koşullu yanıtlar (Conditional Responses)​**

​Veritabanını, sıradan bir uygulamada, mantıksal bir ifadeyi değerlendirmeye zorlar. Örneğin bir kitap inceleme sitesinde, hangi kitabın görüntüleneceğini belirlemek için yandaki sorgu kullanılır.​

```SELECT * FROM kitapIncelemeleri WHERE id=Deger(ID);​```

​

Sorgu sunucuda tamamlanır. Kullanıcı veritabanının, tablonun veya alanların adlarını ve sorgu ifadesini bilmediği için bu kısımlara müdahale edemez. Sadece yukarıdaki URL'nin bir kitap incelemesi getirdiğini görür.Ancak saldırgan :​

```SELECT * FROM kitapIncelemeleri WHERE id='5' OR '1'='1';​```

​
```SELECT * FROM kitapIncelemeleri WHERE id='5' AND '1'='2';​```
​

Sorgularına bu şekilde müdahele ederse, sorgu büyük olasılıkla her iki durumda başarıyla geçmiştir ve site SQL enjeksiyonu saldırılarına açıktır.​

## **​2.5-İkincil SQL enjeksiyonu (Second Order SQL Injection)​**

​İkincil SQL enjeksiyonu, kötü amaçlı kodlar içeren değerlerin gönderilir gönderilmez yürütülmeyip, bir süre tutulduğu zaman oluşur. Uygulama SQL ifadesini doğru şekilde encode edip, geçerli SQL ifadesi olarak depo edebilir. Sonrasında SQL enjeksiyonuna karşı denetimsiz olan uygulamanın başka bir kısmında, depolanan SQL ifadesi çalıştırılır. Bu saldırıyı gerçeklemek için saldırganın, gönderilen değerlerin daha sonra nasıl kullanıldığına dair daha fazla bilgiye sahip olması gerekir. Otomatik web uygulaması güvenlik tarayıcıları, bu tür bir SQL enjeksiyonunu kolaylıkla algılayamaz. Dolayısıyla kötü niyetli yazılımların kodun handi kısmında olduğu manuel olarak kontrol edilmelidir.​

# **3-Saldırı**

​Saldırıyı gerçekleştirmek için, SQL Injection saldırılarını test etmek için kurulan  (https://issauga.lt/login-1/index.php) sitesini kullanacağız.​

​

## **3.1-Çıkış (Escape) Karakterlerinin Yanlış Filtrelenmesi Saldırısı​**

Saldırgan, şifre alanına ```SELECT * FROM kullanicilar WHERE kullaniciAdi = '' OR '1'='1' -- ';``` kodunu enjekte eder. Bu kod, SQL sorgusunu manipüle ederek, kullaniciAdi değeri ne olursa olsun 'OR '1'='1'' ifadesi her zaman doğru olduğu için, veritabanındaki tüm kullanıcı kayıtlarını döndürür. "--" ifadesi ise sorgunun geri kalan kısmını yorum satırı haline getirir, böylece orijinal sorgunun devamı devre dışı bırakılır. Sonuç olarak, saldırgan, veritabanında bulunan tüm kullanıcı bilgilerine erişebilir. Bu, saldırganın sistemdeki tüm kullanıcıların hassas bilgilerine ulaşmasına ve bunları kötüye kullanmasına olanak tanır, ciddi veri ihlallerine ve gizlilik ihlallerine yol açabilir.​

​
## **3.2-SQLMap Nedir ve Ne İşe Yarar?​**

**SQLMap Tanımı​**

SQLMap, açık kaynak kodlu bir otomatik SQL injection ve veritabanı alma aracıdır. Python programlama dili ile yazılmış olan bu araç, web uygulamalarındaki SQL injection güvenlik açıklarını tespit etmek ve bu açıklardan yararlanarak veritabanına yetkisiz erişim sağlamak amacıyla kullanılır. SQLMap, geniş bir yelpazede SQL injection türlerini destekler ve veritabanı yönetim sistemlerine karşı kapsamlı saldırılar gerçekleştirebilir.​
​
**SQLMap'in İşlevleri​**

SQLMap, kullanıcıların web uygulamalarındaki güvenlik açıklarını keşfetmelerini ve bu açıkların kötüye kullanılabilirliğini test etmelerini sağlar. Araç, çeşitli veritabanı yönetim sistemlerini (DBMS) destekler, örneğin MySQL, PostgreSQL, Oracle, Microsoft SQL Server, ve daha birçok popüler DBMS. SQLMap, belirli bir URL veya POST isteği gibi hedefleri tarayarak SQL injection zafiyetlerini tespit eder ve bu zafiyetler üzerinden veritabanındaki tabloları, sütunları ve kayıtları çıkartabilir.​

**SQLMap'in Özellikleri​**

SQLMap, kullanıcılara geniş bir özellik seti sunar. Bunlar arasında veritabanı bilgilerini elde etme, veritabanı kullanıcılarını ve parolalarını keşfetme, veritabanı tablolarını ve sütunlarını listeleme, veritabanı kayıtlarını çıkartma ve hatta işletim sistemi komutları çalıştırma yetenekleri bulunur. Ayrıca, SQLMap, proxy desteği, çeşitli yükleme teknikleri, farklı kimlik doğrulama yöntemleri ve zamanlama seçenekleri gibi ileri düzey konfigürasyon imkanları da sağlar.​

**Güvenlik ve Kullanım Alanları​**

SQLMap, genellikle siber güvenlik uzmanları, penetrasyon testi uzmanları ve etik hackerlar tarafından kullanılır. Aracın asıl amacı, web uygulamalarındaki SQL injection zafiyetlerini tespit ederek bu açıkları kapatmak ve sistemlerin daha güvenli hale gelmesini sağlamaktır.



### **3.3.1 Hedef URL Belirleme​**

Öncelikle, SQL injection zafiyetine sahip olduğunu düşündüğümüz hedef URL'yi belirliyoruz. Örneğin, aşağıdaki URL'yi hedef alalım: ​

(http://testphp.vulnweb.com/login.php?id=1​)

​

### **3.3.2 SQLMap Kullanarak Tarama​**

SQLMap'i kullanarak hedef URL'yi tarıyoruz. Bu işlem, URL'deki SQL injection güvenlik açıklarını tespit etmek için gerçekleştirilir. Komutu çalıştırıyoruz:​

```sqlmap http://testphp.vulnweb.com/login.php?id=1 -u  --batch​```

​

```--batch``` seçeneği, SQLMap'in kullanıcıdan onay istemeden otomatik olarak devam etmesini sağlar.​

### **3.3.3 Veritabanı Bilgilerini Elde Etme​**

Tespit edilen SQL injection açığını kullanarak veritabanı bilgilerini elde ediyoruz:​

```sqlmap -u "http://hedefsite.com/urunler.php?id=1" --dbs​```

Bu komut, hedef veritabanında mevcut olan tüm veritabanı isimlerini listeler.​

​

### 3.3.4 Hedef Veritabanını Seçme​

Elde edilen veritabanı isimlerinden birini seçiyoruz ve bu veritabanındaki tabloları listeliyoruz. Örneğin, hedef_db adında bir veritabanı seçelim:​

```sqlmap -u "http://hedefsite.com/urunler.php?id=1" -D hedef_db --tables​```

Bu komut, hedef_db veritabanındaki tüm tabloları listeler.​

### 3.3.5 Tablolardaki Sütunları Listeleme​

Seçilen tablodaki sütunları listeleyerek hassas bilgilerin olduğu sütunları belirliyoruz. Örneğin, kullanicilar adlı bir tabloyu hedef alalım:​

```sqlmap -u "http://hedefsite.com/urunler.php?id=1" -D hedef_db -T kullanicilar –columns​```

Bu komut, kullanicilar tablosundaki tüm sütunları listeler.​

### 3.3.6 Hassas Verileri Çıkartma​

Son olarak, belirlenen sütunlardan hassas verileri çıkartıyoruz. Örneğin, kullaniciAdi ve sifre sütunlarını hedef alalım:​

```sqlmap -u "http://hedefsite.com/urunler.php?id=1" -D hedef_db -T kullanicilar -C kullaniciAdi,sifre –dump​```

Bu komut, kullanicilar tablosundaki kullaniciAdi ve sifre sütunlarının tüm verilerini çıkarır.​

# 4-Tarihteki Saldırılar​

## 2008 Heartland Payment Systems İhlali​

Heartland Payment Systems, ABD'deki en büyük ödeme işlemcilerinden biriydi. 2008 yılında SQL injection yoluyla gerçekleştirilen bir saldırıda, yaklaşık 130 milyon kredi kartı bilgisi çalındı. Bu saldırı, Albert Gonzalez ve ekibi tarafından gerçekleştirildi ve tarihin en büyük veri ihlallerinden biri olarak kaydedildi.​

## 2008 HBGary Federal Saldırısı​

2011 yılında HBGary Federal adlı siber güvenlik firmasına yönelik bir SQL injection saldırısı gerçekleştirildi. Anonymous adlı hacktivist grup, HBGary Federal'in CEO'su Aaron Barr'ın şirketin web sitesine yaptığı zayıf güvenlik önlemlerini kullanarak veritabanına erişti ve binlerce e-posta ve diğer hassas bilgileri sızdırdı.​

## 2012 Yahoo! Veri İhlali​

Yahoo!'nun 2012'de yaşadığı veri ihlali, SQL injection yoluyla gerçekleştirilen büyük bir saldırıydı. Bu saldırıda, Yahoo'nun kullanıcı kimlik bilgileri çalındı. Yaklaşık 450.000 hesap bilgisi, zayıf güvenlik önlemleri ve SQL injection açıkları nedeniyle ele geçirildi.​

## Sony Pictures Saldırısı 2014​

2014 yılında Sony Pictures, Guardians of Peace (GOP) adlı bir hacker grubu tarafından saldırıya uğradı. Bu saldırıda SQL injection teknikleri kullanılarak şirketin veritabanına erişildi ve büyük miktarda veri çalındı. Çalınan veriler arasında şirket içi e-postalar, çalışan bilgileri ve henüz yayınlanmamış filmler yer alıyordu.​

## TalkTalk Veri İhlali 2015​

2015 yılında İngiltere merkezli telekomünikasyon şirketi TalkTalk, SQL injection saldırısına maruz kaldı. Bu saldırıda, yaklaşık 157.000 müşterinin kişisel bilgileri çalındı. Saldırganlar, şirketin web sitesindeki bir güvenlik açığını kullanarak bu saldırıyı gerçekleştirdi.​

# ​5-Saldırılara Karşı Önlemler

## 1.Parametrik Sorgular Kullanma​

SQL injection saldırılarına karşı en etkili koruma yöntemlerinden biri parametrik sorgular kullanmaktır. Parametrik sorgular, kullanıcı girdilerini SQL komutlarından ayırarak veritabanı sorgularının güvenli bir şekilde çalıştırılmasını sağlar. Bu yöntem, kullanıcı girdilerinin doğrudan sorguya eklenmesini engeller ve saldırganların kötü niyetli SQL kodları enjekte etmesini zorlaştırır. Örneğin, PHP'de hazırlıklı ifadeler (prepared statements) kullanarak parametrik sorgular oluşturulabilir.​

## 2. Kullanıcı Girdilerini Doğrulama ve Temizleme​

Tüm kullanıcı girdileri, veritabanı sorgularına dahil edilmeden önce titizlikle doğrulanmalı ve temizlenmelidir. Bu, kullanıcıların yalnızca beklenen veri türlerini girmelerini sağlamak ve zararlı kodların girişine engel olmak için yapılır. Örneğin, sayısal bir değerin beklenildiği bir alana yalnızca sayısal girişlerin kabul edilmesi sağlanabilir. Ayrıca, özel karakterlerin ve SQL komutlarının filtrelenmesi de güvenlik açısından önemlidir.​

## 3. Veritabanı Yetkilendirmesi ve Ayrıcalıkları​

Veritabanı kullanıcılarına minimum gerekli yetkilerin verilmesi, SQL injection saldırılarının etkisini azaltabilir. Uygulama, veritabanı ile etkileşime girerken yalnızca gerekli işlemleri gerçekleştirebilecek sınırlı yetkilere sahip olmalıdır. Ayrıca, veritabanı kullanıcılarının ayrıcalıkları düzenli olarak gözden geçirilmeli ve gerektiğinde güncellenmelidir. Bu, saldırganların veritabanına erişim sağlaması durumunda hasarın en aza indirilmesine yardımcı olur.​

## 4. Hata Mesajlarını Sınırlama​

Hata mesajları, saldırganlara sistem hakkında bilgi sağlayabilir. Bu nedenle, veritabanı hatalarını ve diğer kritik hata mesajlarını sınırlamak önemlidir. Kullanıcıya gösterilen hata mesajları, sistemin iç yapısı hakkında bilgi vermemeli ve yalnızca genel bilgiler içermelidir. Detaylı hata mesajları ise yalnızca geliştirme ve test ortamlarında kullanılmalı, üretim ortamında gizlenmelidir.​

## 5. Güvenlik Duvarları ve Güvenlik Yazılımları​

Web uygulama güvenlik duvarları (WAF) ve diğer güvenlik yazılımları, SQL injection saldırılarına karşı ek bir koruma katmanı sağlar. WAF, web trafiğini izleyerek ve analiz ederek zararlı istekleri engeller. Bu tür güvenlik çözümleri, bilinen saldırı imzalarını ve anormal davranışları tespit ederek saldırılara karşı proaktif savunma sağlar.​

​

​

​

​

​
​

​

​

​

​
​



​

​

​

​

​

​

​
​
