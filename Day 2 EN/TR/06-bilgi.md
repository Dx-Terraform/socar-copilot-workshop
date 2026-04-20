## 🍿 Atıştırmalık Bilgi: Neden Faz 5'ü Ayrı Bir Topic Olarak Oluşturuyoruz?

Şu ana kadar tüm adımları tek bir topic içinde yazdık. Faz 5'ten itibaren bu yaklaşım değişiyor:
yeni içeriği **ayrı bir topic** olarak oluşturacak, ardından ana akışımızın birden fazla noktasından
o topic'e bağlacağız. Bunun arkasında hem sezgisel hem de endüstri kökenli bir mantık var.

### Endüstrideki Karşılığı

Yazılım geliştirmede bu prensibin adı **DRY — Don't Repeat Yourself** (Kendini Tekrar Etme).
Profesyonel ekiplerin kodlarını organize ederken uyguladıkları en temel ilkelerden biridir ve üç
somut zaman kazancına dayanır:

**Tek noktadan güncelleme**

Aynı mantığı birden fazla yere kopyaladığınızda, her güncelleme için o kopyaların hepsini
değiştirmeniz gerekir. Birini atlamak yeterlidir — sistemin bir kolu eski davranışı sürdürmeye
devam eder. Mantığı tek bir yerde tutmak bu riski ortadan kaldırır.

**Okunabilirlik**

Bir akışı ya da kodu okurken bir blok görüyorsunuz ve onun adı "Anket Akışı". İçine girip 40
node'u tek tek incelemenize gerek kalmıyor; ne yaptığını zaten biliyorsunuz. Aynı node'ları
her iki yere gömseydiniz, ana akışın canvas'ı katlanarak büyür ve nerede ne olduğunu takip etmek
güçleşir.

**Yeniden kullanılabilirlik**

Bağımsız bir topic olarak tanımlanan anket akışı, sadece bu agent'a ait değildir. Başka bir topic,
başka bir agent ya da tamamen farklı bir proje de aynı akışa ihtiyaç duyduğunda onu yeniden
yazmak yerine doğrudan referanslar. Ekip büyüdükçe ya da yeni gereksinimler geldikçe bu esneklik
ciddi zaman tasarrufu sağlar.

---

### Bizim Durumumuzda Ne Oluyor?

Faz 5'te oluşturulacak anket akışı, ana topic'in **iki farklı noktasından** çağrılacak:

- Kullanıcı kriterleri ilk seferinde onayladıktan sonra (Faz 3, "Uygun, devam et" dalı)
- Kullanıcı revizyon yaptıktan sonra (Faz 4 sonrası)

Her iki durumda da aynı anket akışı çalışacak. Onu ayrı bir topic olarak tanımladığımız için
her iki noktaya yalnızca bir referans bağlamak yeterli olacak.

---

> 💡 **Özet:** Bir akışı birden fazla yerde kullanacaksanız onu ayrı bir topic olarak tanımlayın
> ve ihtiyaç duyulan yerlerden referanslayın. Bu yaklaşım hem Copilot Studio'da hem de
> yazılım geliştirmenin genelinde değişim maliyetini düşürür, okunabilirliği artırır ve
> yeniden kullanımı mümkün kılar.

---
