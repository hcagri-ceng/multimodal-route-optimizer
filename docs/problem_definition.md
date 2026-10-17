# Problem Definition — Multimodal Route Optimizer

## 1. Problem Statement

Multimodal taşımacılıkta temel amacımız, farklı taşıma türlerini (kara, demir yolu, deniz ve hava yolu) birleştirerek toplam taşıma maliyetini minimize eden güzergâhı bulmaktır.

Sistem, bulunan rotanın toplam süresini ve karbon emisyonunu da hesaplayıp raporlayacaktır. Kullanıcı isteğe bağlı olarak azami bütçe ve teslimat süresi kısıtları belirleyebilecektir. Bu durumda sistem, belirtilen kısıtları karşılayan en düşük maliyetli rotayı bulacaktır.

Tek bir taşıma türü her zaman en uygun seçenek olmayabilir. Bazı noktalar arasında doğrudan bağlantı bulunmayabilir veya farklı taşıma türlerini birleştirmek daha düşük maliyetli bir güzergâh sağlayabilir.

## 2. Inputs (Girdiler)

Sistem aşağıdaki bilgileri alacaktır:

- Başlangıç noktası
- Varış noktası
- İsteğe bağlı azami bütçe
- İsteğe bağlı azami teslimat süresi
- Taşıma ağına ait düğümler ve mevcut bağlantılar
- Her bağlantının taşıma türü, maliyeti, süresi ve karbon emisyonu

İlk sürümde taşıma ağı ve bağlantı bilgileri önceden tanımlanmış sentetik verilerden sağlanacaktır.

## 3. Outputs (Çıktılar)

Sistem aşağıdaki bilgileri sunacaktır:

- Önerilen güzergâh
- Rotadaki bağlantıların sıralaması
- Her aşamada kullanılan taşıma türü
- Her aşamanın maliyeti ve süresi
- Her aşamanın karbon emisyonu
- Toplam taşıma maliyeti
- Toplam tahmini süre
- Toplam karbon emisyonu

Uygun bir rota bulunamadığında sistem bu sonucu kullanıcıya açıkça bildirecektir.

## 4. Constraints (Kısıtlar)

- Yalnızca taşıma ağında tanımlanmış bağlantılar kullanılabilir.
- Toplam maliyet, kullanıcı tarafından belirtilen azami bütçeyi aşamaz.
- Toplam süre, kullanıcı tarafından belirtilen azami teslimat süresini aşamaz.
- Başlangıç ve varış noktaları geçerli olmalıdır.
- Başlangıç ile varış arasında kısıtları karşılayan bir güzergâh bulunamayabilir.

Bütçe ve süre sınırları isteğe bağlıdır. Kullanıcı bu sınırları belirtmezse ilgili kısıt uygulanmayacaktır.

## 5. Assumptions (Varsayımlar)

- İlk sürümde gerçek piyasa verileri yerine sentetik ve önceden tanımlanmış veriler kullanılacaktır.
- Bağlantıların maliyet, süre ve karbon emisyonu değerleri sabit ve negatif olmayan sayısal değerlerdir.
- Taşıma bağlantıları ilk sürümde mevcut veya mevcut değil şeklinde modellenir.
- Canlı fiyat değişimleri, gecikmeler, kapasite kısıtları ve gerçek zamanlı trafik ilk sürümün kapsamı dışındadır.
- Karbon emisyonu hesaplanacak ve raporlanacaktır; ilk sürümde optimizasyon hedefi olmayacaktır.

## 6. Success Criteria (Başarı Ölçütleri)

- Önceden beklenen sonucu bilinen küçük taşıma ağlarında algoritmanın doğru rotayı bulması.
- Bulunan rotanın toplam maliyet, süre ve emisyon değerlerinin doğru hesaplanması.
- Bütçe ve süre kısıtlarını aşan rotaların elenmesi.
- Uygun rota bulunmadığında sistemin doğru sonucu bildirmesi.
- Geçersiz girdilerin ve negatif maliyet, süre veya emisyon değerlerinin reddedilmesi.
- Sonuçların otomatik testlerle doğrulanması.
