# Задача 4

[![](https://dan4i4ek.info/src/58.html)

Схема: Общая схема сети  
Условие: ЛинкМиАп получает Full View от обоих провайдеров.

Тема: Поиск неисправностей.  
От провайдеров: полная таблица маршрутов BGP

На маршрутизаторе msk-arbat-gw1 настроено распределение исходящего трафика между провайдерами Балаган Телеком и Филькин Сертификат. Трафик идущий в сети провайдера Филькин Сертификат, должен идти через него, если он доступен. Остальной исходящий трафик, должен передаваться через провайдера Балаган Телеком, когда он доступен.  
При проверке исходящего трафика, оказалось, что при отключении Балаган Телеком, исходящий трафик к ЦОД \(103.0.0.1\) не идет через Филькин Сертификат.

Задание:  
Исправить настройки так, чтобы исходящий трафик в сети провайдера ISP2, к его клиенту и в сеть удаленного офиса компании, шел через провайдера ISP2.

Подробности задачи и конфигурация [тут](https://linkmeup.ru/blog/58.html)

