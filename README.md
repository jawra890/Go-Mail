from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import TimeoutException
import random
import string
import time

def generate_username():
    return ''.join(random.choices(string.ascii_lowercase + string.digits, k=random.randint(8, 12))) + "@gmail.com"

def create_gmail_accounts(count):
    options = webdriver.ChromeOptions()
    options.add_argument('--headless')
    options.add_argument('--disable-gpu')
    options.add_argument('--no-sandbox')
    options.add_argument('--user-agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36')
    
    # Replace with your proxy list if needed
    # options.add_argument(f'--proxy-server=http://user:pass@ip:port')

    driver = webdriver.Chrome(options=options)
    base_url = "https://accounts.google.com/signup/v2/webcreateaccount"
    
    for i in range(count):
        driver.get(base_url)
        time.sleep(2)
        
        try:
            email = generate_username()
            
            # Fill First Name
            first_name = driver.find_element(By.NAME, "firstName")
            first_name.send_keys("User")
            
            # Fill Last Name
            last_name = driver.find_element(By.NAME, "lastName")
            last_name.send_keys("Account")
            
            # Fill Email
            email_input = driver.find_element(By.NAME, "Username")
            email_input.send_keys(email)
            
            time.sleep(1)
            
            # Click Next
            next_btn = driver.find_element(By.XPATH, "//input[@value='Next']")
            next_btn.click()
            
            time.sleep(3)
            
            # Bypass Phone Verification: Click "Try again" if phone field appears
            phone_field = driver.find_element(By.NAME, "phoneNumber")
            if phone_field.is_displayed():
                try_again_btn = driver.find_element(By.XPATH, "//span[text()='Try again']")
                for _ in range(15):
                    try_again_btn.click()
                    time.sleep(0.5)
                
                # If it disappears, proceed
                if not phone_field.is_displayed():
                    # Proceed to Password
                    password = "HackedPassword123!"
                    password_field = driver.find_element(By.NAME, "Passwd")
                    password_field.send_keys(password)
                    confirm_password = driver.find_element(By.NAME, "ConfirmPasswd")
                    confirm_password.send_keys(password)
                    
                    # Click Next
                    next_btn = driver.find_element(By.XPATH, "//input[@value='Next']")
                    next_btn.click()
                    print(f"Account created: {email}")
                else:
                    print(f"Verification stuck for: {email}")
            else:
                print(f"Phone verification skipped for: {email}")

        except Exception as e:
            print(f"Error creating {email}: {e}")
        finally:
            driver.quit()

# Run to create 5 accounts
create_gmail_accounts(5)
