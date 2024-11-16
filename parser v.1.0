from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from bs4 import BeautifulSoup
import time
import os
import requests

# Опции для Chrome
options = webdriver.ChromeOptions()
options.add_argument("--ignore-certificate-errors")
options.add_argument("--allow-insecure-localhost")

driver = webdriver.Chrome(options=options)

try:
    # Переход на сайт
    driver.get("https://www.riatomsk.ru/novosti")

    # Прокликивание кнопки "Далее" n-раз
    for _ in range(2):
        try:
            button = WebDriverWait(driver, 10).until(
                EC.element_to_be_clickable((By.CLASS_NAME, "nextLink"))
            )
            button.click()
            time.sleep(2)  # время ожидания 2 секунды
        except Exception as e:
            print(f"Ошибка при нажатии кнопки: {e}")
            break

    # Получение ссылок на новости
    time.sleep(2)  # Дополнительная пауза для полной загрузки страницы
    soup = BeautifulSoup(driver.page_source, "html.parser")
    news_links = []

    # Поиск всех новостей на странице
    articles = soup.find_all("a", class_="rubNewItem")

    if articles:
        for article in articles:
            link = article.get("href")  # Получаем атрибут 'href' напрямую
            if link:  # Проверяем, что ссылка существует
                if not link.startswith("http"):
                    link = "https://www.riatomsk.ru" + link
                news_links.append(link)
                print(f"Собрана ссылка: {link}")
    else:
        print("Не удалось найти статьи на странице.")

    # Запись ссылок в файл
    if news_links:
        try:
            with open("news_links.txt", "w", encoding="utf-8") as file:
                for link in news_links:
                    file.write(link + "\n")
            print("Ссылки на новости успешно записаны в news_links.txt.")
        except Exception as e:
            print(f"Ошибка при записи в файл: {e}")
    else:
        print("Ссылки не были найдены для записи.")

except Exception as e:
    print(f"Произошла ошибка: {e}")

finally:
    driver.quit()  # Закрытие браузера

# Чтение ссылок из файла и сбор текстов новостей
try:
    with open("news_links.txt", "r", encoding="utf-8") as file:
        links = file.readlines()

    if not links:
        print("Файл с ссылками пуст.")
    else:
        with open("news_data.txt", "w", encoding="utf-8") as news_file:
            for link in links:
                link = link.strip()
                print(f"Переход на страницу новости: {link}")

                response = requests.get(link)
                if response.status_code == 200:
                    news_soup = BeautifulSoup(response.text, "html.parser")
                    title = (
                        news_soup.find("h1").text.strip()
                        if news_soup.find("h1")
                        else "Нет заголовка"
                    )
                    content = (
                        news_soup.find("div", class_="statAbout").text.strip()
                        if news_soup.find("div", class_="statAbout")
                        else "Нет текста новости"
                    )
                    news_file.write(f"Заголовок: {title}\n")
                    news_file.write(f"Текст новости: {content}\n\n")
                    print(f"Данные собраны: {title}")
                else:
                    print(f"Ошибка при доступе к {link}: {response.status_code}")
except Exception as e:
    print(f"Ошибка при сборе новостей: {e}")
