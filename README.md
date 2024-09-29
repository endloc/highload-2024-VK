# Проектирование высоконагруженной системы на примере сервиса для путешествий Tripadvisor
Курсовая работа по предмету "Проектирование высоконагруженных систем" в образовательном центре ВК в МГТУ им. Баумана.

## 1. Тема, аудитория, функционал
В качестве темы работы выбран **Tripadvisor** - крупный туристический сервис для планирования путешествий. Сайт работает в 40 странах мира, на 20 языках и предоставляет пользователям около 1 миллиарда отзывов более чем о 8 миллионах объектов.

### Аудитория
Сервис имеет около 300 млн уникальных пользователей в месяц[^1].

Распределение по странам[^9]:
| Страна         | Количество пользователей, % |
| -------------- | --------------------------- |
| США            | 62,50                       |
| Великобритания | 3,92                        |
| Германия       | 1,61                        |
| Канада         | 1,56                        |
| Польша         | 1,54                        |
| Нидерланды     | 1,31                        |
| Другие         | 27,56                       |

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
* **Количество активных пользователей:** 130 млн[^1] 
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
| Создание и редактирование профиля | 0,0027 (1 раз в год)                             |
| Публикация отзывов                | 0,001                                            |
| Поиск объектов                    | 0,333 (10 раз в месяц)                           |
| Просмотр карточки объекта         | 1                                                |
| Просмотр отзывов                  | 4                                                |
| Добавление мест в "Избранное"     | 0,167 (5 раз в месяц)                            |
| Взаимодействие с рекомендациями   | 0,167 (5 раз в месяц)                            |

*Публикация отзывов*: за 2022 год было опубликовано 30,2 млн отзывов[^2]. 30 млн / 80 млн / 12 месяцев / 30 дней ~ 0,001 отзыва в день в среднем на 1 зарегистрированного пользователя.

* **Прирост по количеству:**

| Категория                                | Прирост, млн/год |
| ---------------------------------------- | ---------------- |
| Объекты в каталоге[^6]                   | 1,33             |
| Зарегистрированные пользователи[^7] [^8] | 21               |


### Технические метрики

* **Общий объем хранимых данных:**

| Данные       | Размер |
| ------------ | ------ |
| Объекты      | 44 ТБ  |
| Пользователи | 160 ТБ |
| Отзывы       | 82 ТБ  |

*Объекты*: для каждого объекта хранятся общие данные (название, описание, адрес, рейтинг, категории и т.п.) (~ 0,5 МБ), фотографии (около 10 штук по 500 КБ каждая). Общее число объектов[^2] - 8 млн. Тогда (0,5 МБ + 10 * 0,5 МБ) * 8 млн = 44 ТБ.

*Пользователи*: 2 МБ * 80 млн (зарегистрированных пользователей[^3]) = 160 ТБ.

*Отзывы*: общее число отзывов[^2] - 1 млрд,  общее число фото[^5] - 160 млн. Тогда 0,002 МБ * 1 млрд + 0,5 МБ * 160 млн = 2 ТБ + 80 ТБ = 82 ТБ.

Общий объем хранилища составляет **286 ТБ**.

* **Прирост по хранению:**

| Категория                                | Прирост, ТБ/год |
| ---------------------------------------- | --------------- |
| Объекты в каталоге[^6]                   | 7,315           |
| Зарегистрированные пользователи[^7] [^8] | 42              |

*Объекты*: 5,5 МБ (см. предыдущий пункт) * 1,33 млн (см. прирост по кол-ву) = 7,315 ТБ.

*Пользователи*: 2 МБ * 21 млн = 42 ТБ.

* **Сетевой трафик:**

Для существенных типов трафика:
| Действие                  | Трафик на ед. | Обычный, Гб/с | Пиковый (х2), Гб/с | Суточный, Тб/с |
| ------------------------- | ------------- | ------------- | ------------------ | -------------- |
| Поиск объектов            | 1 МБ          | 1,384         | 2,768              | 120            |
| Просмотр карточки объекта | 5,5 МБ        | 22,917        | 45,834             | 1980           |
| Просмотр отзывов          | 500 КБ        | 8,333         | 16,666             | 720            |

Формула: Трафик = [ср. кол-во действий в день на 1 польз.] * [трафик на 1 действие] / 24 / 60 / 60 * DAU.

* **RPS:**

| Действие                          | RPS средний | RPS пиковый (х2) |
| --------------------------------- | ----------- | ---------------- |
| Создание и редактирование профиля | 1,4         | 2,8              |
| Публикация отзывов                | 0,5         | 1                |
| Поиск объектов                    | 173         | 346              |
| Просмотр карточки объекта         | 520         | 1040             |
| Просмотр отзывов                  | 2082        | 4164             |
| Добавление мест в "Избранное"     | 86          | 172              |
| Взаимодействие с рекомендациями   | 86          | 172              |
| --------------------------------- | ----------- | ---------------- |
| Общее число                       | 2949        | 5898             |

Формула: RPS = [ср. кол-во действий в день на 1 польз.] / 24 / 60 / 60 * DAU.
Общее число запросов к сервису в секунду: обычный 

