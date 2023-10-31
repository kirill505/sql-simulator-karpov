# Lesson 1 - Dashboards creating

Module: Module 2

### Exercise 1

Для начала давайте проанализируем, насколько быстро растёт аудитория нашего сервиса, и посмотрим на динамику числа пользователей и курьеров.

---

**Задание:**

Для каждого дня, представленного в таблицах `user_actions` и `courier_actions`, рассчитайте следующие показатели:

1. Число новых пользователей.
2. Число новых курьеров.
3. Общее число пользователей на текущий день.
4. Общее число курьеров на текущий день.

Колонки с показателями назовите соответственно `new_users`, `new_couriers`, `total_users`, `total_couriers`. Колонку с датами назовите `date`. Проследите за тем, чтобы показатели были выражены целыми числами. Результат должен быть отсортирован по возрастанию даты.

Поля в результирующей таблице: `date`, `new_users`, `new_couriers`, `total_users`, `total_couriers`

---

**Пояснение:**

Новыми будем считать тех пользователей и курьеров, которые в данный день совершили своё первое действие в нашем сервисе. Общее число пользователей/курьеров на текущий день — это результат сложения числа новых пользователей/курьеров в текущий день со значениями аналогичного показателя всех предыдущих дней.

После того как составите запрос, попробуйте визуализировать результаты и постройте графики, отражающие динамику рассчитанных показателей.

Предлагаем вам построить два графика: на один поместить динамику новых пользователей и курьеров, а на второй — рост их общего числа.

---

**Примеры графиков:**

Динамика новых пользователей и курьеров:

![https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269842/New%20Users%20and%20Couriers%20by%20Date_qxp0WWB.png](https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269842/New%20Users%20and%20Couriers%20by%20Date_qxp0WWB.png)

---

Динамика общего числа пользователей:

![https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269842/Total%20Users%20and%20Couriers%20Growth_te91Tfu.png](https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269842/Total%20Users%20and%20Couriers%20Growth_te91Tfu.png)

---

Проанализируйте построенные графики и попробуйте ответить на следующие вопросы:

1. Что растёт быстрее: количество пользователей или количество курьеров?
2. Насколько стабильны показатели числа новых пользователей и курьеров? Нет ли в данных таких дней, когда показатели сильно выбивались из общей динамики?
3. Можно ли сказать, что показатель числа новых курьеров более стабилен, чем показатель числа новых пользователей?

```sql
SELECT first_event as date, new_users, new_couriers,
    SUM(new_users) OVER(ORDER BY first_event)::int as total_users,
    SUM(new_couriers) OVER(ORDER BY first_event)::int as total_couriers
FROM (
    SELECT first_event,
        COUNT(DISTINCT user_id) as new_users
    FROM (
        SELECT user_id,
            MIN(time::date) as first_event
        FROM user_actions
        GROUP BY user_id
        ) t1
    GROUP BY first_event
    ) t2
JOIN (
    SELECT first_event,
        COUNT(DISTINCT courier_id) as new_couriers
    FROM (
        SELECT courier_id,
            MIN(time::date)as first_event
        FROM courier_actions
        GROUP BY courier_id
        ) t3
    GROUP BY first_event
    ) t4
USING(first_event)
```

### Exercise 2

Анализируя динамику показателей из предыдущего задания, вы могли заметить, что сравнивать абсолютные значения не очень удобно. Давайте посчитаем динамику показателей в относительных величинах.

---

**Задание:**

Дополните запрос из предыдущего задания и теперь для каждого дня, представленного в таблицах `user_actions` и `courier_actions`, дополнительно рассчитайте следующие показатели:

1. Прирост числа новых пользователей.
2. Прирост числа новых курьеров.
3. Прирост общего числа пользователей.
4. Прирост общего числа курьеров.

Показатели, рассчитанные на предыдущем шаге, также включите в результирующую таблицу.

Колонки с новыми показателями назовите соответственно `new_users_change`, `new_couriers_change`, `total_users_growth`, `total_couriers_growth`. Колонку с датами назовите `date`.

Все показатели прироста считайте в процентах относительно значений в предыдущий день. При расчёте показателей округляйте значения до двух знаков после запятой.

Результирующая таблица должна быть отсортирована по возрастанию даты.

Поля в результирующей таблице:

`date`, `new_users`, `new_couriers`, `total_users`, `total_couriers`,

