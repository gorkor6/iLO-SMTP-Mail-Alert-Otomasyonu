# iLO SMTP Mail Alert Otomasyonu

Bu proje, HPE iLO arayüzleri üzerinden otomatik olarak test maili göndermek ve yapılandırmayı doğrulamak için geliştirilmiştir. Selenium ve Python kullanılarak, belirlenen iLO IP adreslerine otomatik giriş yapılır ve "Test Alert" maili gönderilir.

## Özellikler

- Çoklu iLO IP adreslerine otomatik erişim
- Selenium ile web arayüzünde otomatik gezinme ve buton tıklama
- Ortam değişkenleriyle (kullanıcı adı, şifre, tekrar deneme limiti) kolay yapılandırma
- Her cihaz için işlem durumunu gösteren konsol çıktısı

## Gereksinimler

- Python 3.x
- [Selenium](https://pypi.org/project/selenium/)
- [ChromeDriver](https://chromedriver.chromium.org/)
- [python-dotenv](https://pypi.org/project/python-dotenv/)

## Kurulum

1. Bu depoyu klonlayın:
   ```bash
   git clone https://github.com/gorkor6/iLO-SMTP-Mail-Alert-Otomasyonu.git
   cd iLO-SMTP-Mail-Alert-Otomasyonu
   ```

2. Gerekli Python paketlerini yükleyin:
   ```bash
   pip install -r requirements.txt
   ```

3. .env dosyasını oluşturun ve aşağıdaki değişkenleri kendi bilgilerinize göre doldurun:
   ```
   ILO_USERNAME=kullanici_adiniz
   ILO_PASSWORD=sifreniz
   RETRY_LIMIT=2
   ```

4. [ChromeDriver](https://chromedriver.chromium.org/downloads) kurulu olmalı ve yolunu kodda güncellemelisiniz (varsayılan: `/usr/local/bin/chromedriver`).

## Kullanım

Script'i çalıştırmak için:
```bash
python main.py
```

Her iLO IP adresi için otomatik olarak giriş yapılır ve "Test Alert" maili gönderilir.

## Örnek Kod

Aşağıda, iLO web arayüzüne giriş yapan ve test mail butonunu bulan temel kod bloğu örneği verilmiştir:

```python
from selenium import webdriver

def login_to_ilo(driver, url):
    driver.get(url)
    # ... giriş işlemleri
```

Daha fazla ayrıntı için [main.py](main.py) dosyasına bakabilirsiniz.

## Notlar

- Script başlatıldığında tüm IP listesi döngüye alınır.
- Her bir IP için işlem konsola yazdırılır.
- Hatalar durumunda tekrar deneme yapılır.

## Lisans

Bu proje MIT lisansı ile lisanslanmıştır.
