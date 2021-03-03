# Руководство по взаимодействию с сервисом коротких сообщений (SMS) на основе HTTPS протокола, методом GET

Запросы необходимо отправлять в UTF-8 кодировке. Не стоит использовать URL длиной более 2,000 символов. В случае ошибки вернется:
```
error: Попытка отправки более одного одинакового запроса в течение минуты
```

# Запрос на отправку смс

Отправляется GET-запрос по адресу:
```
https://имя_хоста/sendsms.php
```

Пример:
```
https://имя_хоста/sendsms.php?user=ваш_логин_в_нашей_системе&pwd=пароль&sadr=HLTelecom_от_кого_придет_СМС&dadr=номер_телефона_получателя_смс&text=текст%смс&translite=1
```
Переменные:
* **user** – пользователь;
* **pwd** – пароль;
* **sadr** - отправитель SMS. Именно это значение будет выводиться на телефоне абонента в поле от кого SMS;
* **text** - текст смс;
* **dadr** -  номер абонента, которому адресована SMS. Можно несколько телефонов через запятую. Номера в международном формате, например, 79001234567 (для России), 380441234567 (для Украины);
* **translite** - транслитерация текста СМС с кириллицы на латиницу (не обязательный параметр). Для транслитерации данный параметр должен быть равен 1.

## В случае успешной отправки смс

Возвращается ID SMS  в plainText. Пример:
```
1000123
```
В случае отправки на несколько номеров возращается ID SMS через запятую в plaintText. Пример:
```
1000123,1000124
```

# Проверка статуса SMS

Отправляетя GET-запрос по адресу:
```
https://имя_хоста/sendsms.php
```

Пример:
```
https://имя_хоста/sendsms.php?user=ваш_логин_в_нашей_системе&pwd=пароль&smsid=id_sms
```
Переменные:
* **user** – пользователь
* **pwd** – пароль
* **smsid** - ID SMS

## В случае успешного запроса

В случае успешного запроса возращается статус SMS сообщения в plainText:

* **send** - статус сообщения не получен.
* **not_deliver** - сообщение   не   было   доставлено.   Конечный   статус   (не меняется со временем).
* **expired** - абонент находился не в сети в те моменты, когда делалась попытка доставки. Конечный Статус (не меняется со временем).
* **deliver** - сообщение   доставлено.   Конечный   статус (не   меняется   со временем)
* **partly_deliver** - сообщение было отправлено, но статус так и не был получен. Конечный статус (не меняется со временем). В этом случае для разъяснения причин отсутствия статуса необходимо связаться со службой тех. поддержки.

Пример:
```
deliver
```

# Проверка статуса SMS с подробной информацией

Отправляетя GET-запрос по адресу:
```
https://имя_хоста/sendsms.php
```

Пример:
```
https://имя_хоста/sendsms.php?user=ваш_логин_в_нашей_системе&pwd=пароль&smsid=id_sms&detail=1
```
Переменные:
* **user** – пользователь
* **pwd** – пароль
* **smsid** - ID SMS
* **detail** - Запрос на детальную информацию по смс, всегда цифра 1

## В случае успешного запроса

В случае успешного запроса возращается статус SMS сообщения в формате plainText. Строка является массивом, обработанной через php функцию serialize(). Для обратного перевода строки в массив, необходимо использовать php функцию unserialize():

**Пример массива ответа, полученный через функцию unserialize():**
```php
Array ( 
	[id_sms] => IDSMS в системе для проверки статуса
	[time_change_state] => 2020-02-23 01:02:03
	[state_sms] => Статус
	[num_parts] => 2
	[price] => 1.15
)
```

