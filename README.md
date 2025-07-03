# iLO SMTP Mail Alert Otomasyonu

Bu proje, HPE iLO arayüzleri üzerinden otomatik olarak test maili göndermek ve yapılandırmayı doğrulamak için geliştirilmiştir. Python ve Selenium kullanılarak, belirlenen iLO IP adreslerine otomatik giriş yapılır ve "Test Alert" maili gönderilir.

## Özellikler

- Çoklu iLO IP adresine otomatik erişim
- Selenium ile web arayüzünde otomatik gezinme ve buton tıklama
- Ortam değişkenleriyle (kullanıcı adı, şifre, tekrar deneme limiti) kolay yapılandırma
- Her cihaz için işlem durumunu gösteren konsol çıktısı

## Gereksinimler

- Python 3.x
- Selenium
- ChromeDriver
- python-dotenv

## Kurulum

1. Depoyu klonlayın:
   ```bash
   git clone https://github.com/gorkor6/iLO-SMTP-Mail-Alert-Otomasyonu.git
   cd iLO-SMTP-Mail-Alert-Otomasyonu
   ```

2. Gerekli Python paketlerini yükleyin:
   ```bash
   pip install -r requirements.txt
   ```

3. .env dosyasını oluşturun ve aşağıdaki değişkenleri doldurun:
   ```
   ILO_USERNAME=kullanici_adiniz
   ILO_PASSWORD=sifreniz
   RETRY_LIMIT=2
   ```

4. ChromeDriver kurulu olmalı ve yolunu kodda belirtmelisiniz (varsayılan: `/usr/local/bin/chromedriver`).

## Kullanım

Script'i çalıştırmak için:
```bash
python main.py
```

Her iLO IP adresi için otomatik olarak giriş yapılır ve "Test Alert" maili gönderilir.

## Lisans

MIT
