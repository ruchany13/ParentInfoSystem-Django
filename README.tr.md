# 🎓 Veli Bilgi Sistemi - Çoklu Kurum Aktivite Yönetim Sistemi

[English](README.md) | **Türkçe**

---

## 📖 Proje Hakkında

Veli Bilgi Sistemi, velilerin çocuklarının haftalık akademik ve sosyal aktivitelerini birden fazla kurumda takip edebilmeleri için tasarlanmış, **Django tabanlı kapsamlı bir web uygulamasıdır**. Sistem, kurum yöneticilerinin aktiviteleri yönetebileceği ve velilerin sadece isim ve soyisim kullanarak çocuklarının programlarını kolayca arayabileceği merkezi bir platform sağlar.

## ✨ Temel Özellikler

### 🏢 Çoklu Kurum Yönetimi
- Sistem yöneticileri birden fazla kurum oluşturabilir ve yönetebilir
- Her kurumun kendine özel yöneticileri olabilir
- Kuruma özel marka ve bilgilendirme

### 👥 Rol Tabanlı Erişim Kontrolü
- **Sistem Yöneticileri:** Tam sistem erişimi, kurum oluşturma, genel duyurular
- **Kurum Yöneticileri:** Sadece atandıkları kurumlarla sınırlı yetki
- **Veliler:** Sadece öğrenci aktivitelerini görüntüleme yetkisi

### 📅 Aktivite Yönetimi
- Kurum yöneticileri haftalık akademik ve sosyal aktiviteler ekleyebilir
- Aktiviteler kolay gezinme için haftaya göre düzenlenir
- Hem akademik programlar hem de sosyal etkinlikler desteği
- Detaylı aktivite açıklamaları ve zamanlama

### 📢 Duyuru Sistemi
- **Genel Duyurular:** Sistem yöneticileri temel duyuruları oluşturur
- **Kuruma Özel Detaylar:** Kurum yöneticileri özel bilgiler ekleyebilir (saat, lokasyon vb.)
- **Otomatik Bilgilendirme:** Sistem otomatik olarak kurum adresi ve iletişim bilgilerini ekler

### 🔍 Veli Arama Portalı
- Veliler isim ve soyisim kullanarak çocuklarının aktivitelerini arayabilir
- Basit ve sezgisel arayüz
- Veliler için giriş gerektirmez
- Haftalık aktivite özeti

## 🏗️ Mimari ve Teknoloji Yığını

### Uygulama Katmanı
- **Framework:** Django 4.x (Python)
- **Veritabanı:** CloudNative PostgreSQL Operator ile PostgreSQL
- **Nesne Depolama:** Statik ve medya dosyaları için Cloudflare R2
- **Container Runtime:** Docker

### Altyapı Katmanı
- **Orkestrasyon:** On-Premise Kubernetes Kümesi
- **GitOps:** Sürekli dağıtım için ArgoCD
- **Depolama:** Kalıcı hacim yönetimi için Longhorn
- **Veritabanı Operatörü:** CloudNative PostgreSQL (CNPG)
- **Network:** Güvenli dış erişim için Cloudflare Tunnel

### CI/CD Pipeline
```
GitHub Push → GitHub Actions → Container Oluştur → k8s-infra Güncelle → ArgoCD Sync → Deploy
```

1. **Kod Gönderimi:** Geliştirici kodu GitHub'a gönderir
2. **Otomatik Build:** GitHub Actions yeni Docker image'ı oluşturur
3. **Image Registry:** Image GitHub Container Registry'ye (ghcr.io) gönderilir
4. **Altyapı Güncellemesi:** Image etiketi k8s-infra deposunda güncellenir
5. **ArgoCD Sync:** ArgoCD değişiklikleri algılar ve otomatik deploy eder

## 🚀 Deployment

### Ön Gereksinimler
- Kubernetes kümesi (v1.24+)
- Kümede kurulu ArgoCD
- CloudNative PostgreSQL operatörü
- Longhorn storage provisioner
- Cloudflare Tunnel (dış erişim için)

### ArgoCD ile Hızlı Başlangıç

1. **Demo Ortamını Deploy Et:**
```bash
kubectl apply -f Kubernetes/demo/argocd-application.yaml
```

2. **Examples Ortamını Deploy Et:**
```bash
kubectl apply -f Kubernetes/examples/argocd-application.yaml
```

### Manuel Deployment

1. **Namespace Oluştur:**
```bash
kubectl create namespace parentinfo
```

2. **PostgreSQL Kümesini Deploy Et:**
```bash
kubectl apply -f Kubernetes/demo/CloudNativePostgreSQL/
```

3. **Secret'ları Oluştur:**
```bash
# Ortamınıza göre secret'ları düzenleyin
kubectl apply -f Kubernetes/demo/parentinfo-app/Django-SealedSecret.yaml
kubectl apply -f Kubernetes/demo/parentinfo-app/Postgres-SealedSecret.yaml
kubectl apply -f Kubernetes/demo/parentinfo-app/R2-SealedSecret.yaml
kubectl apply -f Kubernetes/demo/parentinfo-app/RegistryCredentials-SealedSecret.yaml
```

4. **Uygulamayı Deploy Et:**
```bash
kubectl apply -f Kubernetes/demo/parentinfo-app/
```

## 📁 Proje Yapısı

