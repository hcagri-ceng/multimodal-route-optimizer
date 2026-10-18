# Algoritma Tasarımı — Multimodal Rota Optimizasyonu

## 1. Genel Bakış

Multimodal Rota Optimizasyonu sistemi, yönlü bir taşımacılık ağı üzerinde başlangıç düğümü ile hedef düğüm arasındaki en düşük maliyetli rotayı bulmayı amaçlar.

Taşımacılık ağı; kara yolu, demir yolu, deniz yolu ve hava yolu gibi farklı taşıma türlerini içerebilir.

Algoritma, isteğe bağlı bütçe ve teslimat süresi kısıtlarını sağlayan rotalar arasından toplam maliyeti en düşük olanı seçer. Toplam süre ve karbon emisyonu hesaplanarak raporlanır. İlk sürümde karbon emisyonu optimizasyon hedefi değildir.

İlk sürümde önceden tanımlanmış sentetik veriler kullanılacaktır. Bağlantıların maliyet, süre ve emisyon değerleri rota hesaplaması sırasında sabit kabul edilir.

## 2. Graf Modeli

Taşımacılık ağı, yönlü ve ağırlıklı bir graf olarak modellenir:

**G = (V, E)**

- `V`: Şehirleri, terminalleri, limanları, havaalanlarını veya aktarma noktalarını temsil eden düğüm kümesi.
- `E`: Düğümler arasındaki yönlü taşımacılık bağlantılarının kümesi.

Her bağlantı aşağıdaki bilgileri içerir:

- `source`: Başlangıç düğümü.
- `destination`: Varış düğümü.
- `transport_mode`: Kara yolu, demir yolu, deniz yolu veya hava yolu gibi taşıma türü.
- `cost`: Taşıma maliyeti.
- `duration`: Tahmini taşıma süresi.
- `emissions`: Tahmini karbon emisyonu.

Tüm bağlantıların maliyet, süre ve emisyon değerleri negatif olmayan sayısal değerler olmalıdır.

A düğümünden B düğümüne bir bağlantı bulunması, B düğümünden A düğümüne de bağlantı bulunduğu anlamına gelmez. Ters yöndeki bağlantılar ayrı kenarlar olarak tanımlanmalıdır.

## 3. Optimizasyon Hedefi

Temel hedef, toplam taşıma maliyetini minimize etmektir:

`minimize total_cost(route)`

Bu hedef aşağıdaki koşullara tabidir:

1. Rota, istenen başlangıç düğümünden başlamalı ve hedef düğümde sonlanmalıdır.
2. Ardışık her düğüm çifti arasında tanımlanmış bir yönlü bağlantı bulunmalıdır.
3. Azami bütçe belirtilmişse toplam maliyet bu bütçeyi aşmamalıdır.
4. Azami teslimat süresi belirtilmişse toplam süre bu sınırı aşmamalıdır.

Bütçe ve teslimat süresi kısıtları isteğe bağlıdır.

Kullanıcı azami teslimat süresi belirtmezse algoritma, bütçe kısıtını sağlamak koşuluyla en düşük maliyetli rotayı seçer ve toplam tahmini süreyi raporlar.

Karbon emisyonu hesaplanır ve raporlanır; ancak ilk sürümde rota seçimini etkilemez.

Birden fazla rota aynı minimum maliyete sahipse algoritma bunlardan herhangi birini döndürebilir. Gerekirse ileride tutarlı bir eşitlik bozma kuralı eklenebilir.

## 4. Algoritma Seçimi

Her düğüm için yalnızca tek bir en iyi maliyet değeri saklayan standart bir Dijkstra uygulaması, bu problem için yeterli değildir.

Bir ara düğüme ulaşan en ucuz rota, başka bir rotaya göre daha uzun sürebilir. En ucuz rota daha sonra azami teslimat süresini aşarken daha pahalı bir rota geçerliliğini koruyabilir.

Bu nedenle algoritmada **çok etiketli kısıtlı en kısa yol araması (multi-label constrained shortest-path search)** kullanılacaktır.

Her etiket (label), belirli bir düğüme ulaşan aday rotayı temsil eder ve aşağıdaki bilgileri içerir:

- `node`: Mevcut düğüm.
- `cost`: Birikmiş toplam maliyet.
- `duration`: Birikmiş toplam süre.
- `previous_label`: Rotanın oluşturulmasında kullanılan önceki etikete referans.
- `incoming_edge`: Mevcut düğüme ulaşmak için kullanılan bağlantı.

Etiketler, birikmiş maliyeti en düşük olan adayı önce işleyen bir **minimum öncelikli kuyrukta (min-priority queue)** tutulur.

