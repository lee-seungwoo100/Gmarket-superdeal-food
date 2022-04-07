from os import link
import requests
from bs4 import BeautifulSoup
from selenium import webdriver
from selenium.webdriver.common.by import By
import time

options = webdriver.ChromeOptions()
options.add_argument('headless')
options.add_argument("disable-gpu")
options.add_argument('user-agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.84 Safari/537.36')
browser = webdriver.Chrome(options=options) #'./chromdriver.exe'

url = 'https://m.gmarket.co.kr/n/superdeal?categoryCode=400000139&categoryLevel=1'
browser.get(url)
browser.implicitly_wait(3)

items = browser.find_elements(By.CSS_SELECTOR,'div.component')
for item in items[:30]:
    try:
        brandname = item.find_element(By.CSS_SELECTOR,'span.text__brand').text.split('명')[1]
    except:
        brandname = '브랜드 없음'
    productname = item.find_element(By.CSS_SELECTOR,'span.text__title').text.split('명')[1]

    productprice = item.find_element(By.CSS_SELECTOR,'strong.text__price').text
    try:
        productcoupon = item.find_element(By.CSS_SELECTOR,'span.gds-nudging-label').text
    except:
        productcoupon = '쿠폰없음'
    try:
        productsell = item.find_element(By.CSS_SELECTOR,'span.text__num').text
    except:
        productsell = '판매이력 없음'
    productlink = item.find_element(By.CSS_SELECTOR,'div.box__itemcard-superdeal--inner > a').get_attribute('href')
    res = requests.get(productlink)
    soup = BeautifulSoup(res.content,'html.parser')
    review = soup.select_one('span.num').get_text()

    