```
ParentInfoSystem-Django/
├── Docker/                      # Docker yapılandırma dosyaları
│   ├── Dockerfile              # Production Dockerfile
│   ├── Dockerfile.prod         # Optimize edilmiş production build
│   ├── entrypoint-dev.sh       # Development entrypoint scripti
│   ├── entrypoint.sh           # Production entrypoint scripti
│   └── sample_data.json        # Test için örnek veri
├── Kubernetes/                  # Kubernetes manifest'leri
│   ├── demo/                   # Demo ortamı yapılandırması
│   │   ├── argocd-application.yaml
│   │   ├── CloudNativePostgreSQL/
│   │   └── parentinfo-app/
│   └── examples/               # Örnek yapılandırma şablonları
│       ├── argocd-application.yaml
│       ├── CloudNativePostgreSQL/
│       └── parentinfo-app/
└── parentinfo/                  # Django uygulaması
    ├── manage.py
    ├── activity/               # Aktivite yönetimi uygulaması
    ├── announcement/           # Duyuru sistemi uygulaması
    ├── core/                   # Temel fonksiyonlar ve modeller
    ├── dashboard/              # Admin dashboard
    └── parentinfo/             # Django proje ayarları
```

## 🔒 Güvenlik Özellikleri

- **Sealed Secrets:** Sealed Secrets ile şifrelenmiş Kubernetes secret'ları
- **SSL/TLS:** Cloudflare proxy üzerinden otomatik SSL
- **Veritabanı Güvenliği:** CNPG operatörü ile izole PostgreSQL
- **Rol Tabanlı Erişim:** Django'nun yerleşik yetkilendirme sistemi
- **Network İzolasyonu:** Güvenli dış erişim için Cloudflare Tunnel

## 🌐 Network Yapısı

Uygulama, Kubernetes servisini güvenli bir şekilde dışarıya açmak için **Cloudflare Tunnel** kullanır:

```
Internet → Cloudflare Edge → Cloudflare Tunnel → K8s Service → Uygulama Pod
```

Avantajlar:
- ✅ Açık port veya public IP gerekmez
- ✅ Otomatik SSL/TLS sonlandırma
- ✅ Cloudflare üzerinden DDoS koruması
- ✅ Erişim kontrolü ve firewall kuralları

## 📊 Depolama Mimarisi

### Veritabanı Depolama (Longhorn)
- PostgreSQL verileri için kalıcı hacimler
- Otomatik yedekleme ve snapshot özellikleri
- Küme düğümleri arasında dağıtılmış depolama

### Nesne Depolama (Cloudflare R2)
- Statik dosyalar (CSS, JavaScript, görüntüler)
- Kullanıcı tarafından yüklenen medya dosyaları
- S3-uyumlu API
- Global CDN dağıtımı

## 🌐 Canlı Demo

Uygulamanın canlı versiyonuna buradan erişebilirsiniz:  
🔗 [demo-parentinfo.ruchan.dev](https://demo-parentinfo.ruchan.dev)

> ⚠️ **Not:** Güvenlik nedeniyle admin paneli devre dışıdır.  
> Veriler statiktir ve haftalık olarak yenilenir.  
> Örnek öğrenci verileri `Docker/sample_data.json` dosyasında mevcuttur.

## 🛠️ Yerel Geliştirme

1. **Depoyu klonlayın:**
```bash
git clone https://github.com/ruchany13/ParentInfoSystem-Django.git
cd ParentInfoSystem-Django
```

2. **Virtual environment oluşturun:**
```bash
python -m venv venv
source venv/bin/activate  # Windows'ta: venv\Scripts\activate
```

3. **Bağımlılıkları yükleyin:**
```bash
pip install -r requirements.txt
```

4. **Environment değişkenlerini yapılandırın:**
```bash
export DEBUG=True
export SECRET_KEY='your-secret-key'
export DATABASE_URL='postgresql://user:password@localhost/parentinfo'
# Diğer gerekli environment değişkenlerini ekleyin
```

5. **Migration'ları çalıştırın:**
```bash
cd parentinfo
python manage.py migrate
```

6. **Superuser oluşturun:**
```bash
python manage.py createsuperuser
```

7. **Development sunucusunu çalıştırın:**
```bash
python manage.py runserver
```

## 📝 Environment Değişkenleri

Uygulama için gerekli environment değişkenleri:

```bash
# Django Ayarları
DEBUG=False
SECRET_KEY=your-django-secret-key
ALLOWED_HOSTS=demo-parentinfo.ruchan.dev

# Veritabanı
DATABASE_URL=postgresql://user:password@host:5432/dbname

# Cloudflare R2
AWS_ACCESS_KEY_ID=your-r2-access-key
AWS_SECRET_ACCESS_KEY=your-r2-secret-key
AWS_STORAGE_BUCKET_NAME=your-bucket-name
AWS_S3_ENDPOINT_URL=https://your-account-id.r2.cloudflarestorage.com
```

## 🤝 Katkıda Bulunma

Katkılarınızı bekliyoruz! Lütfen Pull Request göndermekten çekinmeyin.

## 📄 Lisans

Bu proje LICENSE dosyasında belirtilen koşullar altında lisanslanmıştır.

---

## 👨‍💻 Geliştirici Bilgileri

- **Uygulama ve Altyapı:** Ruchan Yalçın
- **GitHub:** [@ruchany13](https://github.com/ruchany13)
- **README Oluşturan:** GitHub Copilot (AI Assistant)