Aynı düğüm için birden fazla baskın olmayan etiket saklanabilir.

## 5. Kısıtlar ve Baskınlık Yoluyla Eleme

### 5.1. Uygunluk kontrolleri

Bir aday etiket aşağıdaki durumlardan biri gerçekleşirse elenir:

- Birikmiş maliyeti belirtilen azami bütçeyi aşarsa.
- Birikmiş süresi belirtilen azami teslimat süresini aşarsa.

Bağlantı maliyetleri ve süreleri negatif olmadığı için yeni bağlantılar eklemek bu toplamları azaltamaz.

### 5.2. Baskınlık kuralı

Aynı düğüme ulaşan A ve B etiketlerini ele alalım.

A etiketi, aşağıdaki koşullar sağlanıyorsa B etiketine baskındır:

- `A.cost <= B.cost`
- `A.duration <= B.duration`
- Bu eşitsizliklerden en az biri kesin olarak sağlanır; yani ilgili değerlerden en az biri diğerinden küçüktür.

A, B'ye baskınsa B elenebilir.

Bunun nedeni, ortak düğümden itibaren kullanılabilecek her devam bağlantısının A rotasına da eklenebilmesidir. A hem daha pahalı değildir hem de daha uzun sürmez. Dolayısıyla mevcut optimizasyon hedefi ve kısıtları altında B daha iyi bir sonuç üretemez.

İlk sürümde karbon emisyonu yalnızca raporlandığı için baskınlık karşılaştırmasına dahil edilmez.

Bu kural, gelecekteki bağlantıların kullanılabilirliğinin ve değerlerinin önceki rota geçmişine değil, mevcut düğüme bağlı olduğu varsayımına dayanır. Önceki rotaya bağlı kısıtlar ilk sürümün kapsamı dışındadır.

## 6. Aramanın Sonlandırılması

Hedef düğüme ulaşmak, aramanın hemen sonlandırılması için yeterli değildir.

Algoritma, hedefe ulaşan en iyi uygun etiketi kaydeder ve daha düşük maliyetli bir rota üretebilecek adayları değerlendirmeye devam eder.

Öncelik kuyruğu etiketleri birikmiş maliyetlerine göre artan sırada işlediğinden ve bağlantı maliyetleri negatif olmadığından, aşağıdaki koşul sağlandığında arama güvenle sonlandırılabilir:

`minimum_queue_cost >= best_destination_cost`

Bu noktada kuyrukta kalan hiçbir aday, mevcut en iyi rotadan kesin olarak daha düşük maliyetli bir hedef rotası üretemez.

Uygun bir hedef etiketi bulunamamışsa ve kuyruk boşalmışsa uygun rota yoktur.

## 7. Sözde Kod (Pseudocode)

```text
FONKSIYON EnDusukMaliyetliRotayiBul(
    graf,
    baslangic,
    hedef,
    istege_bagli_azami_butce,
    istege_bagli_azami_sure
):
    BASLANGIC ve HEDEF dugumlerini dogrula
    Graf baglanti degerlerini dogrula
    Istege bagli kisitlari dogrula

    EGER baslangic hedefe esitse:
        SIFIR maliyetli, sureli ve emisyonlu bos rota dondur

    baslangic_etiketi = {
        node: baslangic,
        cost: 0,
        duration: 0,
        previous_label: YOK,
        incoming_edge: YOK
    }

    oncelik_kuyrugu = bos minimum oncelikli kuyruk
    dugum_etiketleri = her dugum icin aktif etiket listesi
    en_iyi_hedef = YOK

    baslangic_etiketini oncelik_kuyruguna ekle
    baslangic_etiketini dugum_etiketleri[baslangic] listesine ekle

    ONCELIK_KUYRUGU bos degilken:

        mevcut = KUYRUKTAN_EN_DUSUK_MALIYETLI_ETIKETI_CIKAR()

        EGER mevcut artik aktif degilse:
            DEVAM ET

        EGER en_iyi_hedef varsa VE
           mevcut.cost >= en_iyi_hedef.cost ise:
            DONGUYU BITIR

        mevcut dugumden cikan HER baglanti icin:

            yeni_maliyet = mevcut.cost + baglanti.cost
            yeni_sure = mevcut.duration + baglanti.duration

            EGER azami butce belirtilmis VE
               yeni_maliyet > azami butce ise:
                DEVAM ET

            EGER azami sure belirtilmis VE
               yeni_sure > azami sure ise:
                DEVAM ET

            aday = {
                node: baglanti.destination,
                cost: yeni_maliyet,
                duration: yeni_sure,
                previous_label: mevcut,
                incoming_edge: baglanti
            }

            EGER aday dugumundeki aktif etiketlerden biri
               adaya baskinsa VEYA
               ayni maliyet ve sureye sahipse:
                ADAYI ELE
                DEVAM ET

            aday dugumundeki HER mevcut_etiket icin:
                EGER aday mevcut_etikete baskinsa:
                    mevcut_etiketi AKTIF DEGIL olarak isaretle

            adayi dugum_etiketlerine ekle
            adayi oncelik_kuyruguna ekle

            EGER aday.node hedefe esitse:
                EGER en_iyi_hedef YOKSA VEYA
                   aday.cost < en_iyi_hedef.cost ise:
                    en_iyi_hedef = aday

    EGER en_iyi_hedef YOKSA:
        UYGUN_ROTA_YOK sonucunu dondur

    rota = ROTAYI_GERIYE_DOGRU_OLUSTUR(en_iyi_hedef)

    toplam_maliyet = rota baglantilarinin maliyetleri toplami
    toplam_sure = rota baglantilarinin sureleri toplami
    toplam_emisyon = rota baglantilarinin emisyonlari toplami

    rota, toplam_maliyet, toplam_sure ve toplam_emisyon dondur
```

