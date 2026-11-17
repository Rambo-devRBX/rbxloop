# RBX — Android Emülasyon ve Yüksek Performanslı Grafik Altyapısı

**RBX | Private Source Project**  
**Geliştirici:** RBX  
**Proje statüsü:** Aktif Ar-Ge ve yazılım geliştirme  
**Kurumsal yürütme:** RBX projesi anonim şirket tarafından yürütülmektedir.

---

## 1. RBX nedir?

RBX, Windows üzerinde çalışan, Android x86_64 tabanlı, yüksek performans ve düşük gecikme hedefleyen özel bir emülasyon platformudur.

RBX'in temel amacı, Android işletim sistemini sanal makine ortamında çalıştırırken bilgisayarın işlemci, bellek ve ekran kartı kaynaklarından mümkün olduğunca verimli yararlanmaktır.

RBX yalnızca Android'i açan bir sanal makine değildir. Grafik işleme, ekran kompozisyonu, giriş gecikmesi, kare süreleri, sanal GPU iletişimi ve uygulama uyumluluğu gibi farklı sistem katmanlarının birlikte optimize edilmesini hedefleyen kapsamlı bir yazılım projesidir.

RBX'in geliştirilmesi anonim şirket tarafından yürütülmektedir. RBX'in kaynak kodu, özel geliştirme araçları, test altyapısı ve üretim yapılandırmaları kapalı kaynak olarak korunmaktadır.

## 2. RBX'in temel hedefleri

RBX'in geliştirme hedefleri şunlardır:

- Windows üzerinde Android x86_64 işletim sistemini çalıştırmak.
- QEMU sanallaştırma altyapısını performans odaklı yapılandırmak.
- NVIDIA ekran kartlarından grafik hızlandırması elde etmek.
- Android ile Windows arasındaki grafik iletişim zincirini optimize etmek.
- SurfaceFlinger ve Hardware Composer performansını geliştirmek.
- Gerçek ekran yenileme hızını ve uygulama FPS değerlerini ölçmek.
- Kare sürelerini ve giriş gecikmesini azaltmak.
- Klavye, fare ve dokunmatik girişlerini Android uygulamalarıyla uyumlu hâle getirmek.
- İlerleyen aşamalarda mobil oyun uyumluluğunu ve kontrol eşleme sistemini geliştirmek.

RBX'in uzun vadeli performans hedeflerinden biri 144 Hz ekran yenileme ve mümkün olan en yüksek gerçek uygulama FPS değerlerine ulaşmaktır. Bu hedef henüz doğrulanmış bir 144 FPS sonucu olarak sunulmamaktadır.

## 3. RBX'in teknik mimarisi

RBX'in mevcut geliştirme altyapısı aşağıdaki bileşenlerden oluşmaktadır:

**Host işletim sistemi:** Windows  
**Sanal makine altyapısı:** QEMU  
**Sanallaştırma:** WHPX  
**Guest işletim sistemi:** BlissOS / Android x86_64  
**Grafik aygıtı:** VirtIO-GPU  
**3B grafik aktarımı:** VirGL / virglrenderer  
**Windows grafik katmanı:** ANGLE  
**Grafik API aktarımı:** EGL / OpenGL ES / D3D11  
**GPU:** NVIDIA GeForce RTX 5060

Grafik iletişim zinciri:

Windows NVIDIA sürücüsü → D3D11 → ANGLE → QEMU EGL → virglrenderer → VirtIO-GPU → Android Guest Mesa → SurfaceFlinger.

Bu mimari sayesinde Android'in grafik komutları sanal GPU üzerinden Windows tarafındaki grafik altyapısına aktarılabilmektedir.

RBX'in amacı bu zincirin yalnızca çalışmasını sağlamak değil, performans kayıplarını ölçerek azaltmaktır.

Bu grafik altyapısının entegrasyonu anonim şirket tarafından geliştirilmektedir.

## 4. RBX kapsamında bugüne kadar yapılan çalışmalar

### 4.1 Android ve QEMU altyapısı

RBX kapsamında Android x86_64 sistemi QEMU üzerinde çalıştırılmıştır.

- QEMU sanallaştırma altyapısı yapılandırıldı.
- Android kernel ve initrd çalışma düzeni incelendi.
- VirtIO-GPU aygıtı test edildi.
- Windows WHPX sanallaştırma altyapısı kullanıldı.
- Test ve üretim diskleri birbirinden ayrıldı.
- Üretim dosyalarını koruyan ayrı test overlay sistemi oluşturuldu.

