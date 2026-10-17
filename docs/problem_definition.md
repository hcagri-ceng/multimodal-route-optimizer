# Problem Definition — Multimodal Route Optimizer

## 1. Problem Statement

Sistemin temel amacı, taşıma ağındaki bağlantıları kullanarak toplam maliyeti en düşük olan güzergâhı bulmaktır. Kullanıcı isteğe bağlı olarak azami bütçe ve azami teslimat süresi belirleyebilir. Bu kısıtlar sağlanırken en düşük maliyetli rota seçilir.

Kullanıcı azami teslimat süresi belirtmezse sistem, bütçe kısıtını sağlamak koşuluyla en düşük maliyetli rotayı seçer ve toplam tahmini süreyi raporlar. Karbon emisyonu hesaplanır ve kullanıcıya sunulur; ancak ilk sürümde optimizasyon hedefi değildir.

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
- Azami teslimat süresi belirtilmediğinde sistemin en düşük maliyetli rotayı seçtiğinin testlerle doğrulanması.
