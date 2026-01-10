# 🛡️ Velora OS - Akıllı Kişisel Asistan & Şifre Yöneticisi
### (Microservices + AI Agents + Secure Vault + Monitoring)

![Velora OS Banner](https://via.placeholder.com/1000x300/4a044e/ffffff?text=Velora+OS+-+Purple+Edition)

## 🚀 Proje Genel Bakış
**Velora OS**; kullanıcıların günlük görevlerini yönettiği, notlarını tuttuğu ve en önemlisi **hassas şifrelerini güvenle sakladığı** modern bir web platformudur. Mikroservis mimarisi üzerine kurulu olan sistem, arka planda çalışan yapay zeka ajanları ile sistem sağlığını denetler ve kullanıcının veri güvenliğini (Password Strength Analysis) aktif olarak analiz eder.

---

## 🧱 Sistem Mimarisi ve Veri Akışı

Bu proje tam izole edilmiş **Docker Konteynerleri** üzerinde çalışır.

* **Frontend (Nginx):** "Glassmorphism" tasarım diline sahip modern arayüz.
* **Backend (FastAPI):** Asenkron çalışan, Pydantic ile veri doğrulayan ana sunucu.
* **Security Layer:** JWT tabanlı oturum ve Bcrypt tabanlı şifreleme katmanı.
* **AI Layer (Ollama/MCP):** Verileri analiz eden ve raporlayan yapay zeka motoru.

### 📊 Sistem Akış Şeması (Sequence Diagram)
Aşağıdaki diyagram, kullanıcının sisteme giriş yapması ve **yeni bir şifre kaydederken** sistemin nasıl güvenlik kontrolü yaptığını göstermektedir:

```mermaid
sequenceDiagram
    participant User as Kullanıcı
    participant UI as Frontend (Web)
    participant API as Backend (FastAPI)
    participant DB as Veritabanı (PostgreSQL)

    %% 1. OTURUM AÇMA
    Note over User, DB: 🔐 Kimlik Doğrulama
    User->>UI: Giriş Yap (E-posta/Şifre)
    UI->>API: POST /auth/login
    API->>DB: Kullanıcıyı Sorgula & Hash Kontrolü
    DB-->>API: Onay
    API-->>UI: JWT Access Token (24 Saatlik)
    
    %% 2. ŞİFRE EKLEME VE ANALİZ (ÖNEMLİ)
    Note over User, DB: 🛡️ Şifre Güvenlik Analizi
    User->>UI: "Yeni Şifre Ekle" (Hesap + Şifre)
    UI->>API: POST /api/passwords/{uid}
    
    rect rgb(30, 0, 30)
        API->>API: Token Doğrula (Bearer)
        API->>API: Şifre Gücünü Analiz Et (Regex)
        Note right of API: Kriterler: Uzunluk, Büyük Harf, Sayı
    end
    
    API->>DB: Şifreyi Kaydet (Güç Skoru ile)
    DB-->>API: Kayıt Başarılı
    API-->>UI: "Şifre Eklendi - Güç: Yüksek" Yanıtı

    %% 3. LİSTELEME
    UI->>API: GET /api/passwords/{uid}
    API->>DB: Şifreleri Çek
    DB-->>API: Şifre Listesi
    API-->>UI: Listeyi Ekranda Göster



Katman,Kullanılan Teknolojiler,Durum
Backend API,"Python FastAPI, SQLAlchemy, Pydantic",✔ Hazır
API Dokümantasyonu,Swagger UI (OpenAPI 3.0),✔ Hazır
Frontend UI,"HTML5, TailwindCSS, JavaScript (Glassmorphism)",✔ Hazır
Veritabanı,PostgreSQL 15 (Kalıcı Depolama),✔ Hazır
AI Katmanı,"Ollama (Gemma:2b), MCP (Model Context Protocol)",✔ Hazır
Güvenlik,"Bcrypt (Hashing), Python-Jose (JWT)",✔ Hazır
İzleme (Monitoring),Prometheus (Metrik Toplama),✔ Hazır
Orkestrasyon,Docker Compose (Çok Servisli Mimari),✔ Hazır






Servis Adı,Port,Açıklama
velora_api,8000,"Ana Arka Uç; Şifre analizi, JWT doğrulama ve veri işlemlerini yapar."
velora_frontend,80,Kullanıcı arayüzü; Nginx üzerinde çalışan modern web paneli.
velora_db,5432,PostgreSQL veritabanı sunucusu. Sadece iç ağa açıktır.
prometheus,9090,Backend'den gelen metrikleri (/metrics) toplar ve saklar.
ollama,11434,Yerel LLM (Gemma:2b) motoru; AI analizlerini sağlar.
mcp_server,-,Yapay zeka araçlarını (Tools) barındıran sunucu.
velora_agent,-,Otonom ajan; verileri analiz edip /reports klasörüne rapor yazar.



Güvenlik ve Şifre Yönetimi
Sistem, kullanıcı verilerini korumak için çok katmanlı bir güvenlik yapısı kullanır:

Şifre Gücü Analizi (Password Strength Meter):

Kullanıcı kasasına yeni bir şifre eklerken, Backend (main.py) şifreyi analiz eder.

Kriterler: Uzunluk (>8), Büyük Harf (A-Z), Rakam (0-9).

Sonuç: "Zayıf", "Orta" veya "Güçlü" etiketiyle veritabanına kaydedilir.

Güvenli Depolama (Hashing):

Kullanıcıların kendi giriş şifreleri veritabanında asla açık metin (plain-text) olarak saklanmaz.

Bcrypt algoritması ile geri döndürülemez şekilde hashlenir.

Yetkilendirme (JWT):

/auth/login dışında kalan tüm API uç noktaları Authorization: Bearer <token> başlığını zorunlu kılar.