### 4.2 SwiftShader performans araştırması

RBX'in ilk grafik performansı araştırmalarında SwiftShader yazılım tabanlı grafik altyapısı incelendi.

Bu kapsamda:

- FP16 texture format uyuşmazlığı araştırıldı.
- AndroidNativeImage bellek kilitleme davranışı incelendi.
- Texture dönüşüm maliyetleri analiz edildi.
- Gereksiz grafik işlemleri ve tekrar çizim davranışları araştırıldı.
- SwiftShader üzerinde özel performans iyileştirmeleri geliştirildi.
- Test derlemeleri ve doğrulama ISO'ları oluşturuldu.

Wallpaper üretim çözünürlüğünün 3840×2160 değerinden 1920×1080 değerine düşürüldüğü ayrı bir testte ana ekran etkileşim performansında yaklaşık %95 iyileşme ölçüldü. Bu sonuç test ortamına aittir; üretim sistemine uygulanmış kalıcı bir değişiklik olarak değerlendirilmemektedir.

### 4.3 Android grafik ve SurfaceFlinger araştırmaları

RBX kapsamında Android'in ekran kompozisyon sistemi ayrıntılı şekilde incelendi.

Araştırılan alanlar:

- SurfaceFlinger çalışma mimarisi.
- Hardware Composer davranışı.
- CLIENT ve DEVICE composition ayrımı.
- RenderEngine ve Skia grafik altyapısı.
- SwiftShader grafik maliyetleri.
- Texture işleme ve bellek bant genişliği.
- Frame pacing ve VSYNC zamanlaması.
- Ekran katmanlarının çizim maliyetleri.

RBX'in hedefi grafik işleme zincirindeki darboğazları ölçülebilir hâle getirerek sistemin toplam kare üretim kapasitesini artırmaktır.

### 4.4 Mesa ve alternatif grafik altyapıları

RBX kapsamında Mesa'nın farklı çalışma modları araştırıldı.

- Mesa EGL ve GLES yüklenmesi test edildi.
- KMS_SWRAST ve yazılım render seçenekleri incelendi.
- Mesa 23.3.3 kaynak kodu araştırıldı.
- minigbm_arcvm ve minigbm_gbm_mesa karşılaştırmaları yapıldı.
- `LIBGL_ALWAYS_SOFTWARE` davranışı incelendi.
- SurfaceFlinger ve uygulama grafik süreçlerinin farklı render yollarına düşmesi araştırıldı.

Yazılım tabanlı Mesa yapılandırmasıyla Android'in açıldığı görüldü; ancak performans sonuçları hedeflenen seviyeyi karşılamadı.

### 4.5 ANGLE ve Windows GPU entegrasyonu

RBX'in önemli aşamalarından biri Windows üzerinde ANGLE/EGL entegrasyonunun çalışır hâle getirilmesidir.

Bu süreçte:

- Windows QEMU grafik altyapısı incelendi.
- WGL ve EGL arasındaki farklar araştırıldı.
- ANGLE DLL bağımlılıkları analiz edildi.
- Uygun x64 EGL çalışma zamanı bulundu.
- ANGLE'nin gerekli EGL dışa aktarımları kontrol edildi.
- QEMU'nun ANGLE fallback EGL yolunu başlattığı doğrulandı.
- NVIDIA sürücüsünün QEMU sürecinde kullanıldığı gözlendi.

NVIDIA RTX 5060 üzerinde OpenGL 4.6 WGL testi ayrıca başarıyla gerçekleştirildi.

ANGLE/EGL entegrasyonunun çalışması RBX için önemli bir altyapı aşamasıdır; ancak tek başına 144 FPS veya bütün grafik işlemlerinin donanım üzerinde hızlandırıldığı anlamına gelmez.

### 4.6 VirGL ve Capset araştırması

RBX'in en önemli teknik araştırmalarından biri QEMU ile virglrenderer arasındaki capset iletişiminin incelenmesidir.

Başlangıçta Android tarafında şu hata gözlemlendi:

`timed out waiting for cap set 0`

Araştırma sırasında:

