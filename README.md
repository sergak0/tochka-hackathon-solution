# Предсказание видов эконоческой активности людей по данным их транзакций
[Хакатон](https://mlhack.tochka.tech/leaderboard), проводимый университетом ИТМО и банком Точка.

Первое место на привате и на паблике


![leaderboard](https://user-images.githubusercontent.com/44319901/170038957-383594ed-8e82-4070-b661-064c7799539f.jpg)

## Задача

> Вам предстоит по данным транзакционной активности клиентов предсказать виды их экономической деятельности.
>
> Фактически задача представляет собой multi-label classification на 35 категорий.
> 
> В качестве метрики используется micro-averaged F-beta score (beta=0.5). 



## Данные
В качестве данных давалась анонимизированная история транзакций клиентов банка.
![image](https://user-images.githubusercontent.com/44319901/170039961-53151f86-52e1-429b-9975-daf4ce84f518.png)

**Описание колонок**
* `client_id` - идентификатор клиента
* `contractor_id` - идентификатор контрагента. Тот, кому отправлены деньги, если транзакция исходящая, либо же тот, кто прислал деньги, если транзакция входящая. Может быть неизвестен.
* `is_outgoing` - направление транзакции. 0 - входящая, 1 - исходящая.
* `amount` - сумма транзакции.
* `dt_day` - порядковый номер дня, в который совершена транзакция. Начинается с нуля.
* `dt_hour` - час, в который совершена транзакция (0-23).
* `channel` - канал, по которому совершена транзакция. web/atm/pos/app. Может быть неизвестен.
* `flag_0-flag_11` - бинарный флаг (0, 1), присвоенный транзакции, описывающий какую-либо ее характеристику.

## Фичи, которые использовал

* Стандартные фичи для суммы транзакций - `.agg(['sum', 'median', 'count', 'std'])`
* Частота транзакций
* Сколько процентов транзакций проходило по какому каналу (app, web...)
* Медиана суммы денег, которая проходила по каждому каналу
* Среднее кол-во транзакций в день (если была сделана хотя бы одна транзакция)
* В качестве категориальной фичи использовались топ 10 contractor_id, которым отсылались деньги по кол-ву переводов
* В скольких процентах транзакций используется каждый флаг

Каждая из этих фичей расчитывалась отдельно для исходящих и входящих транзакций (is_outgoing=True/False)

## Модель

В качестве модели отдельно для каждого из 35 лейблов обучался catboost. Поизучав графики обучения кэтбуста, было замечено, что он практически не переобучается и модель на последней эпохе лучшая (ну или почти), поэтому в итоговом решение кэтбуст обучался на всем датасете и без валидации (это дало +0.8%)

![image](https://user-images.githubusercontent.com/44319901/170044019-c8dc8421-8144-47d2-88ef-2476c95a8764.png)