`new_users_change`, `new_couriers_change`, `total_users_growth`, `total_couriers_growth`

---

**Пояснение:**

Не забывайте при делении заранее приводить значения к нужному типу данных. Пропущенные значения приростов для самой первой даты не заполняйте — просто оставьте поля в этой строке пустыми.

---

**Ожидаемый результат**

---

**Подсказка на случай, если совсем не получается**

---

После того как составите запрос, попробуйте визуализировать результаты и постройте графики, отражающие динамику рассчитанных показателей.

Предлагаем вам снова построить два графика: на один поместить приросты числа новых пользователей и курьеров, а на второй — приросты общего числа пользователей и курьеров. В данном случае для визуализации рекомендуем использовать столбиковые диаграммы (барчарты). Чтобы создать такую диаграмму, необходимо выбрать Bar в поле Chart Type.

---

**Примеры графиков:**

Динамика прироста числа новых пользователей и курьеров:

![https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269843/New%20Users%20and%20Couriers%20Change%20by%20Date.png](https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269843/New%20Users%20and%20Couriers%20Change%20by%20Date.png)

---

Динамика прироста общего числа пользователей и курьеров:

![https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269843/Total%20Users%20and%20Couriers%20Growth%20by%20Date.png](https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269843/Total%20Users%20and%20Couriers%20Growth%20by%20Date.png)

---

Проанализируйте построенные графики и попробуйте ответить на следующие вопросы:

1. Как изменились темпы прироста общего числа пользователей и курьеров за рассматриваемый промежуток времени? Какая в целом динамика у этих показателей: они растут или, наоборот, затухают?
2. В какие дни темп прироста числа новых курьеров заметно опережал темп прироста числа новых пользователей?
3. Можно ли, глядя на графики с относительными показателями, сказать, что показатель числа новых курьеров более стабилен, чем показатель числа новых пользователей?

```sql
SELECT date, new_users, new_couriers, total_users, total_couriers,
    ROUND(100*(new_users-lag(new_users) OVER(ORDER BY date))::numeric/lag(new_users) OVER(ORDER BY date), 2) as new_users_change,
    ROUND(100*(new_couriers-lag(new_couriers) OVER(ORDER BY date))::numeric/lag(new_couriers) OVER(ORDER BY date), 2) as new_couriers_change, 
    ROUND(100*(total_users-lag(total_users) OVER(ORDER BY date))::numeric/lag(total_users) OVER(ORDER BY date), 2) as total_users_growth, 
    ROUND(100*(total_couriers-lag(total_couriers) OVER(ORDER BY date))::numeric/lag(total_couriers) OVER(ORDER BY date), 2) as total_couriers_growth
FROM (
    SELECT first_event as date, new_users, new_couriers,
        SUM(new_users) OVER(ORDER BY first_event)::int as total_users,
        SUM(new_couriers) OVER(ORDER BY first_event)::int as total_couriers
    FROM (
        SELECT first_event,
            COUNT(DISTINCT user_id) as new_users
        FROM (
            SELECT user_id,
                MIN(time::date) as first_event
            FROM user_actions
            GROUP BY user_id
            ) t1
        GROUP BY first_event
        ) t2
    JOIN (
        SELECT first_event,
            COUNT(DISTINCT courier_id) as new_couriers
        FROM (
            SELECT courier_id,
                MIN(time::date)as first_event
            FROM courier_actions
            GROUP BY courier_id
            ) t3
        GROUP BY first_event
        ) t4
    USING(first_event)
    ) t5
```

### Exercise 3

Теперь предлагаем вам посмотреть на нашу аудиторию немного под другим углом — давайте посчитаем не просто всех пользователей, а именно ту часть, которая оформляет и оплачивает заказы в нашем сервисе. Заодно выясним, какую долю платящие пользователи составляют от их общего числа.

---

**Задание:**

Для каждого дня, представленного в таблицах `user_actions` и `courier_actions`, рассчитайте следующие показатели:

1. Число платящих пользователей.
2. Число активных курьеров.
3. Долю платящих пользователей в общем числе пользователей на текущий день.
4. Долю активных курьеров в общем числе курьеров на текущий день.

Колонки с показателями назовите соответственно `paying_users`, `active_couriers`, `paying_users_share`, `active_couriers_share`. Колонку с датами назовите `date`. Проследите за тем, чтобы абсолютные показатели были выражены целыми числами. Все показатели долей необходимо выразить в процентах. При их расчёте округляйте значения до двух знаков после запятой.