Где:
* **id_sms** - номер SMS сообщения, полученный в ответном XML-документа в процессе отправки SMS сообщения.
* **time_change_state** - время изменения статуса.
* **state_sms** - статус сообщения:
    1. «send» - статус сообщения не получен. В этом случае передается пустой time_change_state.
    2. «not_deliver» - сообщение не было доставлено. Конечный   статус   (не меняется со временем).
    3. «expired» - абонент находился не в сети в те моменты, когда делалась попытка доставки. Конечный Статус (не меняется со временем.
    4. «deliver» - сообщение доставлено. Конечный статус (не меняется со временем)
    5. «partly_deliver» - сообщение было отправлено, но статус так и не был получен. Конечный статус (не меняется со временем). В этом случае для разъяснения причин отсутствия статуса необходимо связаться со службой тех. поддержки.
* **num_parts** - Количество частей в СМС.
* **price** - Цена за одну часть СМС.

# Проверка баланса

Отправляется GET-запрос по адресу:
```
https://имя_хоста/sendsms.php
```

Пример:
```
https://имя_хоста/sendsms.php?user=ваш_логин_в_нашей_системе&pwd=пароль&balance=1
```

Переменные:
* **user** – пользователь
* **pwd** – пароль
* **balance** – параметр, определяющий вывод баланса (цифра 1)

## В случае успешного запроса

В случае успешного запроса в plainText возвращается ваш текущий баланс и остаток по текущему пакету через запятую. Пример ответа:
```
444.6 RUR Россия:361,МТС:1,Мегафон:1,Skylink:1,Yota:1,Байкалвестком:1,Уралсвязьинформ:1,Енисейтелеком:1,НСС:1,Мотив:1,Tele2:1,СМАРТС:1,Ростелеком:1,BeeLine:1,Остальные:1
```

## Ошибки возвращаемые платформой

В случае возникновения ошибки возращается текст ошибки в plainText. Возможные варианты:
* У нас закончились SMS. Для разрешения проблемы свяжитесь с менеджером.
* Закончились SMS.
* Аккаунт заблокирован.
* Укажите номер телефона.
* Номер телефона присутствует в стоп-листе.
* Данное направление закрыто для вас.
* Данное направление закрыто.
* Недостаточно средств для отправки SMS. SMS будет отправлена как только вы пополните счет по данному направлению.
* Текст SMS отклонен модератором.
* Нет отправителя.
* Отправитель не должен превышать 15 символов для цифровых номеров и 11 символов для буквенно-числовых.
* Номер телефона должен быть меньше 15 символов.
* Нет текста сообщения.
* Нет ссылки.
* Такого отправителя Нет.
* Отправитель не прошел модерацию.
* error: Попытка отправки более одного одинакового запроса в течение минуты
* Данное сообщение уже было отправлено


# Входящие СМС

Имеется возможность принимать входящие СМС, в том числе и с коротких номеров. Для подключения сервиса, использующего короткий номер, необходимо:

1. Отправить заявку, на подключение входящих смс.
2. Предоставить адрес URL скрипта обработчика на вашем сайте.

API использует GET запрос для передачи сообщения, которое абонент отправил на короткий номер с предоставленным вам префиксом. Кроме самого текста сообщения, вашему скрипту будут переданы, другие данные, которые вы можете использовать для обработки "входных данных" у себя на сайте. 
Существует 2 способа оповещения пользователя:

## Способ 1. Асинхронное оповещение
Запрос к Вашу скрипту выглядит так:
```
http://адрес_сервера/ваш_скрипт?date=2020-02-23 01:02:03&prefix=0001&text=test_sms&smsid=543&sender=3443&receiver=79001234567
```
Переменные:
* **date** - дата обработки смс на короткий номер в формате 2020-02-23 01:02:03
* **prefix** - префикс. Текст, который необходимо отправить Абоненту в смс-сообщении (вначале), чтобы сообщение было однозначно 	идентифицировано нашей системой и отнесено к Вашему Проекту.
* **text**  - текст входящего сообщения. Обратите внимание, текст приходит в кодировке utf-8.
* **smsid** - уникальный идентификатор СМС в системе.
* **sender** - номер телефона абонента приславшего смс.
* **receiver**  - короткий номер, на который пришла входящая смс.

После обработки "входных данных" ваш скрипт должен ответить телом ответа **smsid=5543** (Уникальный идентификатор СМС).

## Способ 2. Синхронное оповещение, с получением текста ответного смс
Запрос к Вашу скрипту выглядит так:
```
http://адрес_сервера/ваш_скрипт?date=2020-02-23 01:02:03&prefix=0001&text=test_sms&smsid=543&sender=3443&receiver=79001234567
```
Переменные:
* **date** - дата обработки смс на короткий номер в формате 2020-02-23 01:02:03
* **prefix** - префикс. Текст, который необходимо отправить Абоненту в смс-сообщении (вначале), чтобы сообщение было однозначно 	идентифицировано нашей системой и отнесено к Вашему Проекту.
* **text**  - текст входящего сообщения. Обратите внимание, текст приходит в кодировке utf-8.
* **smsid** - уникальный идентификатор СМС в системе.
* **sender** - номер телефона абонента приславшего смс.
* **receiver**  - короткий номер, на который пришла входящая смс.

В ответ мы ожидаем от вашего скрипта текст ответного смс, помещенного в тело ответа. В случае, если текст ответного смс не нужен, тело ответа должно быть пустым.

Способ оповещения заранее обговаривайте с менеджером.
