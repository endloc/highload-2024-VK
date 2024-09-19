# Проектирование высоконагруженной системы на примере сервиса для путешествий Tripadvisor
Курсовая работа по предмету "Проектирование высоконагруженных систем" в образовательном центре ВК в МГТУ им. Баумана.

## 1. Тема, аудитория, функционал
В качестве темы работы выбран **Tripadvisor** - крупный туристический сервис для планирования путешествий. Сайт работает в 40 странах мира, на 20 языках и предоставляет пользователям около 1 миллиарда отзывов более чем о 8 миллионах объектов.

### Аудитория
Сервис имеет около 300 млн уникальных пользователей в месяц[^1].
Данные на июнь 2023 года[^2]:
| Страна         | Количество пользователей, % |
| -------------- | --------------------------- |
| США            | 67.71                       |
| Великобритания | 1.91                        |
| Польша         | 1.54                        |
| Канада         | 1.38                        |
| Другие         | 26.31                       |

### Аналоги
Аналоги Tripadvisor - Booking, Expedia, Airbnb, Yelp - большие сервисы с обширной аудиторией, что подтверждает существование рыночной ниши для подобных платформ.

### Функционал
Ключевой функционал сервиса, который планируется спроектировать:
1. Создание и редактирование профиля пользователя.
2. Создание и публикация отзывов.
3. Просмотр отзывов и рейтингов.
4. Поиск и фильтрация объектов (отели, рестораны, достопримечательности - по категории, названию, местоположению, рейтингу, цене).
5. Добавление мест в "Избранное" ("Trips").
6. Рекомендации.

### Продуктовые решения
1. Подборки мест - Travellers' Choice:
- лучшие (на основании рейтингов),
- определенного типа (например, "All inclusive"),
- в популярных направлениях.
2. Персональные рекомендации.
3. Форумы для путешественников.

## 2. Расчет нагрузки

### Продуктовые метрики

* **MAU:** 300 млн[^1]
* **DAU:** 45 млн
* **Количество зарегистрированных пользователей**: 80 млн[^3]

Примечание. Данных о DAU в свободном доступе не нашлось. В связи с низкой частотой использования (эпизодически, когда планируются поездки) примем DAU как 15% от MAU.

* **Средний размер хранилища пользователя:**

| Данные           | Кол-во на одного пользователя | Размер единицы | Общий размер |
| ---------------- | ----------------------------- | -------------- | ------------ |
| Профиль          | 1                             | 1 МБ           | 1 МБ         |
| Текстовые отзывы | 12                            | 2 КБ           | 24 КБ        |
| Фото в отзывах   | 2                             | 500 КБ         | 1 МБ         |

*Профиль*: фото профиля (500 КБ), обложка профиля (500 КБ), остальная информация (имя, дата присоединения, избранное и т.п.) (10 КБ).

*Текстовые отзывы*: на данный момент на сайте опубликовано около 1 млрд отзывов[^2]. Отзывы могут оставлять только зарегистрированные пользователи. Итак, в среднем 1000 млн / 80 млн ~ 12  отзывов на 1 пользователя.

*Фото в отзывах*: путешественники опубликовали более 160 млн таких фото[^5]. Итак, в среднем 160 млн / 80 млн = 2 фото на 1 пользователя.

Таким образом, данные, связанные с 1 пользователем, занимают в среднем **2 МБ** хранилища.

* **Среднее количество действий пользователя по типам в день:**

| Действие                          | Среднее кол-во действий в день на 1 пользователя |
| --------------------------------- | ------------------------------------------------ |
| Создание и редактирование профиля | 0,016 (~ 1 раз в 2 месяца)                       |
| Публикация отзывов                | 0,001                                            |
| Просмотр отзывов                  | 10                                               |
| Поиск объектов                    | 5                                                |
| Добавление мест в "Избранное"     | 2                                                |
| Взаимодействовие с рекомендациями | 1                                                |

Публикация отзывов: за 2022 год было опубликовано 30,2 млн отзывов[^2]. 30 млн / 80 млн / 12 месяцев / 30 дней ~ 0,001 отзыва в день в среднем на 1 зарегистрированного пользователя.

### Технические метрики

**Общий объем хранимых данных:**

| Данные       | Размер |
| ------------ | ------ |
| Пользователи | 80 ТБ  |
| Объекты      | 44 ТБ  |
| Отзывы       | 82 ТБ  |

*Пользователи*: 1 МБ * 80 млн (зарегистрированных пользователей[^3]) = 80 ТБ.

*Объекты*: для каждого объекта хранятся общие данные (название, описание, адрес, рейтинг, категории и т.п.) (~ 0,5 МБ), фотографии (около 10 штук по 500 КБ каждая). Общее число объектов[^2] - 8 млн. Тогда (0,5 МБ + 10 * 0,5 МБ) * 8 млн = 44 ТБ.

*Отзывы*: общее число отзывов[^2] - 1 млрд,  общее число фото[^5] - 160 млн. Тогда 0,002 МБ * 1 млрд + 0,5 МБ * 160 млн = 2 ТБ + 80 ТБ = 82 ТБ.

Общий объем хранилища составляет **206 ТБ**.

## Использованные источники
[^1]: [Tripadvisor First Quarter 2024 Investor Presentation](https://ir.tripadvisor.com/static-files/1b6bf47a-248a-40fe-840b-1bb9a4774ace)
[^2]: [60+ Tripadvisor Statistics, Facts, and Trends 2023](https://passport-photo.online/blog/tripadvisor-statistics/)
[^3]: [Tripadvisor - Wikipedia Ru](https://ru.wikipedia.org/wiki/Tripadvisor)
[^4]: [Tripadvisor - Wikipedia En](https://en.wikipedia.org/wiki/Tripadvisor)
[^5]: [Where is TripAdvisor Going? 39+ Signpost Statistics](https://review42.com/resources/tripadvisor-statistics/)