Результат должен быть отсортирован по возрастанию даты.

Поля в результирующей таблице: `date`, `paying_users`, `active_couriers`, `paying_users_share`, `active_couriers_share`

---

**Пояснение:**

Платящими будем считать тех пользователей, которые в данный день оформили хотя бы один заказ, который в дальнейшем не был отменен.

Курьеров будем считать активными, если в данный день они приняли хотя бы один заказ, который был доставлен (возможно, уже на следующий день), или доставили любой заказ.

Общее число пользователей/курьеров на текущий день — это по-прежнему результат сложения числа новых пользователей/курьеров в текущий день со значениями аналогичного показателя всех предыдущих дней. Мы считали этот показатель на предыдущих шагах.

Не забывайте при делении заранее приводить значения к нужному типу данных.

---

**Ожидаемый результат**

---

**Подсказка на случай, если совсем не получается**

---

После того как составите запрос, попробуйте визуализировать результаты и постройте графики, отражающие динамику рассчитанных показателей.

Предлагаем вам построить два графика: на один поместить динамику платящих пользователей и активных курьеров, а на второй — доли платящих пользователей и активных курьеров в их общем числе.

---

**Примеры графиков:**

Динамика платящих пользователей и активных курьеров:

![https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269881/Paying%20Users%20and%20Active%20Couriers.png](https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269881/Paying%20Users%20and%20Active%20Couriers.png)

---

Динамика долей платящих пользователей и активных курьеров:

![https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269881/Paying%20Users%20and%20Active%20Couriers%20Share.png](https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269881/Paying%20Users%20and%20Active%20Couriers%20Share.png)

---

Проанализируйте построенные графики и попробуйте ответить на следующие вопросы:

1. Можно ли сказать, что вместе с общим числом пользователей и курьеров растёт число платящих пользователей и активных курьеров?
2. Как в то же время ведут себя показатели долей платящих пользователей и активных курьеров? Можно ли считать их текущую динамику в целом нормальной и закономерной?

```sql
WITH paying_users as (
    SELECT time::date as date,
        COUNT(DISTINCT user_id) as paying_users
    FROM user_actions
    WHERE order_id not in (SELECT order_id FROM user_actions WHERE action='cancel_order')
    GROUP BY date
    ),
active_couriers as (
    SELECT time::date as date,
        COUNT(DISTINCT courier_id) as active_couriers
    FROM courier_actions
    WHERE order_id not in (SELECT order_id FROM user_actions WHERE action='cancel_order')
    GROUP BY date
    )

SELECT date, paying_users, active_couriers,
    ROUND(100*paying_users::decimal/total_users, 2) as paying_users_share,
    ROUND(100*active_couriers::decimal/total_couriers, 2) as active_couriers_share
FROM (
    SELECT date, paying_users, active_couriers,
        SUM(new_users) OVER(ORDER BY date)::int as total_users,
        SUM(new_couriers) OVER(ORDER BY date)::int as total_couriers
    FROM (
            SELECT date,
                COUNT(DISTINCT user_id) as new_users
            FROM (
                SELECT user_id,
                    MIN(time::date) as date
                FROM user_actions
                GROUP BY user_id
                ) t1
            GROUP BY date
        ) t2
    JOIN paying_users USING (date)
    JOIN (
        SELECT date,
            COUNT(DISTINCT courier_id) as new_couriers
        FROM (
            SELECT courier_id,
                MIN(time::date)as date
            FROM courier_actions
            GROUP BY courier_id
            ) t3
        GROUP BY date
        ) t4
    JOIN active_couriers USING(date)
    USING(date)
    ) t5
```

### Exercise 4

Давайте подробнее остановимся на платящих пользователях, копнём немного глубже и выясним, как много платящих пользователей совершают более одного заказа в день. В конце концов нам важно понимать, как в большинстве своём ведут себя наши пользователи — они заходят в приложение, чтобы сделать всего один заказ, или же наш сервис настолько хорош, что они готовы пользоваться им несколько раз в день.

---

**Задание:**

Для каждого дня, представленного в таблице `user_actions`, рассчитайте следующие показатели:

