# Домашнее задание к занятию 1.  «Введение в виртуализацию. Типы и функции гипервизоров. Обзор рынка вендоров и областей применения»

> 1. Опишите кратко, как вы поняли: в чем основное отличие полной (аппаратной) виртуализации, паравиртуализации и виртуализации на основе ОС.

Ответ:
- Аппаратная (полная) виртуализация - полная эмуляция процессора, памяти и необходимых перифирийных устройств, в силу чего большие накладные расходы, что может выливаться в уменьшение производительности. Однако данные проблемы отчасти закрываются динамической трансляцией и реализацией аппаратной виртуализации Inet-VT, AMD-V.
- Паравиртулизация - в том числе эмуляция работы устройств, однако гостевая ОС знает о наличии гипервизора, а выполнения привилегированных инструкций происходит через гипервизор, поэтому происходит серьезное изменение ядра гостевой ОС. В силу данной особенности подходит для ОС с открытым кодом, издержки меньше, производительность не особо страдает.
- Виртуализация на основе ОС - еще называется контейнеризация, при помощи механизмов namespace и cgroups, можно выделять новые пространства имен, ограничивать ресурсы хостовой машины, тем самым создавать изолированные контейнеры, в которых будут выполняться процессы. Ядро гостевой ОС не может отличаться от ядра хостовой ОС.



> 2. Выберите один из вариантов использования организации физических серверов, в зависимости от условий использования. Опишите, почему вы выбрали к каждому целевому использованию такую организацию.

Ответ:
1. Высоконагруженная база данных, чувствительная к отказу: виртуализация уровня ОС - так БД можно рассматривать, как приложение (сервис), по моему мнению необходимо запаковать это в контейнер, создать несколько таких контейнеров, тем самым получить отказоустойчивый кластер, управление контейнером сводится к управлению процессом - быстро и без накладных расходов.
2. Различные web-приложения: виртуализация уровня ОС - если приложения могут и должны работать на том же ядре, что и ядро хостовой ОС, если, все же, в связи с этим есть ограничения, тогда паравиртуализация. Так как это web-приложения, то это значит, что будет достаточно много обращений к ним, как к сервису, поэтому нам в первую очередь необходимо обеспечить хорошую производительность и снизить накладные расходы. Данный способ так же позволяет утилизировать по максимуму хостовые ресурсы, управление контейнерами позволит нам упростить в принципе управление стеком веб приложений. 
3. Windows системы для использования бухгалтерским отделом: паравиртуализация - системы для бухов - по большей части, не требуют от нас супер производительности и кол-во обращение более менее прогнозируемое и поддается подсчету. Виртуализация более масштабируемое решение, скорее всего можно сформировать единую точку управления.
4. Системы, выполняющие высокопроизводительные расчеты на GPU: физические сервера - так как потребуется установка мощных графических процессоров для обработки данных. Хотя, графические интерфейсы в том, числе виртуализируются, однако предполагаю, что все таки физические решения для графических вычислений более производительны.

П.С. Хотел бы отметить, что при выборе того или иного решения, необходимо проводить более глубокую аналитику - что за приложения будут выполняться, какие задачи будут решать новые сервисы, в том числе необходимо учитывать и анализировать не только страт проекта, но и делать выбор с учетом требования по отказоустойчивости и масштабируемости. Возможно было бы уместно для типовых задач проанализировать ИТ опыт, так сказать рассмотреть best practice. А также не мало важно учитывать бюджет проекта.

> 3. Выберите подходящую систему управления виртуализацией для предложенного сценария. Детально опишите ваш выбор.

Ответ: 

Сценарий 1: VMWare. В рамках продуктов виртуализации и управления, решения от VMWare являются наиболее сбалансированными, имеют достаточно обширный функционал, который позволит закрыть вопросы по сопровождению и администрированию среды на 100 VM Windows, Linux.

Сценарий 2: KVM. Решение на базе KVM позволит управлять гостевыми ОС, как Windows, так и Linux, без особых потерь в производительности, что обеспечивает решение поставленной задачи. Проект развивающийся, это несет в себе риски, но ими можно пренебречь, так как производительность нас интересует в первую очередь. 

Сценарий 3: XEN PV. Решение позволит поднять инфраструктуру на базе Windows OS, является open source проектом, обеспечит высокую производительность.

Сценарий 4: Virtual Box совместно с Vagrant. Для создания окружения для тестирования подойдет open source решение Virtual Box, с помощью Vagrant можно добиться автоматизации сборки такого окружения, при помощи Vagrantfile отдавать данное окружения всем, кто заинтересован, это позволит точно быть уверенным, что среда у всех единая.

> 4. Опишите возможные проблемы и недостатки гетерогенной среды виртуализации (использования нескольких систем управления виртуализацией одновременно) и что необходимо сделать для минимизации этих рисков и проблем. Если бы у вас был выбор, то создавали бы вы гетерогенную среду или нет? Мотивируйте ваш ответ примерами.

Ответ: выбор среды виртуализации делается на основании плотного пред проектного анализа, рассматривается детальное описание требований к будущей реализации, мое мнение такое, если мы можем на проекте избежать солянки из продуктов и решений, то ни к чему вводить два или более решений по управлению виртуальной средой. Однако, если же этого не избежать, специфика поставленной задачи, такова, что гетерогенная среда будет оправдана, и с точки зрения затрат, и с точки зрения управления, то тогда необходимо внедрять такое решение, но учитывать особенности - две или более точки управления виртуальными средами, это в свою очередь тянет за собой и необходимость привлекать более скиловый персонал, объем выполняемых работ увеличиться, потребуется разработка автоматизационных решений под обе среды, возможно это наложит существенные ограничения на выбор стека технологий по мониторингу, логированию и бекапированию или наоборот увеличит кол-во продуктов. В том числе значительно усложняется вопрос по реализации прозрачного процесса управления изменениями, то есть изменения, которые будут доставляться в одну среду, могут отличаться в смысле способа и метода доставки в другой среде. Для минимизации влияния всех описаных факторов, техническое решение должно быть проработано хорошо, задача отладки процессов управления сервисов в гетерогенной среде становиться крайне важна, в том числе для страховки необходимо будет прорабатывать детальный план DRP - disaster recovery plan. По моему личному мнению, гетерогенная среда виртуализации - требует определенного баланса между необходмиым и достаточным, необходимо отдавать себе отчет в том, какой стек технологий будет использоваться, и точно не стоит боятся, необходимо ответственно подходить к архитектуре решения. 
