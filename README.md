from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import time
import threading
import random

def xpath_wait(driver, xpath):
    try:
        WebDriverWait(driver, 10).until(
            EC.presence_of_element_located((By.XPATH, xpath)))
    except Exception, e:
        pass


def parser_code():
    login_url = 'https://mail.protonmail.com/login'
    username = 'lingying_spider@protonmail.com'
    passwd = 'Lingying_spider001'
    username = 'lingying_spider002@protonmail.com'
    passwd = 'Lingying_spider002'
    username = 'lingying_spider003@protonmail.com'
    passwd = 'Lingying_spider003'

    driver = webdriver.Chrome()
    driver.get(login_url)
    driver.find_element_by_id('username').send_keys(username)
    driver.find_element_by_id('password').send_keys(passwd)
    driver.find_element_by_id('login_btn').click()
    refresh_btn = '//*[@id="pm_sidebar"]/ul[1]/li[1]/a/button'
    xpath_wait(driver, xpath=refresh_btn)
    driver.find_element_by_xpath(refresh_btn).click()
    title_btn = '//*[@id="conversation-list-columns"]/div[2]/div[2]/div[1]/h4/span[2]'
    xpath_wait(driver, xpath=title_btn)
    driver.find_element_by_xpath(title_btn).click()
    xpath_wait(driver, xpath='//*[@id="message0"]')
    id_num = 0
    for i in range(0,1000):
        try:
            driver.find_element_by_id('message'+str(i))
        except:
            id_num = i-1
            break
    code_text = '//*[@id="message'+str(id_num)+'"]/div[2]/div[2]/div/table/tbody/tr[2]/td/table/tbody/tr/td/table[1]/tbody/tr/td/table[5]/tbody/tr/td[2]/table[2]/tbody/tr[6]/td'
    xpath_wait(driver, xpath=code_text)
    text = driver.find_element_by_xpath(code_text).text
    driver.quit()
    return text.split(':')[-1]

profile_list = ['https://www.linkedin.com/in/wongky/','https://www.linkedin.com/in/lewislai/',
                'https://www.linkedin.com/in/catherine-kirchmann-a2549213/'
    ,'https://www.linkedin.com/in/bo-dong-063728135/']

done_list = []
count = 1
stop_flag = False


def get_linked_data():
    global profile_list
    global count
    global stop_flag
    global done_list
    login_url = 'https://www.linkedin.com/'
    username = 'lingying_spider@protonmail.com'
    passwd = 'Lingying_spider001'
    username = 'lingying_spider002@protonmail.com'
    passwd = 'Lingying_spider002'
    username = 'lingying_spider003@protonmail.com'
    passwd = 'Lingying_spider003'

    driver = webdriver.Chrome()
    driver.get(login_url)
    driver.find_element_by_id('login-email').send_keys(username)
    driver.find_element_by_id('login-password').send_keys(passwd)
    flag = True
    try:
        driver.find_element_by_name('submit').click()
    except Exception, e:
        flag = False
    if not flag:
        driver.find_element_by_id('login-submit').click()
    while True:
        if len(profile_list)==0:
            random.randint(1, 10)
            continue
        try:
            profile = profile_list.pop()
            if stop_flag:
                break
            if driver.current_url == 'https://www.linkedin.com/uas/consumer-email-challenge':
                stop_flag = True
                code = parser_code()
                driver.find_element_by_id('verification-code').send_keys(code)
                driver.find_element_by_id('btn-primary').click()
                if driver.current_url == 'https://www.linkedin.com/uas/consumer-email-challenge':
                    break
                else:
                    stop_flag = False
            driver.get(profile)
            done_list.append(profile)

            WebDriverWait(driver, 5).until(EC.presence_of_element_located((By.ID, 'education-section')))
            print driver.find_element_by_id('education-section').text
            xpath_wait(driver, '//a[contains(@href,"/in/")]')
            els = driver.find_elements_by_xpath('//a[contains(@href,"/in/")]')
            for href in [el.get_attribute('href') for el in els]:
                if href == profile:
                    continue
                if not len(href.split('/'))==6:
                    continue
                if href not in profile_list and href not in done_list:
                    profile_list.append(href)
            count += 1
            print "+" * 20 + str(count) + "+" * 20
            #random.randint(1, 10)
        except Exception, e:
            pass

if __name__ == "__main__":
    time.sleep(1)
    timer_thread_1 = threading.Thread(target=get_linked_data, args=())
    # timer_thread_1.setDaemon(True)
    timer_thread_1.start()
    time.sleep(10)
    #timer_thread_2 = threading.Thread(target=get_linked_data, args=())
    #timer_thread_2.start()