1. Долю пользователей, сделавших в этот день всего один заказ, в общем количестве платящих пользователей.
2. Долю пользователей, сделавших в этот день несколько заказов, в общем количестве платящих пользователей.

Колонки с показателями назовите соответственно `single_order_users_share`, `several_orders_users_share`. Колонку с датами назовите `date`. Все показатели с долями необходимо выразить в процентах. При расчёте долей округляйте значения до двух знаков после запятой.

Результат должен быть отсортирован по возрастанию даты.

Поля в результирующей таблице: `date`, `single_order_users_share`, `several_orders_users_share`

---

**Пояснение:**

Платящими по-прежнему считаем тех пользователей, которые в данный день оформили (и не отменили) хотя бы один заказ.

Не забывайте при делении заранее приводить значения к нужному типу данных.

---

**Ожидаемый результат**

---

**Подсказка на случай, если совсем не получается**

---

После того как составите запрос, попробуйте визуализировать результаты и постройте график, отражающий динамику рассчитанных показателей.

Поскольку в сумме оба показателя дают 100%, рекомендуем использовать столбчатую диаграмму. Чтобы столбцы были расположены друг над другом, во вкладке General в разделе Stacking можно указать параметр Stack.

---

**Пример графика:**

Доли пользователей с одним и несколькими заказами:

![https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269886/Single%20Order%20Users%20and%20Several%20Orders%20Users%20Share.png](https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269886/Single%20Order%20Users%20and%20Several%20Orders%20Users%20Share.png)

---

Проанализируйте построенные графики и попробуйте ответить на следующие вопросы:

1. На каком уровне в среднем держится доля пользователей с несколькими заказами?
2. Можно ли считать значение показателя в первый день аномально низким, если принять во внимание общее количество пользователей в этот день?

```sql
WITH paying_users as (
    SELECT time::date as date,
        COUNT(DISTINCT user_id) as paying_users
    FROM user_actions
    WHERE order_id not in (SELECT order_id FROM user_actions WHERE action='cancel_order')
    GROUP BY date
),
active_couriers as (
    SELECT time::date as date,
        COUNT(DISTINCT courier_id) as active_couriers
    FROM courier_actions
    WHERE order_id not in (SELECT order_id FROM user_actions WHERE action='cancel_order')
    GROUP BY date
),
new_users as (
    SELECT date,
        COUNT(DISTINCT user_id) as new_users
    FROM (
        SELECT user_id,
            MIN(time::date) as date
        FROM user_actions
        GROUP BY user_id
        ) t1
    GROUP BY date
),
new_couriers as (
    SELECT date,
            COUNT(DISTINCT courier_id) as new_couriers
    FROM (
        SELECT courier_id,
            MIN(time::date)as date
        FROM courier_actions
        GROUP BY courier_id
        ) t3
    GROUP BY date
),
orders_per_day as (
    SELECT date, 
        SUM(single_order) as single_orders, SUM(several_order) as several_orders
    FROM (
        SELECT date,
            COUNT(DISTINCT user_id) FILTER(WHERE order_count=1)::int as single_order,
            COUNT(DISTINCT user_id) FILTER(WHERE order_count>1)::int as several_order
        FROM (
            SELECT MIN(time::date) as date, user_id,
                    COUNT(user_id) as order_count
            FROM user_actions
            where order_id not in (select order_id
                                     from user_actions
                                     where action = 'cancel_order')
            GROUP BY time::date, user_id
            ) t1
        GROUP BY date, user_id, order_count
        ORDER BY date
        ) t2
    GROUP BY date
)

SELECT date,
    ROUND(100*single_orders::decimal/paying_users, 2) as single_order_users_share,
    (100 - ROUND(100*single_orders::decimal/paying_users, 2)) as several_orders_users_share
FROM (
    SELECT date, paying_users, single_orders, several_orders,
        SUM(new_users) OVER(ORDER BY date) as total_users
    FROM new_users
    JOIN paying_users USING (date)
    JOIN orders_per_day USING (date)
    ) t5
```

### Exercise 5

Продолжим изучать наш сервис и рассчитаем несколько показателей, связанных с заказами.

---

**Задание:**

Для каждого дня, представленного в таблице `user_actions`, рассчитайте следующие показатели:

1. Общее число заказов.
2. Число первых заказов (заказов, сделанных пользователями впервые).
3. Число заказов новых пользователей (заказов, сделанных пользователями в тот же день, когда они впервые воспользовались сервисом).
4. Долю первых заказов в общем числе заказов (долю п.2 в п.1).
5. Долю заказов новых пользователей в общем числе заказов (долю п.3 в п.1).