- QEMU capset işleme kodu incelendi.
- virglrenderer kaynak kodu analiz edildi.
- `virgl_renderer_get_cap_set` ve `virgl_renderer_fill_caps` fonksiyonları araştırıldı.
- Windows EGL başlatma hatası belirlendi.
- ANGLE çalışma zamanı uyumsuzlukları tespit edildi.
- Uygun EGL runtime bulundu ve QEMU ile test edildi.

Sonuç olarak A205 testinde:

- VirtIO-GPU algılandı.
- `+virgl` özelliği doğrulandı.
- GET_CAPSET_INFO aşaması başarılı oldu.
- Önceki testlerde görülen capset 0 timeout hatası gözlenmedi.
- SurfaceFlinger SIGABRT hatası gözlenmedi.

Önemli not: Capset timeout hatasının ortadan kalkması olumlu bir sonuçtur; ancak pozitif capset veri yanıtı ayrıca yakalanmış değildir.

### 4.7 Android boot sürecinin tamamlanması

RBX'in A206 testinde Android işletim sistemi watchdog kullanılmadan çalışan QEMU yapılandırmasında başarıyla açıldı.

Doğrulanan sonuçlar:

- Linux kernel boot: PASS.
- VirtIO-GPU + VirGL: PASS.
- GET_CAPSET_INFO: PASS.
- Capset timeout: gözlenmedi.
- SurfaceFlinger: çalışır durumda.
- `boot_completed=1`: PASS.
- Android boot tamamlanma zamanı: yaklaşık 493,5 saniye.

A206 sırasında SurfaceFlinger servisinin başlatılmasına ilişkin tekrarlayan init mesajları incelendi. Önceki 240 saniyelik test penceresinin Android'in açılması için yeterli olmadığı anlaşıldı.

Bu aşama RBX'in Windows üzerinde ANGLE/EGL ve VirGL kullanan Android sistemini tam olarak başlatabildiğini göstermektedir.

## 5. RBX'in şu an üzerinde çalıştığı konular

### 5.1 Gerçek FPS ölçüm altyapısı

RBX'in mevcut geliştirme aşamasında en önemli konu Android içinden güvenilir performans verisi alabilmektir.

ADB bağlantısı ve Android tarafındaki ölçüm araçları henüz tamamlanmış değildir.

Planlanan ölçümler:

- Android ekran çözünürlüğü.
- Ekran yoğunluğu (DPI).
- Aktif Display Mode.
- Gerçek refresh rate.
- SurfaceFlinger kompozisyon yolu.
- Launcher FPS.
- Uygulama çekmecesi FPS.
- Settings FPS.
- Frame time p50, p90, p95 ve p99.
- Kare düşürme ve frame pacing davranışı.

RBX, host tarafındaki QEMU görüntüleme hızını Android uygulamalarının FPS değeri olarak kabul etmez. Ölçümlerin guest tarafından alınmasını esas alır.

### 5.2 GPU hızlandırmasının doğrulanması

RBX, RTX 5060'ın Android grafik işlemlerindeki gerçek rolünü belirlemeyi hedeflemektedir.

Bunun için:

- Guest GLES renderer bilgisi alınacak.
- SurfaceFlinger GLES ve EGL bilgileri incelenecek.
- VirGL üzerinden gelen grafik komutları analiz edilecek.
- Host GPU kullanımı ile guest grafik performansı karşılaştırılacak.
- Yazılım render ve donanım destekli render sonuçları ayrı tutulacak.

### 5.3 Grafik performansı ve düşük gecikme

RBX'in performans araştırmaları şu alanlarda devam etmektedir:

- RenderEngine optimizasyonu.
- SurfaceFlinger kompozisyon maliyeti.
- GPU ve CPU darboğazları.
- Texture aktarım maliyetleri.
- Bellek bant genişliği.
- Kare zamanlaması.
- Giriş gecikmesi.
- QEMU ve Android arasındaki görüntü aktarımı.

### 5.4 Mobil oyun uyumluluğu

RBX'in ilerleyen geliştirme hedeflerinden biri mobil oyunları Android ortamında çalıştırabilmektir.

Bu kapsamda:

- ARM uygulama uyumluluğu.
- x86_64 Android uygulama desteği.
- Klavye ve fare eşleme.
- Dokunmatik giriş simülasyonu.
- Oyun içi kamera kontrolü.
- Giriş gecikmesi.
- Oyun performansı ve kararlılığı.

