# Задача 8

[![](https://dan4i4ek.info/src/67.html)

Условие: ЛинкМиАп использует статические маршруты к провайдерам \(не BGP\).  
[Схема и конфигурация](https://docs.google.com/document/d/1bH7FdkcQ4HYRDYKph5qp0mC_xBbMnlPL1ETlTxB2s_E/pub). Маршрутизаторы провайдеров также не используют BGP.

Задание: Настроить переключение между провайдерами.  
Маршрут по умолчанию к Балаган Телеком должен использоваться до тех пор, пока приходят icmp-ответы на пинг google \(103.0.0.10\) ИЛИ yandex \(103.0.0.20\). Запросы должны отправляться через Балаган Телеком. Если ни один из указанных ресурсов не отвечает, маршрут по умолчанию должен переключиться на провайдера Филькин Сертификат. Для того чтобы переключение не происходило из-за временной потери отдельных icmp-ответов, необходимо установить задержку переключения, как минимум, 5 секунд.

Подробности задачи [тут](https://linkmeup.ru/blog/67.html)

