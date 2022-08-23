# RSTP

Все, о чем мы говорили ранее в этой статье, относится к первой реализация протокола STP, которая была разработана в 1985 году Радией Перлман \(ее стихотворение использовано в качестве эпиграфа\). В 1990 году эта реализации была включена в стандарт IEEE 802.1D. Тогда время текло медленнее, и перестройка топологии STP, занимающая 30-50 секунд \(!!!\), всех устраивала. Но времена меняются, и через десять лет, в 2001 году, IEEE представляет новый стандарт **RSTP** \(он же 802.1w, он же Rapid Spanning Tree Protocol, он же Быстрый STP\).

Чтобы структурировать предыдущий материал и посмотреть различия между обычным STP \(802.1d\) и RSTP \(802.1w\), соберем таблицу с основными фактами:

<table>
  <thead>
    <tr>
      <th style="text-align:left"><b>STP (802.1d)</b>
      </th>
      <th style="text-align:left"><b>RSTP (802.1w)</b>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">
        <p>В уже сложившейся топологии только корневой свич шлет BPDU,</p>
        <p>остальные ретранслируют</p>
      </td>
      <td style="text-align:left">Все свичи шлют BPDU в соответствии с hello-таймером (2 секунды по умолчанию)</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Состояния портов</b>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left">
        <ul>
          <li>blocking (блокировка)</li>
          <li>listening (прослушивание)</li>
          <li>learning (обучение)</li>
          <li>forwarding (перенаправление/пересылка)</li>
          <li>disabled (отключен)</li>
        </ul>
      </td>
      <td style="text-align:left">
        <ul>
          <li>discarding (отбрасывание, заменяет disabled, blocking и listening)</li>
          <li>learning</li>
          <li>forwarding</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Роли портов</b>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left">
        <ul>
          <li>root (корневой), участвует в пересылке данных, ведет к корневому свичу</li>
          <li>designated (назначенный), тоже работает, ведет от корневого свича</li>
          <li>non-designated (неназначенный), не участвует в пересылке данных</li>
        </ul>
      </td>
      <td style="text-align:left">
        <ul>
          <li>root (корневой), участвует в пересылке данных</li>
          <li>designated (назначенный), тоже работает</li>
          <li>alternate (дополнительный), не участвует в пересылке данных</li>
          <li>backup (резервный), тоже не участвует</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Механизмы работы</b>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p>Использует таймеры:</p>
        <ul>
          <li>Hello (2 секунды)</li>
          <li>Max Age (20 секунд)</li>
          <li>Forward delay timer (15 секунд)</li>
        </ul>
      </td>
      <td style="text-align:left">Использует процесс Proposal and Agreement (предложение и соглашение)</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p>Свич, обнаруживший изменение топологии, извещает корневой свич.</p>
        <p>Тот, в свою очередь, требует от всех остальных очистить их записи</p>
        <p>о текущей топологии в течение forward delay timer</p>
      </td>
      <td style="text-align:left">Обнаружение изменений в топологии влечет немедленную очистку записей</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p>Если некорневой свич не получает hello-пакеты от корневого в течение Max Age,</p>
        <p>он начинает новые выборы</p>
      </td>
      <td style="text-align:left">Начинает действовать, если не получает BPDU в течение 3 hello-интервалов</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p>Последовательное прохождение порта через состояния:</p>
        <ul>
          <li>blocking (20 сек)</li>
          <li>listening (15 сек)</li>
          <li>learning (15 сек)</li>
          <li>forwarding</li>
        </ul>
      </td>
      <td style="text-align:left">Быстрый переход к forwarding для p2p и Edge-портов</td>
    </tr>
  </tbody>
</table>

Как мы видим, в RSTP остались такие роли портов, как корневой и назначенный, а роль заблокированного разделили на две новых роли: Alternate и Backup. Alternate — это резервный корневой порт, а backup — резервный назначенный порт. Как раз в этой концепции резервных портов и кроется одна из причин быстрого переключения в случае отказа. Это меняет поведение системы в целом: вместо реактивной \(которая начинает искать решение проблемы только после того, как она случилась\) система становится проактивной, заранее просчитывающей “пути отхода” еще до появления проблемы. Смысл простой: для того, чтобы в случае отказа основного переключится на резервный линк, RSTP не нужно заново просчитывать топологию, он просто переключится на запасной, заранее просчитанный.

Ранее, для того, чтобы убедиться, что порт может участвовать в передаче данных, требовались таймеры, т.е. свич пассивно ждал в течение означенного времени, слушая BPDU. Ключевой фичей RSTP стало введение концепции типов портов, основанных на режиме работы линка — full duplex или half duplex \(типы портов p2p или shared, соответственно\), а также понятия пограничный порт \(тип edge p2p\), для конечных устройств. Пограничные порты назначаются, как и раньше, командой spanning-tree portfast, и с ними все понятно — при включении провода сразу переходим к forwarding-состоянию и работаем. Shared-порты работают по старой схеме с прохождением через состояния BLK — LIS — LRN — FWD. А вот на p2p-портах RSTP использует процесс предложения и соглашения \(proposal and agreement\). Не вдаваясь в [подробности,](http://blog.ine.com/2009/09/07/rstp-and-fast-convergence/) его можно описать так: свич справедливо считает, что если линк работает в режиме полного дуплекса, и он не обозначен, как пограничный, значит, на нем только два устройства- он и другой свич. Вместо того, чтобы ждать входящих BPDU, он сам пытается связаться со свичом на том конце провода с помощью специальных proposal BPDU, в которых, конечно, есть информация о стоимости маршрута к корневому свичу. Второй свич сравнивает полученную информацию со своей текущей, и принимает решение, о чем извещает первый свич посредством agreement BPDU. Так как весь этот процесс теперь не привязан к таймерам, происходит он очень быстро, только подключили новый свич и он практически сразу вписался в общую топологию и приступил к работе \(можете сами оценить скорость переключения в сравнении с обычным STP на видео\). В Cisco-мире RSTP называется PVRST \(Per-Vlan Rapid Spanning Tree\).
