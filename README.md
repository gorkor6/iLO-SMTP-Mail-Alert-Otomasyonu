# iLO-SMTP-Mail-Alert-Otomasyonu
iLO SMTP Mail Alert Otomasyonu
import time
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.chrome.options import Options as ChromeOptions
from selenium.webdriver.chrome.service import Service as ChromeService

from dotenv import load_dotenv
import os

# iLO IP adresleri örnek (genel format)
ILO_LIST = (
    [f"https://10.0.0.{i}" for i in range(60, 100)] +
    [f"https://10.0.1.{i}" for i in range(20, 40)] +
    [f"https://10.0.2.{i}" for i in range(20, 36)]
)

# .env dosyasını yükle (kullanıcı adı, şifre ve retry sayısı)
load_dotenv(dotenv_path="./.env")

USERNAME = os.getenv("ILO_USERNAME")
PASSWORD = os.getenv("ILO_PASSWORD")
RETRY_LIMIT = int(os.getenv("RETRY_LIMIT", 2))

def setup_chrome_driver():
    options = ChromeOptions()
    options.add_argument("--ignore-certificate-errors")
    options.add_argument("--disable-blink-features=AutomationControlled")
    options.add_argument("--window-size=1920,1080")
    options.add_argument("--no-sandbox")
    options.add_argument("--disable-dev-shm-usage")
    options.add_argument("--headless")
    options.add_argument("--remote-debugging-port=9222")
    options.add_experimental_option("excludeSwitches", ["enable-automation"])
    options.add_experimental_option('useAutomationExtension', False)

    service = ChromeService(executable_path="/usr/local/bin/chromedriver")
    driver = webdriver.Chrome(service=service, options=options)
    driver.execute_script("Object.defineProperty(navigator, 'webdriver', {get: () => undefined})")
    return driver

def login_to_ilo(driver, url):
    driver.get(url)
    time.sleep(3)
    iframe = WebDriverWait(driver, 20).until(EC.presence_of_element_located((By.NAME, "appFrame")))
    driver.switch_to.frame(iframe)

    WebDriverWait(driver, 20).until(EC.presence_of_element_located((By.ID, "username"))).send_keys(USERNAME)
    driver.find_element(By.ID, "password").send_keys(PASSWORD)
    driver.find_element(By.ID, "login-form__submit").click()
    time.sleep(5)

def navigate_to_mail_page(driver):
    driver.switch_to.default_content()
    app_iframe = WebDriverWait(driver, 20).until(EC.presence_of_element_located((By.NAME, "appFrame")))
    driver.switch_to.frame(app_iframe)
    WebDriverWait(driver, 20).until(EC.element_to_be_clickable((By.XPATH, "//a[contains(text(), 'Management')]"))).click()
    time.sleep(2)
    WebDriverWait(driver, 20).until(EC.element_to_be_clickable((By.XPATH, "//a[contains(text(), 'Mail')]"))).click()
    time.sleep(5)

def find_test_alert_button(driver):
    # Önce default content içinde arayalım
    driver.switch_to.default_content()
    try:
        button = WebDriverWait(driver, 10).until(EC.presence_of_element_located((By.ID, "test_alert")))
        if button.is_displayed() and button.is_enabled():
            print("Buton default content içinde bulundu.")
            return button
    except:
        pass

    # Yoksa tüm iframe'lerde ara
    iframes = driver.find_elements(By.TAG_NAME, "iframe")
    for iframe in iframes:
        try:
            driver.switch_to.frame(iframe)
            button = driver.find_element(By.ID, "test_alert")
            if button.is_displayed() and button.is_enabled():
                print("Buton iframe içinde bulundu.")
                return button
        except:
            pass
        driver.switch_to.default_content()
    return None

def click_test_button(driver, button):
    try:
        WebDriverWait(driver, 10).until(EC.element_to_be_clickable((By.ID, "test_alert")))
        driver.execute_script("arguments[0].scrollIntoView(true);", button)
        time.sleep(1)

        if button.is_displayed() and button.is_enabled():
            try:
                button.click()
                print("\t✅ Normal click ile tıklandı!")
            except:
                driver.execute_script("arguments[0].click();", button)
                print("\t✅ JS click başarılı!")
        else:
            print("\t⚠️ Buton görünmüyor ya da pasif")
            return False

        time.sleep(3)
        return True
    except Exception as e:
        print(f"\t❌ Tıklama hatası: {e}")
        return False

def test_ilo_alert(ilo_url):
    for attempt in range(1, RETRY_LIMIT + 1):
        print(f"\n🖥️  iLO: {ilo_url.split('//')[1]} (Deneme {attempt})")
        driver = None
        try:
            driver = setup_chrome_driver()
            login_to_ilo(driver, ilo_url)
            navigate_to_mail_page(driver)
            button = find_test_alert_button(driver)
            if button:
                if click_test_button(driver, button):
                    print("\t✅ Test Alert Mail gönderildi.")
                    return
                else:
                    raise Exception("Buton tıklanamadı")
            else:
                raise Exception("Buton bulunamadı")
        except Exception as e:
            print(f"\t⚠️ Hata oluştu: {e}")
        finally:
            if driver:
                driver.quit()
    print(f"\t❌ {ilo_url.split('//')[1]} için işlem başarısız.")

if __name__ == "__main__":
    for ilo_url in ILO_LIST:
        test_ilo_alert(ilo_url)