## 8. Rotanın Geriye Doğru Oluşturulması

Her etiket, kendisinden önceki etikete ve kullanılan bağlantıya referans tutar.

En iyi hedef etiketi bulunduğunda algoritma, başlangıç etiketine ulaşana kadar bu referansları geriye doğru takip eder.

Elde edilen sıra ters çevrilerek başlangıçtan hedefe doğru rota oluşturulur.

Nihai rota çıktısı aşağıdaki bilgileri içerir:

- Sıralı rota düğümleri ve bağlantıları.
- Her bağlantıda kullanılan taşıma türü.
- Her bağlantının maliyeti, süresi ve emisyonu.
- Toplam maliyet, süre ve karbon emisyonu.

## 9. Girdi Doğrulama ve Hata Durumları

Algoritma aşağıdaki durumları ele almalıdır:

- Başlangıç veya hedef düğümün bulunmaması.
- Negatif bağlantı maliyeti, süresi veya emisyonu.
- Negatif bütçe veya teslimat süresi kısıtı.
- Başlangıç ve hedefin aynı düğüm olması.
- Başlangıç ile hedef arasında hiçbir yol bulunmaması.
- Yollar bulunmasına rağmen hiçbirinin verilen kısıtları karşılamaması.

Geçersiz girdiler, rota araması başlamadan önce reddedilmelidir.

İstek geçerli olduğu hâlde uygun rota bulunamaması `UYGUN_ROTA_YOK` sonucu ile bildirilir. Bu durum, beklenmeyen bir algoritma hatası değil, geçerli bir iş sonucudur.

## 10. Test Stratejisi

Algoritma, beklenen sonuçları önceden bilinen küçük sentetik graf yapılarıyla doğrulanacaktır.

Gerekli test senaryoları:

1. Tek bir doğrudan bağlantının en düşük maliyetli rota olması.
2. Multimodal bir rotanın mevcut doğrudan rotadan daha ucuz olması.
3. En ucuz ara etiketin süre sınırını aşması, ancak daha pahalı bir etiketin uygun kalması.
4. Bütçe kısıtının, aksi durumda seçilecek en ucuz rotayı elemesi.
5. Süre kısıtının azami süreyi aşan rotaları elemesi.
6. Hiçbir uygun rotanın bulunmaması.
7. Başlangıç ve hedef düğümün aynı olması.
8. Baskın bir etiketin, en iyi sonucu değiştirmeden diğer etiketi elemesi.
9. Bütçe veya süre sınırını aşan bir adayın genişletilmemesi.
10. Toplam maliyet, süre ve emisyonun seçilen rotadaki bağlantıların toplamına eşit olması.

## 11. İlk Sürümün Sınırlamaları

- Bağlantı değerleri sabittir ve sentetik verilerden oluşur.
- Canlı trafik, gecikmeler, bakım ve geçici bağlantı kapanmaları modellenmez.
- Karbon emisyonu optimizasyon hedefi değildir.
- Çok etiketli arama, en kötü durumda çok sayıda etiket saklamak zorunda kalabilir. Optimizasyon veya eleme sınırları eklenmeden önce daha büyük sentetik graflarda performans değerlendirilecektir.
- Kapasite kısıtları, aktarma cezaları, sefer tarifeleri ve zamana bağlı maliyetler ilk sürümün kapsamı dışındadır.
