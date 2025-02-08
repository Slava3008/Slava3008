import requests
from bs4 import BeautifulSoup
import pandas as pd


def collect_user_rates(user_login):
    page_num = 15
    data = []

    while True:
        url = f'https://letterboxd.com/rfeldman9/films/diary/'
        html_content = requests.get(url).text
        soup = BeautifulSoup(html_content, 'lxml')

        entries = soup.find_all('tr', class_='diary-entry-row viewing-poster-container')

        if len(entries) == 0:
            break

        for entry in entries:
            td_film_details = entry.find('td', class_='td-film-details')
            film_name = td_film_details.find('a').text.strip()
            release_date = entry.find('td', class_='td-released center').text.strip()

            td_rating_rating_green = entry.find('td', class_='td-rating rating-green')
            rating_span = td_rating_rating_green.find('span', class_='rating')
            classes = rating_span.get('class', [])
            rating = classes[1].split('-')[1] if len(classes) > 1 else 'N/A'

            data.append({'film_name': film_name, 'release_date': release_date, 'rating': rating})

        page_num += 1

    return data

user_login = 'rfeldman9'
user_rates = collect_user_rates(user_login)

df = pd.DataFrame(user_rates)
df.to_excel('user_rates.xlsx', index=False)

print(f"Total films collected: {len(user_rates)}")
print(user_rates[:5])

Парсер пользовательских оценок на Letterboxd

Постановка задачи
Целью данного проекта является разработка парсера, который собирает данные о фильмах из дневника пользователя на сайте Letterboxd. Собранные данные включают название фильма, год выпуска и оценку, выставленную пользователем. Результаты сохраняются в формате Excel для дальнейшего анализа.

Инструкция по сборке и запуску вашего кода

Предварительные требования
- Python 3.x
- Библиотеки: requests, BeautifulSoup4, pandas, lxml

Установка зависимостей
Для установки необходимых библиотек используйте pip:

pip install requests beautifulsoup4 pandas lxml

Запуск кода
1. Скопируйте код в файл с расширением .py, например letterboxd_parser.py.
2. В файле, замените user_login на нужный вам логин пользователя Letterboxd.
3. Запустите скрипт:

python letterboxd_parser.py

4. После завершения работы скрипта, файл user_rates.xlsx будет создан в текущем каталоге.

Пример результатов работы парсера
После выполнения скрипта, в консоли будет выведено количество собранных фильмов и первые пять записей:

print([
    {'film_name': 'Companion', 'release_date': '2025', 'rating': '8'},
    {'film_name': 'The Greatest Showman', 'release_date': '2017', 'rating': '6'},
    {'film_name': 'Conclave', 'release_date': '2024', 'rating': '8'},
    {'film_name': 'Raiders of the Lost Ark', 'release_date': '1981', 'rating': '10'},
    {'film_name': 'Death Becomes Her', 'release_date': '1992', 'rating': '8'}
])

Собранные данные будут сохранены в Excel-файл, который можно использовать для дальнейшего анализа.