Колонки с показателями назовите соответственно `orders`, `first_orders`, `new_users_orders`, `first_orders_share`, `new_users_orders_share`. Колонку с датами назовите `date`. Проследите за тем, чтобы во всех случаях количество заказов было выражено целым числом. Все показатели с долями необходимо выразить в процентах. При расчёте долей округляйте значения до двух знаков после запятой.

Результат должен быть отсортирован по возрастанию даты.

Поля в результирующей таблице: `date`, `orders`, `first_orders`, `new_users_orders`, `first_orders_share`, `new_users_orders_share`

---

**Пояснение:**

При расчёте числа первых заказов учитывайте, что у каждого пользователя может быть всего один первый заказ (что вполне логично).

При расчёте числа заказов новых пользователей учитывайте, что в свой первый день каждый новый пользователь мог как совершить сразу несколько заказов, так и не совершить ни одного.

Во всех случаях при расчёте числа заказов учитывайте только фактически совершённые заказы, отменённые заказы не учитывайте.

Не забывайте при делении заранее приводить значения к нужному типу данных.

---

**Ожидаемый результат**

---

**Подсказка на случай, если совсем не получается**

---

После того как составите запрос, попробуйте визуализировать результаты и постройте графики, отражающие динамику рассчитанных показателей.

---

**Примеры графиков:**

Динамика общего числа заказов, числа первых заказов и числа заказов новых пользователей:

![https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269880/Orders%2C%20First%20Orders%20and%20Orders%20From%20New%20Users.png](https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269880/Orders%2C%20First%20Orders%20and%20Orders%20From%20New%20Users.png)

---

Динамика доли первых заказов и доли заказов новых пользователей в общем числе заказов:

![https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269880/First%20Orders%20and%20Orders%20From%20New%20Users%20Share%2C%20%25.png](https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269880/First%20Orders%20and%20Orders%20From%20New%20Users%20Share%2C%20%25.png)

---

Проанализируйте построенные графики и попробуйте ответить на следующие вопросы:

1. Какая в целом динамика у абсолютных показателей? Можно ли сказать, что вместе с ростом количества всех заказов растут показатели числа первых заказов и числа заказов новых пользователей?
2. Что можно сказать о динамике относительных показателей? Можно ли считать её в целом закономерной? Как, на ваш взгляд, будут вести себя эти показатели в долгосрочной перспективе: они будут расти или снижаться?

```sql
WITH total_orders as (
    SELECT time::date as date,
        COUNT(user_id) as orders
    FROM user_actions
    WHERE order_id not in (SELECT order_id FROM user_actions WHERE action='cancel_order')
    GROUP BY time::date
),
first_order as (
    SELECT date,
        COUNT(DISTINCT user_id) as first_orders
    FROM (
        SELECT user_id,
            MIN(time::date) as date
        FROM user_actions
        WHERE order_id not in (SELECT order_id FROM user_actions WHERE action='cancel_order')
        GROUP BY user_id
        ) t1
    GROUP BY date
),
first_action as (
    SELECT user_id,
        MIN(time::date) as date
    FROM user_actions
    GROUP BY user_id
),
new_users_order as (
    SELECT user_actions.time::date as date, 
        COUNT(DISTINCT user_actions.order_id) as new_users_orders
    FROM user_actions
    JOIN first_action
    ON (first_action.user_id=user_actions.user_id)
    WHERE order_id not in (SELECT order_id FROM user_actions WHERE action='cancel_order')
        AND user_actions.time::date=first_action.date
    GROUP BY user_actions.time::date
)

SELECT date, orders, first_orders, new_users_orders,
    ROUND(100*first_orders::decimal/orders, 2) as first_orders_share,
    ROUND(100*new_users_orders::decimal/orders, 2) as new_users_orders_share
FROM (
    SELECT date, orders, first_orders, new_users_orders
    FROM total_orders
    JOIN first_order USING (date)
    JOIN new_users_order USING (date)
    ) t5
ORDER BY date
```

### Exercise 6

Теперь давайте попробуем примерно оценить нагрузку на наших курьеров и узнаем, сколько в среднем заказов и пользователей приходится на каждого из них.

---

**Задание:**

