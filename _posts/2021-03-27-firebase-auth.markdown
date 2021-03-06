---
layout: post
title: "Flutter Projesine Firebase'i Nasıl Entegre Ederiz?"
description: Firebase projesi oluşturup, Flutter projemize entegre ediyoruz.
tags: flutter firebase
---

# Flutter Projesine Firebase Nasıl Entegre Edilir?

Firebase uygulamanızı geliştirmenize, büyütmenizi ve inşa etmenize yarayan bir platformdur. Firebase içerisinde kullanıcıların işlerini kolaylaştırıp, ürünlerini büyütmeleri için birçok ürün(Cloud Firestore, Authentication, Cloud Messaging, vb.) sunmakta.

Firebase'i Go, Python, Java, Kotlin, vb. gibi birçok dil ile kullanma imkanına sahibiz. Yazımda ise Flutter projemize Firebase'i nasıl entegre edeceğimizden bahsedeceğim.

### Firebase'i Flutter Projesine Ekleme

#### Firebase Projesi Oluşturma

**[Firebase](https://firebase.google.com/)**'in sitesine girdiğinizde *Go to console* ile kendi konsol sayfamıza gidiyoruz.

![go_to_console](https://i.hizliresim.com/CfnEHD.png)

Konsol sayfasına geldiğiniz zaman sizi yeni proje ekleyebileceğiniz veya daha önceki projelerinize erişebileceğiniz bir sayfa karşılıyor. Bu sayfada *Add Project*'i seçip yeni bir Firebase projesi oluşturmaya başlıyoruz.

<img src="https://i.hizliresim.com/6NYcor.png" alt="add_project" style="zoom:67%;" />

Üç adımdan oluşan proje oluşturma aşamalarını tamamlamamız gerekiyor. İlk aşamada projemizin ismini belirliyoruz, ikinci aşamada projemiz için Google Analytics'in aktif olup olmayacağını belirliyoruz ve son aşamada eğer Google Analytics'i aktif ise Google Analytics hesabını seçiyoruz. Böylelikle üç adımı da tamamlayarak Firebase projemizi oluşturmuş oluyoruz.

#### Firebase Projesine Android Uygulaması Ekleme

Firebase projemizi oluşturduktan sonra projemizin ana ekranına yönlendiriliyoruz. Proje ana ekranının sol tarafındaki bölümde Firebase'in sunmuş olduğu ürünleri, geri kalan kısımda ise projemiz hakkında özet bilgilerin olduğu kısımı görüyoruz. 

<img src="https://i.hizliresim.com/wCNTdy.png" alt="firebase_home" style="zoom:67%;" />

Oluşturmuş olduğumuz Firebase projesine iOS, Android, Web ve Unity uygulamaları ekleyebiliyoruz. Şimdi Firebase projemize nasıl Android uygulaması ekleriz ona göz atalım. 

Android ikonunu seçerek Android uygulaması ekleme ekranına gidiyoruz. Burada bizi dört adımdan oluşan bir ekran karşılıyor.

<img src="https://i.hizliresim.com/ZDEwtk.png" alt="add_android_app" style="zoom: 50%;" />

İlk adımda uygulamamızı kayıt etmemiz gerekiyor. Bunun için oluşturmuş olduğumuz Flutter projesinin paket ismini *Android package name* alanına yazmamız gerekiyor. Eğer paket isminizi bilmiyorsanız paket isminize *proje_isminiz/android/app/build.gradle* dizinin altında applicationId kısmından ulaşabilirsiniz.

<img src="https://i.hizliresim.com/ResTeE.png" alt="package_name_path" style="zoom:50%;" />

<img src="https://i.hizliresim.com/YwbYZs.png" alt="package_name" style="zoom:50%;" />

Paket isminizi doğru yazdığınızdan emin olduktan sonra isterseniz uygulamanıza bir nickname verebilirsiniz. İlk adımın son kısmında ise Dynamic Links veya telefon ile Authentication işlemi yapmak istiyorsanız SHA-1 sertifikanızı girmeniz gerekiyor. Bütün adımları tamamladıktan sonra uygulamamızı kayıt ediyoruz. Kayıt işleminden sonra Firebase bizim için *google-services.json* adında bir dosya oluşturuyor. Bu dosyayı indirip *proje_ismi/android/app/* in altına atıyoruz.

![google_service_json](https://i.hizliresim.com/9obSav.png)

İkinci adımı da tamamladıktan sonra sıra geldi üçüncü adımı yapmaya. Bir önceki adımda indirmiş olduğumuz *google-services.json* dosyası ile projemize Gradle için Google servislerini yüklemiş olduk. Üçüncü adımda yüklemiş olduğumuz servisleri kullanabilmemiz için *build.gradle* dosyalarında birkaç düzenleme yapmamız gerekiyor. İlk olarak proje dizinindeki *build.gradle* dosyasına yani *proje_ismi/android/build.gradle*'a  aşağıdaki satırı ekliyoruz.

```
buildscript {
	repositories {
		// Bu satırın olup olmadığını kontrol edin, eğer yoksa ekleyin:
		google()
	}
	dependencies {
		...
		// Bu satırı ekliyoruz.
		classpath 'com.google.gms:google-services:4.3.5'
	}
}
allprojects {
  ...
  repositories {
    // Check that you have the following line (if not, add it):
    google()  // Google's Maven repository
    ...
  }
}
```

Uygulama dizininde bulunan *build.gradle* (*proje_ismi/android/app/build.gradle*) dosyasına ise aşağıdaki satırları ekliyoruz.

```
apply plugin: 'com.android.application'
// Bu satırı ekleyin.
apply plugin: 'com.google.gms.google-services'

dependencies {
  // Firebase BoM'u import eder.
  implementation platform('com.google.firebase:firebase-bom:26.8.0')

  // Google Analytics Firebase SDK için bu dependency'i ekleyin.
  // BoM kullandığınız zaman Firebase dependencyleri için sürüm belirtmenize gerek yok
  implementation 'com.google.firebase:firebase-analytics-ktx'
}
```

Bütün bunları yaptıktan sonra Firebase projemize Android uygulamamızı eklemiş bulunuyoruz. Şimdi sıra Flutter tarafındaki düzenlemeleri yapmaya geldi.

Flutter'da Firebase ürünlerini ve kullanabilmemiz için projemize eklememiz gereken paketler var. Bu paketlere [pub.dev](https://pub.dev/) üzerinden ulaşabiliyoruz. Flutter projemize ekleyeceğimiz [Firebase Core](https://pub.dev/packages/firebase_core) paketini pubspec.yaml dosyamıza ekliyoruz. Firebase Core paketini ekledikten sonra *main.dart* dosyasına aşağıdaki kodları ekliyoruz.

```dart
// Firebase Core paketini import ediyoruz.
import 'package:firebase_core/firebase_core.dart';

// main fonksiyonumuzu aşağıdaki şekilde düzenliyoruz.
void main() async {
    WidgetsFlutterBinding.ensureInitialized();
    await Firebase.initializeApp();
    run(MyApp);
}
```

Kodu ekledikten sonra uygulamanızı çalıştırabilirsiniz. Eğer bir hata almadan kodunuz çalışıyorsa Flutter projenize başarılı şekilde Firebase'i eklemeyi başarmışsınız demektir.

