1. Problem Statement
    Multimodal taşımacılıkta hangi problemi çözüyoruz? Tek bir taşıma türü neden her zaman en iyi seçenek olmayabilir? 
C: Multimodal taşımacılıkta, toplam harcama maliyetini minimize etmek bunu yaparken karbon ayak izini ve süreyi de raporlayarak en verimli birleşik taşıma yöntemini , güzergahını bulmak temel amacımızdır. Opsiyonel olarak süre ve bütçe kısıtları uygulanabilir. Tek bir taşıma türü çoğu zaman en iyi seçenek olmayabilir çünkü  güzergah bulunmayabilit.  

2. Input (Girdiler)
    Sistem hangi bilgileri alacak? Örneğin başlangıç noktası, varış noktası, taşıma bağlantıları ve maliyetler.
C: Başlangıç konumunu , varış konumunu , taşıma için istenen süreyi (opsiyonel) , taşıma için bütçeyi (opsiyonel) isteyeceğiz. 

3. Output (Çıktılar)
    Sistem kullanıcıya ne sunacak? Örneğin önerilen rota, toplam maliyet ve tahmini süre.
C: Önerilen rotayı , toplam maliyeti ve tahmini süreyi sunacağız. Ayrıca rotanın hangi aşamalardan oluştuğunu, her aşamanın maliyetini ve süresini, hangi taşıma modlarının kullanıldığını ve her aşamanın karbon ayak izini sunacağız. 


4. Constraints (Kısıtlar)
    Hangi rotalar geçersiz sayılacak? Örneğin bağlantısı olmayan terminaller arasında doğrudan taşıma yapılamaması. 
C: Bağlantısı olmayan terminaller arasında doğrudan taşıma yapılamaması , iki nokta arasında hiçbir güzergah bulunmaması , bütçeyi aşması ve teslimat süresini aşması gibi durumlar en önemli kısıtlardır.

5. Assumptions (Varsayımlar)
    İlk sürümde hangi kabulleri yapıyoruz? Örneğin maliyetlerin önceden bilindiğini ve sabit olduğunu varsaymak. 
C: Maliyetler belirli sahte veriler(gerçek piyasa verileri olmamalı, negatif değerler içermemeli.) üzerinden hesaplacaktır.

6. Success Criteria (Başarı ölçütleri)
    Projenin doğru çalıştığını nasıl kanıtlayacağız? Örneğin geçersiz rotaları reddetmek, mümkün olan en düşük maliyetli rotayı bulmak ve sonuçları testlerle doğrulamak.
C: Testlerde doğru maliyeti önceden bildiğimiz küçük ağlar oluşturmalı ve algoritmanın sonucu bu değerle karşılaştırmalıyız.