На основе данных в таблицах `user_actions`, `courier_actions` и `orders` для каждого дня рассчитайте следующие показатели:

1. Число платящих пользователей на одного активного курьера.
2. Число заказов на одного активного курьера.

Колонки с показателями назовите соответственно `users_per_courier` и `orders_per_courier`. Колонку с датами назовите `date`. При расчёте показателей округляйте значения до двух знаков после запятой.

Результирующая таблица должна быть отсортирована по возрастанию даты.

Поля в результирующей таблице: `date`, `users_per_courier`, `orders_per_courier`

---

**Пояснение:**

Платящими по-прежнему считаем тех пользователей, которые в данный день оформили хотя бы один заказ, который в дальнейшем не был отменен.

Курьеров считаем активными, если в данный день они приняли хотя бы один заказ, который был доставлен (возможно, уже на следующий день), или доставили любой заказ.

В расчётах учитывайте только неотменённые заказы.

Не забывайте при делении заранее приводить значения к нужному типу данных.

---

**Ожидаемый результат**

---

**Подсказка на случай, если совсем не получается**

---

После того как составите запрос, попробуйте визуализировать результаты и постройте графики, отражающие динамику рассчитанных показателей.

Можете поместить оба показателя на один график или сделать два разных.

---

**Пример графика:**

Динамика числа пользователей и заказов на одного курьера:

![https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269844/Users%20and%20Orders%20per%20Courier.png](https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269844/Users%20and%20Orders%20per%20Courier.png)

---

Проанализируйте построенные графики и попробуйте ответить на следующие вопросы:

1. Совпадает ли в целом динамика рассматриваемых показателей? Если да, то почему так происходит?
2. Как изменяются рассматриваемые показатели? Они скорее растут или, наоборот, падают? О чём может говорить такая динамика?
3. Как вы считаете, достаточно ли высокая нагрузка у курьеров нашего сервиса? Стоит ли сервису продолжать увеличивать количество курьеров или, наоборот, сейчас лучше приостановить наращивание их численности?

```sql
WITH paying_users as (
    SELECT time::date as date,
        COUNT(DISTINCT user_id) as paying_users
    FROM user_actions
    WHERE order_id not in (SELECT order_id FROM user_actions WHERE action='cancel_order')
    GROUP BY date
),
active_couriers as (
    SELECT time::date as date,
        COUNT(DISTINCT courier_id) as active_couriers
    FROM courier_actions
    WHERE order_id not in (SELECT order_id FROM user_actions WHERE action='cancel_order')
    GROUP BY date
),
total_orders as (
    SELECT creation_time::date as date,
        COUNT(order_id) as total_order
    FROM orders
    WHERE order_id not in (SELECT order_id FROM user_actions WHERE action='cancel_order')
    GROUP BY creation_time::date
    
)

SELECT paying_users.date,
    ROUND(paying_users::decimal/active_couriers, 2) as users_per_courier,
    ROUND(total_order::decimal/active_couriers, 2) as orders_per_courier
FROM paying_users
JOIN active_couriers ON paying_users.date=active_couriers.date
JOIN total_orders ON paying_users.date=total_orders.date
ORDER BY date
```

### Exercise 7

Давайте рассчитаем ещё один полезный показатель, характеризующий качество работы курьеров.

---

**Задание:**

На основе данных в таблице `courier_actions` для каждого дня рассчитайте, за сколько минут в среднем курьеры доставляли свои заказы.

Колонку с показателем назовите `minutes_to_deliver`. Колонку с датами назовите `date`. При расчёте среднего времени доставки округляйте количество минут до целых значений. Учитывайте только доставленные заказы, отменённые заказы не учитывайте.

Результирующая таблица должна быть отсортирована по возрастанию даты.

Поля в результирующей таблице: `date`, `minutes_to_deliver`

---

**Пояснение:**

