# Web_interview_HL_Music_service
Задание по проектированию архитектуры высоконагруженной системы (Музыкального сервиса) для Образовательного центра VK
## Содержание

* ### [Введение](#1)
* ### [Схема архитектуры приложения](#2)
* ### [Технологии](#3)
* ### [Влияение целевого действия на архитектуру](#4)
* ### [Масштабирование и деградация](#5)
## 1. Введение <a name="1"></a>
VK Музыка — это один из самых популярных в СНГ сервисов для просулшивания музыки, предоставляемый социальной сетью ВКонтакте. В данном репозитории я попытался представить как могла бы выглядеть архитектура данного высоконагрженного сервиса.
## 2. Схема архитектуры приложения <a name="2"></a>
![image](https://github.com/user-attachments/assets/a31d0527-c077-47b9-9171-c1f0184272fc)
## 3. Технологии <a name="3"></a>
| Технология  | Область применения             | Обоснование                                                                                             |
|-------------|--------------------------------|---------------------------------------------------------------------------------------------------------|
| Go | Backend |Go и gRPC — это мощная комбинация для разработки высокопроизводительных и масштабируемых микросервисов. Go обеспечивает простоту, производительность и экосистему, необходимые для разработки микросервисов, а gRPC обеспечивает высокую производительность, строгую типизацию и поддержку множества языков для взаимодействия между микросервисами.|
| React.js| Frontend | Наиболее часто используемый фреймворк в связке с Go для создания пользовательских интерфейсов. Позволяет легко создавать динамические и интерактивные интерфейсы |
| Nginx| Frontend-web-server, Load Balancer | Nginx эффективно обслуживает статические файлы, такие как HTML, CSS, JavaScript, изображения и видео. Nginx может распределять входящие запросы между несколькими серверами, что очень сильно повышает производительность и надежность.            |
| Redis |Session Storage|Redis как хранилище сессий обеспечивает высокую производительность, масштабируемость, устойчивость, поддержку сложных структур данных, гибкость и автоматическое управление памятью. Это делает его одним из лучших выборов для хранения сессий в высоконагруженных и распределенных системах.|
|VK Cloude| Cloude Storage|Ввиду того что VK Cloude является продуктом VK, интегрирование данного облачного хранилища для музыки в VK Музыка будет более простым и дешевым|
|VK Cloude| Content Delivery Network|VK Cloud предлагает сервис CDN, который позволяет пользователям ускорить загрузку контента на своих веб-сайтах и приложениях, улучшить производительность и снизить нагрузку на исходные серверы. Это крайне необходимо для того чтобы музыка, которая является довольно тяжелым статическим файлом, загружалась с минимальной задержкой|
| Cassandra| DBMS| Cassandra — это распределенная NoSQL база данных, которая хорошо подходит для хранения больших объемов структурированных и полуструктурированных данных с высокой доступностью и масштабируемостью. В контексте VK Cloud, Cassandra может быть использована для хранения метаданных о музыке, таких как информация о треках, альбомах, исполнителях, жанрах и других атрибутах.  Ее распределенная архитектура, высокая доступность, масштабируемость и производительность делают ее идеальным выбором для систем, требующих обработки больших объемов данных с высокой скоростью и надежностью.|
| C++| Backend | C++ является отличным выбором для реализации рекомендательных систем благодаря своей высокой производительности, обеспечиваемой низкоуровневым доступом к ресурсам и возможностью точной оптимизации, а также масштабируемости, позволяющей эффективно работать с большими объемами данных и сложными алгоритмами.|
| MongoDB |DBMS|MongoDB обеспечивает гибкость в структуре данных, высокую доступность и масштабируемость, что позволяет эффективно хранить рекомендации сгенерированные моделью на C++|
| ClickHouse |Analytics|Столбчатая БД для сохранения данных для аналитики работы сервиса|
## 4. Влияение целевого действия на архитектуру <a name="4"></a>
  Ключевое действие для данного сервиса - это прослушивание музыки, а значит требуется, чтобы в первую очередь, данный процесс был бсытрый и бесперебойный. Для того чтобы хранить саму музыку используется Облачное хранилище, что может быть не самым надежным вариантом для такого рода задач. 
  Для решения проблемы испльзуется CDN (Content Delivery Network): 
  1. Быстрое воспроизведение
  2. Высокое качество звука
  3. Бесперебойная работа
  4. Экономия трафика
  5. Оптимизация на мобильных устройствах
## 5. Масштабирование и деградация <a name="5"></a>
  В случае повышения средней нагрзки на сервис требуется масштабирование. Это например может быть горизонтальное масштабирование путем увеличения количества серверов на микросервисы и установки дополнительных load balancer. Или вертикальное масштабирование, которое достигается увеличением мощностей уже существующих серверов. Так же из за относительно невыского покрытия целевой аудитории сервиса (пределы СНГ) можно рассмотреть географическое масштабирование, это может быть увеличение серверов для CDN или создание региональных центров обработки данных.
  Если на сервисе резкое возрастание нагрузки, вызванное например релизом ОЧЕНЬ популярного артиста, можно воспользоваться деградацией некоторых его компонентов. К примеру: 
  1. Отключение рекомендаций: Отключение динамических рекомендаций или снижение частоты их обновлений, которые требуют обработки большого объема данных,
  2. Ограничение доступа к истории прослушивания: Ограничение доступа к истории прослушивания или снижение частоты обновления данных
  3. Ограничение частоты обновления аналитики: Ограничение частоты обновления аналитических данных
  4. Отключение уведомлений для некоторых пользователей: Отключение уведомлений для пользователей с низким уровнем активности.
