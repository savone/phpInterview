# Распределенные БД



## Репликация и шардинг

- Master-Master
- Master-Slave
- Вертикальный и горизонтальный шардинг
- Инвалидация кеша при репликации
- Бинарный лог
- Консистентный хэш

## Теорема CAP

Теорема САР (известная также как теорема Брюера) — эвристическое утверждение о том, что в любой реализации распределённых вычислений возможно обеспечить не более двух из трёх следующих свойств:

- согласованность данных (англ. consistency) — во всех вычислительных узлах в один момент времени данные не противоречат друг другу;
- доступность (англ. availability) — любой запрос к распределённой системе завершается корректным откликом, однако без гарантии, что ответы всех узлов системы совпадают;
- устойчивость к разделению (англ. partition tolerance) — расщепление распределённой системы на несколько изолированных секций не приводит к некорректности отклика от каждой из секций.