Для решения задачи вам, возможно, придётся вспомнить, как определить количество минут, содержащихся в интервале времени. Мы уже решали похожую задачу [на этом шаге](https://lab.karpov.courses/learning/152/module/1762/lesson/17930/53221/260807/).

Некоторые заказы оформляют в один день, а доставляют уже на следующий. При расчёте среднего времени доставки в качестве дней, для которых считать среднее, используйте дни фактической доставки заказов.

---

**Ожидаемый результат**

---

**Подсказка на случай, если совсем не получается**

---

После того как составите запрос, попробуйте визуализировать результаты и постройте график, отражающий динамику рассчитанного показателя.

---

**Пример графика:**

Динамика среднего времени доставки заказов:

![https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269846/Average%20Delivery%20Time_lQb5YZ4.png](https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269846/Average%20Delivery%20Time_lQb5YZ4.png)

---

Проанализируйте построенный график и попробуйте ответить на следующий вопрос:

Какое, скорее всего, время ожидания доставки заказа в нашем сервисе? Получается ли у курьеров придерживаться этого целевого показателя?

```sql
SELECT date, AVG(extract(epoch FROM AGE(deliver_order_time, accept_order_time))/60)::int as minutes_to_deliver
FROM (
    SELECT time::date as date, order_id, time, action,
        FIRST_VALUE(time) OVER(PARTITION BY order_id ORDER BY time) as accept_order_time,
        LAST_VALUE(time) OVER(PARTITION BY order_id) as deliver_order_time
    FROM courier_actions
    WHERE order_id not in (SELECT order_id FROM user_actions WHERE action='cancel_order')
    ORDER BY order_id, time
    ) t1
GROUP BY date
ORDER BY date
```

### Exercise 8

И наконец, давайте оценим почасовую нагрузку на наш сервис, выясним, в какие часы пользователи оформляют больше всего заказов, и заодно проанализируем, как изменяется доля отмен в зависимости от времени оформления заказа.

---

**Задача:**

На основе данных в таблице `orders` для каждого часа в сутках рассчитайте следующие показатели:

1. Число успешных (доставленных) заказов.
2. Число отменённых заказов.
3. Долю отменённых заказов в общем числе заказов ().
    
    cancel rate
    

Колонки с показателями назовите соответственно `successful_orders`, `canceled_orders`, `cancel_rate`. Колонку с часом оформления заказа назовите `hour`. При расчёте доли отменённых заказов округляйте значения до трёх знаков после запятой.

Результирующая таблица должна быть отсортирована по возрастанию колонки с часом оформления заказа.

Поля в результирующей таблице: `hour`, `successful_orders`, `canceled_orders`, `cancel_rate`

---

**Ожидаемый результат**

---

**Подсказка на случай, если совсем не получается**

---

После того как составите запрос, попробуйте визуализировать результаты и постройте графики, отражающие динамику рассчитанных показателей.

Рекомендуем в этот раз построить комбинированный график, настроив во вкладке Series тип визуализации, подходящий для каждого показателя. Например, для числа успешных и отменённых заказов можно использовать столбиковую диаграмму, а для cancel rate — линейную. Чтобы столбцы были расположены друг над другом, во вкладке General в разделе Stacking можно указать параметр Stack.

---

**Пример графика:**

Динамика показателя cancel rate и числа успешных/отменённых заказов:

![https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269847/Orders%20by%20Hours.png](https://storage.yandexcloud.net/klms-public/production/learning-content/152/1881/19868/57329/269847/Orders%20by%20Hours.png)

---

Проанализируйте построенный график и попробуйте ответить на следующие вопросы:

1. В какие часы наблюдаются пиковые значения числа оформляемых заказов? В какие часы пользователи совершают меньше всего заказов?
2. Прослеживается ли какая-то взаимосвязь между количеством оформляемых заказов и долей отменённых заказов? Растёт ли с увеличением числа заказов показатель ?
    
    cancel rate
    

```sql
WITH canceled_orders as (
    SELECT DATE_PART('hour', creation_time)::int as hour,
    COUNT(DISTINCT order_id) as canceled_orders
    FROM orders
    WHERE order_id in (SELECT order_id FROM user_actions WHERE action='cancel_order')
    GROUP BY DATE_PART('hour', creation_time)
),
uncanceled_orders as (
    SELECT DATE_PART('hour', creation_time)::int as hour,
        COUNT(DISTINCT order_id) as successful_orders
    FROM orders
    WHERE order_id not in (SELECT order_id FROM user_actions WHERE action='cancel_order')
    GROUP BY DATE_PART('hour', creation_time)
)

SELECT hour, successful_orders, canceled_orders,
    ROUND(canceled_orders::decimal/(canceled_orders+successful_orders), 3) as cancel_rate
FROM canceled_orders
JOIN uncanceled_orders USING(hour)
ORDER BY hour
```