## 3. Глобальная балансировка нагрузки

* **Обоснования расположения ДЦ:**

Вспомним таблицу распределения пользователей сервиса по странам и посчитаем общий RPS для каждой из них (умножим долю пользователей в конкретной стране на общее число запросов к сервису в секунду): 
| Страна         | Количество пользователей, % | RPS средний | RPS пиковый (х2) |
| -------------- | --------------------------- | ----------- | ---------------- |
| США            | 62,50                       | 1843        | 3686             |
| Великобритания | 3,92                        | 117         | 234              |
| Германия       | 1,61                        | 47          | 94               |
| Канада         | 1,56                        | 46          | 92               |
| Польша         | 1,54                        | 45          | 90               |
| Нидерланды     | 1,31                        | 38          | 76               |
| Другие         | 27,56                       | 812         | 1624             |

Более развернутой информации о списке стран ("Другие"), использующих сервис, в открытом доступе не нашлось.

Также известно процентное соотношение регионов, откуда были опубликованы отзывы в 2023 году[^6]: 
| Регион                      | Количество отзывов, % |
| --------------------------- | --------------------- |
| Европа                      | 51,86                 |
| Северная Америка            | 25,21                 |
| Азия                        | 11,50                 |
| Африка и Средний Восток     | 5,85                  |
| Центральная и Южная Америка | 5,58                  |

Итак, сервис пользуется наибольшей популярностью в Европе и в США. Расположим в этих регионах по 3 ДЦ, так как, хоть и большее число пользователей из США, отзывы публикуются чаще всего из Европы (активная аудитория).

Итак, ДЦ можно раположить в следующих городах (с учетом расположения IXPs[^11], коммуникационных кабелей[^12] и распределения населения в мире[^13]):
- США: Нью-Йорк, Сиэтл, Лос-Анджелес
- Великобритания: Манчестер
- Италия: Рим
- Россия: Москва
- Индия: Мумбаи
- ЮАР: Кейптаун (рядом с АЭС)
- Аргентина: Буэнос-Айрес (рядом с АЭС)

Точки расположения дата-центров отмечены на карте[^14]:
![data centers map](./images/dc_map.png)
![submarine cable map](./images/cabel_map.png)
![population map](./images/population_map.png)

* **DNS балансировка:**

Для балансировки выбрана Geo-based DNS, поскольку изначально точки размещения дата-центров подбирались с учетом плотности пользователей в различных регионах. Кроме того, GeoDNS дешевле и проще в поддержке, чем Latency-based DNS, так как не требует сложных механизмов мониторинга сетевых задержек.

* **Anycast балансировка:**

Для обеспечения отказоустойчивости внутри каждого региона (США, Европа, остальной мир) будет использоваться балансировка на уровне роутинга, которая позволит направлять трафик к доступному серверу в случае, если один из серверов в регионе окажется недоступен. 

- В США: Anycast между тремя серверами в Нью-Йорке, Сиэтле и Лос-Анджелесе.
- Аналогично, в зоне Европы: Anycast между серверами в Манчестере, Риме и Москве.
- В остальных регионах: Anycast между серверами в Мумбаи, Кейптауне, Буэнос-Айресе.

## Использованные источники
[^1]: [Tripadvisor First Quarter 2024 Investor Presentation](https://ir.tripadvisor.com/static-files/1b6bf47a-248a-40fe-840b-1bb9a4774ace)
[^2]: [60+ Tripadvisor Statistics, Facts, and Trends 2023](https://passport-photo.online/blog/tripadvisor-statistics/)
[^3]: [Tripadvisor - Wikipedia Ru](https://ru.wikipedia.org/wiki/Tripadvisor)
[^4]: [Tripadvisor - Wikipedia En](https://en.wikipedia.org/wiki/Tripadvisor)
[^5]: [Where is TripAdvisor Going? 39+ Signpost Statistics](https://review42.com/resources/tripadvisor-statistics/)
[^6]: [Tripadvisor Review Transparency Report 2023](https://www.tripadvisor.com/TransparencyReport2023)
[^7]: [Tripadvisor Second Quarter 2012 Investor Presentation](https://ir.tripadvisor.com/static-files/2d76119b-570c-4a3f-85ff-8836981d437c)
[^8]: [Tripadvisor Second Quarter 2013 Investor Presentation](https://ir.tripadvisor.com/static-files/eb1c9dd3-a362-4aad-8d31-8fa86e243ac5)
[^9]: [August 2024 Traffic Stats](https://www.semrush.com/website/tripadvisor.com/overview/)
[^10]: [TripAdvisor Statistics 2024](https://www.condorferries.co.uk/tripadvisor-statistics)
[^11]: [Internet Exchange Points](https://www.datacentermap.com/ixp/)
[^12]: [Submarine Cable Map](https://www.submarinecablemap.com/)
[^13]: [World Population Density](https://luminocity3d.org/WorldPopDen/#2/63.6/4.9)
[^14]: [Tripadvisor data center locations](https://yandex.ru/maps/?um=constructor%3Afb36150e32687cfb3ae6e0c3c75affbf3d70c184a5229660eb1410b4f58be426&source=constructorLink)