PUBG Mobile gibi oyunlar için ayrıca ABI, ARM çeviri katmanı, grafik API uyumluluğu ve uygulama güvenliği araştırmaları gerekmektedir. Bu uyumluluk henüz tamamlanmış bir özellik olarak sunulmamaktadır.

## 6. RBX'in 144 Hz ve 144 FPS hedefi

RBX'in en önemli uzun vadeli hedeflerinden biri yüksek yenileme hızlı ekranlarla uyumlu bir Android emülasyon altyapısı oluşturmaktır.

Burada iki farklı değer birbirinden ayrılmaktadır:

**144 Hz:** Android'in gördüğü sanal ekranın yenileme hızı.

**144 FPS:** Android uygulamasının saniyede gerçekten ürettiği kare sayısı.

Bu iki değer aynı şey değildir.

RBX, 144 Hz ekran zamanlamasını ve gerçek uygulama FPS değerini ayrı ayrı doğrulamayı hedeflemektedir.

Mevcut araştırmalar virtio-gpu, VirGL, ANGLE ve Windows grafik altyapısının çeşitli sınırları olabileceğini göstermektedir. Bu nedenle RBX'in 144 FPS hedefi bir geliştirme hedefidir; henüz kanıtlanmış nihai performans sonucu değildir.

## 7. RBX'in geliştirme yaklaşımı

RBX geliştirme sürecinde ölçüm ve doğrulama esas alınmaktadır.

- Her teknik değişiklik ayrı test edilir.
- Üretim sistemi ile deneysel test sistemi ayrılır.
- Test sonuçları raporlanır.
- Dosya bütünlüğü kontrol edilir.
- Önceki test kayıtları korunur.
- Performans değerleri ölçülmeden başarı olarak sunulmaz.
- Grafik hataları kaynak kodu ve çalışma zamanı kanıtlarıyla araştırılır.

RBX'in bütün geliştirme, test ve araştırma faaliyetleri anonim şirket tarafından yürütülmektedir.

## 8. Projenin mevcut durumu

| Bileşen | Durum |
|---|---|
| Windows + QEMU altyapısı | Çalışıyor |
| WHPX sanallaştırma | Çalışıyor |
| Android x86_64 kernel boot | Başarılı |
| Android boot_completed | Başarılı |
| ANGLE/EGL entegrasyonu | Doğrulandı |
| NVIDIA host grafik yolu | Doğrulandı |
| VirtIO-GPU + VirGL | Çalışıyor |
| GET_CAPSET_INFO | Başarılı |
| Capset timeout hatası | Son testte gözlenmedi |
| SurfaceFlinger | Çalışıyor |
| ADB ölçüm altyapısı | Bekliyor |
| Gerçek Android refresh rate ölçümü | Bekliyor |
| Gerçek uygulama FPS ölçümü | Bekliyor |
| 144 FPS hedefi | Araştırma ve geliştirme aşamasında |
| PUBG Mobile uyumluluğu | Araştırma aşamasında |

## 9. RBX'in uzun vadeli vizyonu

RBX, Android uygulamalarını Windows üzerinde yüksek performansla çalıştırabilecek özel bir emülasyon platformuna dönüşmeyi hedeflemektedir.

RBX'in uzun vadeli geliştirme vizyonu:

1. Kararlı Android sanallaştırma altyapısı.
2. Donanım destekli grafik işleme.
3. Düşük gecikmeli görüntü aktarımı.
4. Yüksek yenileme hızlı sanal ekran.
5. Gerçek zamanlı FPS ve performans izleme.
6. Gelişmiş klavye-fare kontrol sistemi.
7. Mobil oyun uyumluluğu.
8. Uzun süreli kararlı çalışma.
9. Özel optimizasyon ve yönetim araçları.

**RBX, anonim şirket tarafından geliştirilen ve kaynak kodu kapalı tutulan özel bir yazılım projesidir.**

RBX'in amacı yalnızca bir Android sanal makinesi oluşturmak değil; grafik, sanallaştırma, giriş ve performans katmanlarını birlikte ele alan, ölçülebilir ve geliştirilebilir bir Android emülasyon platformu ortaya çıkarmaktır.

---

**RBX — Private Source. Proprietary Development.**

**RBX anonim şirket tarafından yürütülmektedir.**
