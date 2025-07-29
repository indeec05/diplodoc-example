# Описание методов API {#описание-методов-api}

## Получение списка способов оплаты {#получение-списка-способов-оплаты}

Метод возвращает доступные на данный момент для интернет магазина способы оплаты.

| GET {baseUrl}/paymentTypes/{currencyId} |
| :---- |

- currencyId, является необязательным параметром и служит для фильтрации способов оплаты по валюте.

Пример запроса:

| GET {baseUrl}/paymentTypes/643 |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 |

Если запрос был обработан без ошибок, API отвечает кодом 200 OK, и возвращает объект содержащий коллекцию доступных способов оплаты:

Пример ответа:

| HTTP/1.1 200 OK |
| :---- |
| {    "PaymentTypes":\[        {"PaymentTypeId":"CreiditCardRub"},        {"PaymentTypeId":"CreditCardRUB"},        {"PaymentTypeId":"CreditCardAZN"},        {"PaymentTypeId":"YandexMoneyRUB"},        {"PaymentTypeId":"QiwiWalletRUB"},        {"PaymentTypeId":"MtsRUB"}     \]} |

## Создание инвойса {#создание-инвойса}

Метод создает инвойс с переданными параметрами и резервирует идентификатор платежа.

| POST {baseUrl}/invoices |
| :---- |

В теле запроса необходимо передать объект содержащий информацию о сумме заказа и способе оплаты.

Пример запроса:

| POST {baseUrl}/invoices |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 |
| {     "OrderId":"GQYOE-MJNPO-49623",     "Amount":2.91,     "CurrencyId":643,     "PaymentTypeId":"MtsRUB",     "OrderItems": \[         {             "Title": "Position 1",             "Quantity": 3.000,             "UnitPrice": 150.00,             "SubTotal": 450.00,             "TaxType": "tax\_ru\_1",             "Tax": 10.00         },         {             "Title": "Position 2",             "Quantity": 1.000,             "UnitPrice": 70.00,             "SubTotal": 70.00,             "TaxType": "tax\_ru\_1",             "Tax": 10.00         }     \],     "Description": "Invoice description",     "InvoiceAdditionalParams": {     } } |

- OrderId \- уникальный идентификатор заказа на стороне интернет магазина,  
- PaymentTypeId \- идентификатор способа оплаты,  
- OrderItems \-  массив товаров, содержащий данные для чека, является обязательным при использовании ККТ,  
- InvoiceAdditionalParams \- необязательный объект, описывающий дополнительные параметры заказа, полный перечень возможных параметров представлен в справочнике [Дополнительные параметры инвойса](#дополнительные-параметры-инвойса).

| Обратите внимание: при передаче массива OrderItems необходимо дополнительно передать один из параметров WMI\_CUSTOMER\_PHONE или WMI\_CUSTOMER\_EMAIL в InvoiceAdditionalParams  |
| :---- |

Если запрос был обработан без ошибок, API отвечает кодом 200 OK, и возвращает объект содержащий данные о созданном инвойсе и зарезервированном платеже.

| Обратите внимание: в поле OrderId не допускается использование следующих символов: ":", "/", "?", "\#", "@" , ";" |
| :---- |

Пример ответа:

| HTTP/1.1 200 OK |
| :---- |
| {    "Invoice": {        "InvoiceId":341234567891        "Amount":2.19,        "CurrencyId":643,        "InvoiceStateId":"Created",        "CreateDate":"2014-06-10T09:54:05.033",        "UpdateDate":"2014-06-10T09:54:05.033",        "Payment": {            "PaymentId":123456,            "PaymentCode":"341234567891,            "PaymentCodeType":"InvoiceId"            "CreateDate":"2014-06-10T09:54:05.033",            "UpdateDate":"2014-06-10T09:54:05.033",            "PaymentStateId":"Created"            "PaymentTypeId":"MtsRUB",        }    }    "CanSaveAsExternalAccount":"false"     "PayerCommissionAmounts": {        "MinCommissionAmount":0.1,        "MaxCommissionAmount":1.0,        }    "InvoiceUrl":"http://.../Index?i=341234567891\&m=119973638979" } |
|  |

- InvoiceStateId \- статус инвойса, полный перечень возможных статусов инвойса, представлен в справочнике [Статусы инвойса](#статусы-инвойса),  
- PaymentStateId \- статус платежа, полный перечень возможных статусов платежа, представлен в справочнике [Статусы платежа](#статусы-платежа),  
- CanSaveAsExternalAccount \- флаг, характеризующий возможность сохранения и последующего использования платежного инструмента плательщика, [подробнее](#рекуррентные-платежи).  
- PayerCommissionAmounts \- объект, отображающий минимальную и максимальную комиссию, которая может быть удержана с плательщика  
- InvoiceUrl \- ссылка на которую можно перенаправить пользователя для выбора способа и дальнейшей оплаты счета в браузере.

## Получение информации по инвойсу {#получение-информации-по-инвойсу}

Метод возвращает информацию о ранее созданном инвойсе.

| GET {baseUrl}/invoices/{orderId}/ |
| :---- |

- orderId \- идентификатор заказа на стороне магазина.

Пример запроса:

| GET {baseUrl}/invoices/GQYOE-MJNPO-49623/ |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 |

Если запрос был обработан без ошибок, API отвечает кодом 200 OK, и возвращает объект содержащий информацию об инвойсе и сумме платежей.

Пример ответа:

| HTTP/1.1 200 OK |
| :---- |
| {      "Invoice": {         "InvoiceId":341234567891,         "Amount":30,         "CurrencyId":643,         "InvoiceStateId":"Accepted",         "CreateDate":"2014-06-10T09:54:05.033",         "UpdateDate":"2014-07-10T09:55:05.033"             },     "TotalPaidAmount": 30 } |

## Получение списка платежей по инвойсу {#получение-списка-платежей-по-инвойсу}

Метод возвращает список платежей по инвойсу.

| GET {baseUrl}/invoices/{orderId}/payments |
| :---- |

- orderId \- идентификатор заказа на стороне магазина.

Пример запроса:

| GET {baseUrl}/invoices/GQYOE-MJNPO-49623/payments |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 |

Если запрос был обработан без ошибок, API отвечает кодом 200 OK, и возвращает коллекцию объектов, содержащих информацию о платеже.

Пример ответа:

| HTTP/1.1 200 OK |
| :---- |
| \[    {        "PaymentId":123456,        "Amount":15.50,        "CurrencyId":643,        "PaymentTypeId": "CreditCardRUB",        "Date":"2014-06-10T09:55:05.033",        "PaymentStateId":"Paid",        "SuspenseAmount": 0    },    {        "PaymentId":123457,        "Amount":15,        "CurrencyId":643,        "PaymentTypeId": "CashTerminalRUB",        "Date":"2014-07-10T09:55:05.033",        "PaymentStateId":"Paid",        "SuspenseAmount": 0.5    }\] |

- SuspenseAmount \- сумма переплаты по платежу.

## Зачисление инвойса {#зачисление-инвойса}

| Обратите внимание: Данный метод используется только если при создании инвойса был передан параметр WMI\_AUTO\_ACCEPT со значением 0\. Требуется согласование с менеджером и наличие определенных настроек на интернет магазине. |
| :---- |

Метод подтверждает списание денежных средств с инструмента плательщика и переводит инвойс в конечный статус.

| POST {baseUrl}/invoices/{orderId}/accept |
| :---- |

- orderId \- идентификатор заказа на стороне магазина.

В случае поддержки частичного зачисления предавторизации возможна передача параметра Amount \- новой суммы счёта. При передаче Amount сумма счёта будет изменена на переданную, а сумма платежа скорректирована исходя из новой суммы счёта и размера комиссий.

Пример запроса:

| POST {baseUrl}/invoices/GQYOE-MJNPO-49623/accept |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 {     "Amount":10 } |

Если запрос был обработан без ошибок, API отвечает кодом 200 OK, и возвращает объект содержащий информацию об инвойсе и созданных платежах.

Пример ответа:

| HTTP/1.1 200 OK |
| :---- |
| {      "Invoice": {         "InvoiceId":341234567891,         "Amount":10,         "CurrencyId":643,         "InvoiceStateId":"Accepted",         "CreateDate":"2014-06-10T09:54:05.033",         "UpdateDate":"2014-07-10T09:55:05.033"             },     "Payments" : \[         {             "PaymentId":123456,             "Amount":10,             "CurrencyId":643,             "PaymentTypeId": "CreditCardRUB",             "Date":"2014-06-10T09:55:05.033",             "SuspenseAmount": 0         }     \],     "TotalPaidAmount":10 } |

## Отмена инвойса {#отмена-инвойса}

| Обратите внимание: Данный метод используется только если при создании инвойса был передан параметр WMI\_AUTO\_ACCEPT со значением 0\. Требуется согласование с менеджером и наличие определенных настроек на интернет магазине. |
| :---- |

Метод отменяет списание средств с платежного инструмента плательщика и переводит инвойс в конечный статус.

| POST {baseUrl}/invoices/{orderId}/reject |
| :---- |

- orderId \- идентификатор заказа на стороне магазина.

Пример запроса:

| POST {baseUrl}/invoices/GQYOE-MJNPO-49623/reject |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 |

Если запрос был обработан без ошибок, API отвечает кодом 200 OK, и возвращает объект содержащий информацию об инвойсе и созданных платежах.

Пример ответа:

| HTTP/1.1 200 OK |
| :---- |
| {      "Invoice": {         "InvoiceId":341234567891,         "Amount":30,         "CurrencyId":643,         "InvoiceStateId":"Accepted",         "CreateDate":"2014-06-10T09:54:05.033",         "UpdateDate":"2014-07-10T09:55:05.033"             },     "Payments" : \[         {             "PaymentId":123456,             "Amount":30,             "CurrencyId":643,             "PaymentTypeId": "CreditCardRUB",             "Date":"2014-06-10T09:55:05.033",             "SuspenseAmount": 0         }     \],     "TotalPaidAmount":30 } |

## Проведение платежа {#проведение-платежа}

Метод инициирует процесс проведения платежа.

| POST {baseUrl}/payments/{paymentId}/process |
| :---- |

- paymentId \- идентификатор платежа, полученный при вызове метода [Создание инвойса](#создание-инвойса).

Пример запроса:

| POST {baseUrl}/payments/123456/process |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 |
| {    "AuthData": {          "CreditCardNumber":"4200000000000000",        "CreditCardHolder":"IVAN BUNSHA",        "CreditCardExpireDate":"03.2014",        "CreditCardSecurityCode":"123",        "UserInfoIP": "81.65.247.46",        "UserInfoEmail": "BUNSHA@testemail.com"    },    "SaveAuthData":"true",         "CustomerId":"testUser123987",            "CreditCardTerminal":"Non3Ds"                                                                  } |

- AuthData \- объект, содержащий данные о плательщике и данные платежного инструмента, список возможных полей варьируется от способа оплаты. С полным перечнем возможных полей можно ознакомится в справочнике [Поля платежных инструментов](#поля-платежных-инструментов),  
- SaveAuthData \- флаг означающий необходимость сохранения данных платежного инструмента, учитывается только при оплате банковской картой,  
- CustomerId \- идентификатор плательщика на стороне интернет магазина,  
- ExternalAccountId \- идентификатор ранее сохраненного платежного инструмента, используется при оплате с ранее привязанного платежного инструмента,  
- UseSavedAuthData \- флаг означающий, нужно ли использовать ранее сохраненный платежный инструмент, будет использован дефолтный,  
- CreditCardTerminal \- используемый терминал, используется только при оплате с банковской карты, список возможных значений представлен в справочнике [Карточные терминалы](#карточные-терминалы).

| Обратите внимание:  при передачи флага SaveAuthData со значение “True” передача CustomerId является обязательным условием, в противном случае будет сгенерирована ошибка, при использовании ранее привязанного платежного инструмента, передача параметра CustomerId является обязательным условием, в противном случае значения параметров ExternalAccountId и UseSavedAuthData будут игнорироваться, параметры ExternalAccountId и UseSavedAuthData являются взаимоисключающими и при передаче обоих будет учитываться только ExternalAccountId. |
| :---- |

Если запрос был обработан без ошибок, API отвечает кодом 200 OK, и возвращает объект содержащий информацию о платеже и требуемой дополнительной авторизации.

Пример ответа:

| HTTP/1.1 200 OK |
| :---- |
| {    "Payment": {        "PaymentId":123456,        "Amount":2.91,        "CurrencyId":643,        "CreateDate":"2014-06-10T09:54:05.033",        "UpdateDate":"2014-06-10T09:54:05.033",        "PaymentStateId":"AuthRequired",        "PaymentTypeId":"CreditCardRUB",        "PaymentCode":"123456789123",        "PaymentCodeType":"UserId",        "ExternalAccountId":245498     },    "Authorization": {        "AuthType":"Redirect",         "PayerConfirmationType":"None",         "HttpForm": {            "Action":"http://superbank.com/auth.php",            "Method":"POST",            "Params":{                "PaReq":"Nkshk\\uehnkf7LsKNSFEPwqnf\[98u23f",                "MD":"123456"            }        }    }} |

- Authorization \- объект, возвращаемый в случае если требуется дополнительная авторизация, например прохождение 3Ds,  
- AuthType \- тип доп. авторизации, с полным перечнем возможных значение можно ознакомиться в справочнике [Типы доп. авторизации](#типы-доп.-авторизации),  
- PayerConfirmationType \- тип подтверждения, с полным перечнем возможных значение можно ознакомиться в справочнике [Типы доп. авторизации](#типы-доп.-авторизации)

| Обратите внимание: При наличии параметров в Authorization.HttpForm.Params их нужно также передавать на Authorization.HttpForm.Action |
| :---- |

## Получение информации по платежу {#получение-информации-по-платежу}

Метод возвращает информацию по платежу.

| GET {baseUrl}/payments/{paymentId} |
| :---- |

- paymentId \- идентификатор платежа, полученный при вызове метода [Создание инвойса](#создание-инвойса).

Пример запроса:

| GET {baseUrl}/payments/123456 |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 |

Если запрос был обработан без ошибок, API отвечает кодом 200 OK, и возвращает объект содержащий информацию о платеже.

Пример ответа:

| HTTP/1.1 200 OK |
| :---- |
| {    "PaymentId":123456,     "PaymentCode":"345676557987",     "PaymentCodeType":"invoiceId",     "PaymentTypeId": "CreditCardRUB",    "Amount":15.50,    "CurrencyId":643,    "CreateDate":"2014-06-10T09:55:05.033",     "UpdateDate":"2014-06-10T09:55:05.033",    "PaymentStateId":"Paid",     "ExternalAccountId":1000578,     "MsterField":"411111\*\*\*\*\*\*1111",     "ErrorMessage":""} |

Любой успешный запрос, обязательно содержит следующие поля:

- PaymentId,  
- PaymentCode,  
- PaymentCodeType,  
- CreateDate,  
- UpdateDate,  
- PaymentStateId

## Создание запроса на осуществление возврата {#создание-запроса-на-осуществление-возврата}

Метод создает заявку на возврат средств по ранее проведенному платежу.

| POST {baseUrl}/payments/{paymentId}/refunds/create |
| :---- |

- paymentId \- идентификатор платежа, полученный при вызове метода [Создание инвойса](#создание-инвойса).

Пример запроса:

| POST {baseUrl}/payments/123456/refunds/create |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 |
| {    "Description": "Описание/причина",     "Amount": 5.56} |

Параметр Amount опционален, используется для частичных отмен.

Если запрос был обработан без ошибок, API отвечает кодом 200 OK, и возвращает объект содержащий информацию о возврате.

Пример ответа:

| HTTP/1.1 200 OK |
| :---- |
| {    "RefundId":10000,    "CreateDate":"2016-04-22T09:54:05.033",    "UpdateDate":"2016-04-22T09:54:05.033",    "Amount":5.56,    "CurrencyId":643,    "Description":"Описание/причина",    "StateId":"Created"} |

- StateId \- состояние созданного возврата, с полным перечнем возможных значение можно ознакомиться в справочнике [Карта состояний возврата](#карта-состояний-возврата).

## Получение информации о возврате {#получение-информации-о-возврате}

Метод возвращает информацию о конкретном возврате.

| GET {baseUrl}/payments/{paymentId}/refunds/{refundId} |
| :---- |

- paymentId \- идентификатор платежа, полученный при вызове метода [Создание инвойса](#создание-инвойса),  
- refundId \- идентификатор возврата, полученный при вызове метода [Создание запроса на осуществление возврата](#создание-запроса-на-осуществление-возврата).

Пример запроса:

| GET {baseUrl}/payments/123456/refunds/10000 |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 |

Если запрос был обработан без ошибок, API отвечает кодом 200 OK, и возвращает объект содержащий информацию о возврате.

Пример ответа:

| HTTP/1.1 200 OK |
| :---- |
| {    "RefundId":10000,    "CreateDate":"2016-04-22T09:54:05.033",    "UpdateDate":"2016-04-22T09:54:05.033",    "Amount":5.56,    "CurrencyId":643,    "Description":"Описание/причина",    "StateId":"Created"} |

## Получение информации о произведенных возвратах {#получение-информации-о-произведенных-возвратах}

Метод возвращает информацию о всех возвратах созданных по платежу.

| GET {baseUrl}/payments/{paymentId}/refunds |
| :---- |

- paymentId \- идентификатор платежа, полученный при вызове метода [Создание инвойса](#создание-инвойса).

Пример запроса:

| GET {baseUrl}/payments/123456/refunds |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 |

Если запрос был обработан без ошибок, API отвечает кодом 200 OK, и возвращает массив объектов.

Пример ответа:

| HTTP/1.1 200 OK |
| :---- |
| \[    {        "RefundId":10000,        "CreateDate":"2016-04-22T09:54:05.033",        "UpdateDate":"2016-04-22T09:54:05.033",        "Amount":5.56,        "CurrencyId":643,        "Description":"Описание/причина",        "StateId":"ProcessError"    },    {        "RefundId":10001,        "CreateDate":"2016-04-23T09:54:05.033",        "UpdateDate":"2016-04-23T09:54:05.033",        "Amount":5.56,        "CurrencyId":643,        "Description":"Описание/причина",        "StateId":"Processing"    }\] |

## Получение списка платежных инструментов {#получение-списка-платежных-инструментов}

Метод возвращает список платежных инструментов по конкретному покупателю.

| GET {baseUrl}/externalaccounts |
| :---- |

Пример запроса:

| GET {baseUrl}/externalaccounts |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 X-Wallet-CustomerId: RTC5698365 |

- X-Wallet-CustomerId \- идентификатор покупателя на стороне интернет магазина, является обязательным заголовком.

Если запрос был обработан без ошибок, API отвечает кодом 200 OK, и возвращает коллекцию привязанных платежных инструментов.

Пример ответа:

| HTTP/1.1 200 OK |
| :---- |
| \[    {        "ExternalAccountId":123456,        "PaymentTypeId": "CreditCardRUB",        "CurrencyId":643,        "Title":"Моя карта",        "MasterField":"411111\*\*\*\*\*\*11",         "IsDefault":"true"    },    {        "ExternalAccountId":1234546,        "PaymentTypeId": "CreditCardRUB",        "CurrencyId":643,        "Title":"Моя карта 2",        "MasterField":"411111\*\*\*\*\*\*12",         "IsDefault":"false"    },\] |

## Обновление платежного инструмента {#обновление-платежного-инструмента}

Метод используется для обновления данных сохраненного платежного инструмента.

| PUT {baseUrl}/externalaccounts/{externalAccountId} |
| :---- |

- externalAccountId \- идентификатор платежного инструмента, полученный при [проведении платежа](#проведение-платежа).

Пример запроса:

| PUT {baseUrl}/externalaccounts/{externalAccountId} |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 X-Wallet-CustomerId: RTC5698365 |
| {    "Title":"Моя новая банковская карта",    "IsDefault":"True"} |

- X-Wallet-CustomerId \- идентификатор покупателя на стороне интернет магазина, является обязательным заголовком.

Если запрос был обработан без ошибок, API отвечает кодом 200 OK.

## Удаление платежного инструмента {#удаление-платежного-инструмента}

Метод используется для удаления ранее сохраненного платежного инструмента.

| DELETE {baseUrl}/externalaccounts/{externalAccountId} |
| :---- |

Пример запроса:

| DELETE {baseUrl}/externalaccounts/{externalAccountId} |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 X-Wallet-CustomerId: RTC5698365 |

- X-Wallet-CustomerId \- идентификатор покупателя на стороне интернет магазина, является обязательным заголовком.

Если запрос был обработан без ошибок, API отвечает кодом 200 OK.

# 

# 

# Сценарии использования {#сценарии-использования}

## Оплата банковской картой {#оплата-банковской-картой}

Оплата банковской картой возможна несколькими сценариями, каждый из которых состоит из нескольких шагов, общим и обязательным шагом является создание инвойса. В зависимости от сценария, параметры на данном шаге могут меняться, эти изменения, если они требуются, будут указаны в начале описания каждого из сценариев. 

Для создания инвойса понадобится метод “[Создание инвойса](#создание-инвойса)”:

| POST {baseUrl}/invoices |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2016-08-04T07:43:47 X-Wallet-UserId: 119973638979 |
| {     "OrderId":"GQYOE-MJNPO-49623",     "Amount":5.30,     "CurrencyId":643,     "PaymentTypeId":"CreditCardRub",     "InvoiceAdditionalParams": {         "WMI\_SUCCESS\_URL":"https://mushop.com/success.html",         "WMI\_FAIL\_URL":"https://mushop.com/fail.html"     } } |

Для того чтобы инвойс можно было оплатить именно картами, в значении поля PaymentTypeId необходимо передать один из карточных способов.

Ответ:

| HTTP/1.1 200 OK |
| :---- |
| X-Wallet-Timestamp: 2016-08-04T07:43:50X-Wallet-Signature: 1SVVi80EB1FRp8tvPaGqRQ== |
| {    "Invoice":    {        "InvoiceId":337450307479,        "Amount":10.0000,        "CurrencyId":643,        "InvoiceStateId":"Created",        "CreateDate":"2016-08-04T07:43:47",        "UpdateDate":"2016-08-04T07:43:47",        "Payment":        {            "PaymentId":92180411,            "PaymentCode":"337450307479",            "PaymentCodeType":"InvoiceId",            "CreateDate":"2016-08-04T07:43:47.05",            "UpdateDate":"2016-08-04T07:43:47.05",            "PaymentStateId":"Created",            "PaymentTypeId":"CreditCardRub"        }    },    "CanSaveAsExternalAccount":"true",    "PayerCommissionAmounts": {        "MinCommissionAmount": 0.0,        "MaxCommissionAmount": 0.0    },    "InvoiceUrl": "http://wl.walletone.com/checkout/checkout/Index/?i=337450307479\&m=119973638979"} |

Значение параметра CanSaveAsExternalAccount указывает на то, что возможна привязка платежного инструмента. Для оплаты инвойса необходимо отправить запрос содержащий данные платежного инструмента:

| POST {baseUrl}/payments/123456/process |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2016-08-04T05:34:53 X-Wallet-UserId: 119973638979 |
| {    "AuthData":    {        "CreditCardNumber":"4111111111111111",        "CreditCardHolder":"IVAN BUNSHA",        "CreditCardExpireDate":"10.2016",        "CreditCardSecurityCode":"356"    }} |

Ответ:

| HTTP/1.1 200 OK |
| :---- |
| X-Wallet-Timestamp: 2016-08-04T07:44:29X-Wallet-Signature: H49Q9kk/OxXCGHMNHNfc3g== |
| {    "Payment":    {        "PaymentId":92180411,        "Amount":10.0000,        "CurrencyId":643,        "PaymentCode":"337450307479",        "PaymentCodeType":"InvoiceId",        "CreateDate":"2016-08-04T07:43:47.05",        "UpdateDate":"2016-08-04T07:43:47.05",        "PaymentStateId":"AuthRequired",        "PaymentTypeId":"CreditCardRub"    },    "Authorization":    {        "AuthType":"Redirect",        "PayerConfirmationType":"None",        "HttpForm":        {            "Action":"http://superbank.com/auth.php",            "Method":"POST",            "Params":            {                "MD":"768862101",                "PaReq":"Nkshk\\uehnkf7LsKNSFEPwqnf\[98u23f",              "TermUrl":"https://wl.walletone.com/checkout/refill/Uniteller/CreditCardRub/UnitellerRUB/92180411/State3Ds"            }        }    }} |

Ответ содержит объект Authorization, указывающий на то, что требуется доп. авторизация. Значение поля AuthType отражает требуемое действие, конкретно в данном примере указано, что необходимо произвести перенаправление пользователя способом указанным в поле HttpForm.Method с параметрами из HttpForm.Params на урл HttpForm.Action:

| \<html\>    \<form id="checkoutOrderData" class="-visor-no-click" method="POST" action="http://superbank.com/auth.php" \>        \<input type="hidden" name="MD" value="768862101" /\>        \<input type="hidden" name="PaReq" value="Nkshk\\uehnkf7LsKNSFEPwqnf\[98u23f" /\>        \<input type="hidden" name="TermUrl" value="https://wl.walletone.com/checkout/refill/Uniteller/CreditCardRub/UnitellerRUB/92180411/State3Ds" /\>        \<input type="submit" style="display:none" value="" /\>    \</form\>    \<script type="text/javascript"\>document.getElementById('checkoutOrderData').submit()\</script\>\</html\> |
| :---- |

После отправки формы пользователь перейдет на страницу банка для ввода 3ds. В зависимости от успешности проведения платежа пользователь перейдет на переданные, при создании инвойса, страницы WMI\_SUCCESS\_URL, WMI\_FAIL\_URL, если они не были указаны то на страницы заглушки.

### Рекуррентные платежи {#рекуррентные-платежи}

Рекуррентные платежи позволяют интернет-магазину производить списание средств с банковских карт своих плательщиков без повторного запроса ввода реквизитов карты.

Для возможности проведения рекуррентного платежа необходимо вначале сохранить и привязать платежный инструмент, это происходит в момент оплаты счета при передаче соответствующих параметров.

Представленное API также содержит дополнительные методы по работе с привязанными платежными инструментами:

- [Получение списка платежных инструментов](#получение-списка-платежных-инструментов),  
- [Обновление платежного инструмента](#обновление-платежного-инструмента),  
- [Удаление платежного инструмента](#удаление-платежного-инструмента).

#### Привязка платежного инструмента {#привязка-платежного-инструмента}

Привязка платежных инструментов возможна только если при [создании инвойса](#создание-инвойса) в ответе вернулся параметр CanSaveAsExternalAccount со значение true. Для осуществления привязки, при вызове метода [проведения платежа](#проведение-платежа) необходимо передать идентификатор плательщика на стороне интернет магазина (CustomerId) и поле SaveAuthData со значением “True”:

| POST {baseUrl}/payments/123456/process |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2016-08-04T05:34:53 X-Wallet-UserId: 119973638979 |
| {    "AuthData":    {        "CreditCardNumber":"4111111111111111",        "CreditCardHolder":"IVAN BUNSHA",        "CreditCardExpireDate":"10.2016",        "CreditCardSecurityCode":"356",         "UserInfoIP": "81.65.247.46",        "UserInfoEmail": "BUNSHA@testemail.com"    },     "CustomerId":"MT-8965-RT",     "SaveAuthData":"true"} |

В примере дополнительно передаются данные плательщика через поля UserInfo.

Ответ:

| HTTP/1.1 200 OK |
| :---- |
| X-Wallet-Timestamp: 2016-08-04T07:44:29X-Wallet-Signature: H49Q9kk/OxXCGHMNHNfc3g== |
| {    "Payment":    {        "PaymentId":92180411,        "Amount":10.0000,        "CurrencyId":643,        "PaymentCode":"337450307479",        "PaymentCodeType":"InvoiceId",        "CreateDate":"2016-08-04T07:43:47.05",        "UpdateDate":"2016-08-04T07:43:47.05",        "PaymentStateId":"AuthRequired",        "PaymentTypeId":"CreditCardRub",         "ExternalAccountId":245498    },    "Authorization":    {        "AuthType":"Redirect",        "PayerConfirmationType":"None",        "HttpForm":        {            "Action":"http://superbank.com/auth.php",            "Method":"POST",            "Params":            {                "MD":"768862101",                "PaReq":"Nkshk\\uehnkf7LsKNSFEPwqnf\[98u23f",              "TermUrl":"https://wl.walletone.com/checkout/refill/Uniteller/CreditCardRub/UnitellerRUB/92180411/State3Ds"            }        }    }} |

Ответ практически не отличается от ответа представленного выше, так что шаг доп авторизации будет опущен. 

Важным отличием является дополнительное поле ExternalAccountId в объекте Payment, оно содержит идентификатор привязки используемый при оплате с привязанного инструмента. 

| Важно, только после успешной оплаты данный идентификатор можно будет использовать для оплаты с привязанной карты. |
| :---- |

#### Оплата с привязанного платежного инструмента {#оплата-с-привязанного-платежного-инструмента}

Для оплаты с ранее привязанной карты необходимо в запрос проведения платежа передать идентификатор ExternalAccountId или же поле UseSavedAuthData со значение true. Если будут переданы оба поля, поле UseSavedAuthData будет игнорироваться.

| POST {baseUrl}/payments/123456/process |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2016-08-04T05:34:53 X-Wallet-UserId: 119973638979 |
| {     "ExternalAccountId":245498,    "AuthData":    {        "RecurrentCreditCardAuthSecurityCode":"356",         "RecurrentCreditCardAuthIP": "81.65.247.46",        "RecurrentCreditCardEmail": "BUNSHA@testemail.com"    },     "CustomerId":"MT-8965-RT"} |

Ответ:

| HTTP/1.1 200 OK |
| :---- |
| X-Wallet-Timestamp: 2016-08-04T07:44:29X-Wallet-Signature: H49Q9kk/OxXCGHMNHNfc3g== |
| {    "Payment":    {        "PaymentId":92180411,        "Amount":10.0000,        "CurrencyId":643,        "PaymentCode":"337450307479",        "PaymentCodeType":"InvoiceId",        "CreateDate":"2016-08-04T07:43:47.05",        "UpdateDate":"2016-08-04T07:43:47.05",        "PaymentStateId":"AuthRequired",        "PaymentTypeId":"CreditCardRub",         "ExternalAccountId":245498    }} |

В зависимости от платежного шлюза, ответ может содержать объект Authorization, что необходимо делать в таком случае описано выше.

### Пред-авторизация платежа {#пред-авторизация-платежа}

Пред-авторизация платежа позволяет интернет магазину самому принимать решение о списание денежных средств с платежного инструмента пользователя, что позволяет избежать дополнительных расходов в случае отказа от услуги или невозможности ее предоставления по каким либо причинам. Другими словами в момент оплаты счета средства на карте пользователя не списываются, а холдируются.

Данный сценарии не сильно отличается от сценариев оплаты банковской картой описанных выше, за исключением того, что на стадии [создания инвойса](#создание-инвойса) необходимо передать дополнительные параметры WMI\_AUTO\_ACCEPT со значением 0 и единственный WMI\_PTENABLED, поддерживающий предавторизацию. После завершения оплаты потребуется подтвердить или отменить списание средств.

| Важно, счет будет автоматически отменен через установленный промежуток времени, данный временной промежуток устанавливается на мерчанте. |
| :---- |

#### Отложенная оплата (списание денежных средств) {#отложенная-оплата-(списание-денежных-средств)}

Для подтверждения списания необходимо вызвать метод [зачисление инвойса](#зачисление-инвойса):

| POST {baseUrl}/invoices/GQYOE-MJNPO-49623/accept |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 |

Ответ:

| HTTP/1.1 200 OK |
| :---- |
| X-Wallet-Timestamp: 2016-08-04T07:44:29X-Wallet-Signature: H49Q9kk/OxXCGHMNHNfc3g== |
| {     "Invoice": {        "InvoiceId":341234567891,        "Amount":30,        "CurrencyId":643,        "InvoiceStateId":"Accepting",        "CreateDate":"2014-06-10T09:54:05.033",        "UpdateDate":"2014-07-10T09:55:05.033"            },    "Payments" : \[        {            "PaymentId":123456,            "Amount":30,            "CurrencyId":643,            "PaymentTypeId": "CreditCardRUB",            "Date":"2014-06-10T09:55:05.033",            "SuspenseAmount": 0        }    \]} |

#### Отложенная оплата (отмена списания денежных средств) {#отложенная-оплата-(отмена-списания-денежных-средств)}

Для отмены списания денежных средств необходимо вызвать метод [отмена инвойса](https://docs.google.com/document/d/18XAlBRPlW_7oXF0eqg39GiZKy6-OyeUylM6R0ZmZKsY/edit#heading=h.qmqr8clqkfm1): 

| POST {baseUrl}/invoices/GQYOE-MJNPO-49623/reject |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 |

Ответ:

| HTTP/1.1 200 OK |
| :---- |
| X-Wallet-Timestamp: 2016-08-04T07:44:29X-Wallet-Signature: H49Q9kk/OxXCGHMNHNfc3g== |
| {     "Invoice": {        "InvoiceId":341234567891,        "Amount":30,        "CurrencyId":643,        "InvoiceStateId":"Canceling",        "CreateDate":"2014-06-10T09:54:05.033",        "UpdateDate":"2014-07-10T09:55:05.033"            },    "Payments" : \[        {            "PaymentId":123456,            "Amount":30,            "CurrencyId":643,            "PaymentTypeId": "CreditCardRUB",            "Date":"2014-06-10T09:55:05.033",            "SuspenseAmount": 0        }    \]} |

## Оплата мобильной коммерцией {#оплата-мобильной-коммерцией}

Процесс оплаты с мобильного телефона аналогичен процессу оплаты с банковской карты \- он также включает шаги создания инвойса и проведения платежа, только сценарий тут один.  
Для создания инвойса понадобится метод “[Создание инвойса](#создание-инвойса)”:

| POST {baseUrl}/invoices |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2016-08-04T07:43:47 X-Wallet-UserId: 119973638979 |
| {     "OrderId":"GQYOE-MJNPO-49623",     "Amount":5.30,     "CurrencyId":643,     "PaymentTypeId":"MegafonRUB",     "InvoiceAdditionalParams": {         "WMI\_SUCCESS\_URL":"https://mushop.com/success.html",         "WMI\_FAIL\_URL":"https://mushop.com/fail.html"     } } |

В значении поля PaymentTypeId необходимо передать один из способов мобильной коммерции, в примере используется MegafonRUB.

Ответ:

| HTTP/1.1 200 OK |
| :---- |
| X-Wallet-Timestamp: 2016-08-04T07:43:50X-Wallet-Signature: 1SVVi80EB1FRp8tvPaGqRQ== |
| {    "Invoice":    {        "InvoiceId":337450307479,        "Amount":10.0000,        "CurrencyId":643,        "InvoiceStateId":"Created",        "CreateDate":"2016-08-04T07:43:47",        "UpdateDate":"2016-08-04T07:43:47",        "Payment":        {            "PaymentId":92180411,            "PaymentCode":"337450307479",            "PaymentCodeType":"InvoiceId",            "CreateDate":"2016-08-04T07:43:47.05",            "UpdateDate":"2016-08-04T07:43:47.05",            "PaymentStateId":"Created",            "PaymentTypeId":"MegafonRUB"        }    },    "CanSaveAsExternalAccount":"false",    "PayerCommissionAmounts": {        "MinCommissionAmount": 0.0,        "MaxCommissionAmount": 0.0    },    "InvoiceUrl": "http://wl.walletone.com/checkout/checkout/Index/?i=337450307479\&m=119973638979"} |

Для оплаты инвойса необходимо отправить запрос содержащий данные платежного инструмента:

| POST {baseUrl}/payments/123456/process |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2016-08-04T05:34:53 X-Wallet-UserId: 119973638979 |
| {    "AuthData":    {        "MobileCommercePhoneNumber":"9368956698",         "UserInfoIP":"123.123.123.123"    }} |

Ответ:

| HTTP/1.1 200 OK |
| :---- |
| X-Wallet-Timestamp: 2016-08-04T07:44:29X-Wallet-Signature: H49Q9kk/OxXCGHMNHNfc3g== |
| {    "Payment":    {        "PaymentId":123456,        "Amount":15.0000,        "CurrencyId":643,        "PaymentCode":"337450307479",        "PaymentCodeType":"InvoiceId",        "CreateDate":"2016-08-05T07:28:56.593",        "UpdateDate":"2016-08-05T07:28:56.593",        "PaymentStateId":"AuthRequired",        "PaymentTypeId":"MtsRUB"    },    "Authorization":    {        "AuthType":"PayerConfirmation",        "PayerConfirmationType":"SmsUssd"    }} |

Ответ содержит объект Authorization, с значением поля AuthType равным PayerConfirmation и полем  PayerConfirmationType со значением SmsUssd это означает что от пользователя требуется дополнительное действие, в данном случае ответ на присланную смс. 

## Оплата Viber кошельком {#оплата-viber-кошельком}

Процесс оплаты в кошельке Viber аналогичен процессу оплаты с банковской карты \- он также включает шаги создания инвойса и проведения платежа.  
Для создания инвойса понадобится метод “[Создание инвойса](https://docs.google.com/document/d/1jXNREvEQ6IUr5W5sdT7ZBy-VhhkgyE5EtVU0LjqtqOg/edit#heading=h.psz7i7txu81z)” c указанием "PaymentTypeId":"ViberRUB":

| POST {baseUrl}/invoices Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 {     "Amount":2.91,     "CurrencyId":643,     "PaymentTypeId":"ViberRUB",     "OrderId":"GQYOE-MJNPO-49623" } |
| :---- |

Ответ:

| HTTP/1.1 200 OK |
| :---- |
| X-Wallet-Timestamp: 2016-08-04T07:43:50X-Wallet-Signature: 1SVVi80EB1FRp8tvPaGqRQ== |
| {     "Invoice": {         "InvoiceId":341234567891,         "Amount":2.19,         "CurrencyId":643,         "InvoiceStateId":"Created",         "CreateDate":"2014-06-10T09:54:05.033",         "UpdateDate":"2014-06-10T09:54:05.033",         "Payment": {             "PaymentId":123456,             "PaymentCode":"341234567891,             "PaymentCodeType":"InvoiceId"             "CreateDate":"2014-06-10T09:54:05.033",             "UpdateDate":"2014-06-10T09:54:05.033",             "PaymentStateId":"Created",             "PaymentTypeId":"ViberRUB"         }         	      },        "CanSaveAsExternalAccount":"false" } |

Для отправки инвойса на оплату в Viber необходимо отправить запрос содержащий данные платежного инструмента:

| POST {baseUrl}/payments/{paymentId}/process Content-type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 {     "AuthData": {         "PhoneNumber":"79228807051",         \-- optional         "NotificationLanguage":"ru-RU",      \-- optional         "SchemeType":"web",                  \-- optional     } } |
| :---- |

**Описание параметров**

| Параметр | Описание параметра ответа |
| ----- | ----- |
| PhoneNumber | Поддерживается 2 режима работы:  Передан номер телефона клиента зарегистрированного в Viber: в этом случае пользователю будет направлено сервисное сообщение в Viber.  Параметр не передан: в этом случае в ответе на запрос будет сформирована ссылка на которую необходимо переадресовать пользователя для оплаты инвойса. |
| NotificationLanguage | Язык получаемого пользователем сервисного сообщения. Имеет смысл указывать только если передан номер телефона получателя сообщения. Поддерживаемые значения: ru-RU; en-US. |
| SchemeType | Задает схему для ссылки, которая будет возвращена в ответе при отсутствии параметра “PhoneNumber” в запросе. Поддерживаемые значения: web \- значение по умолчанию, в ответе будет стандартный http URL который можно открыть в любом браузере; mobile \- в ответе будет deeplink для мобильной платформы. |

**Используемый шаблон сервисного сообщения в Viber**

|  | Пример | RU | EN |
| ----- | ----- | ----- | ----- |
| **Текст на кнопке** | Оплатить 100 руб. | Оплатить ${Amount}  | Pay ${Amount}  |
| **Текст сообщения** | Вам выставлен счет. Получатель средств: Интернет-магазин Ozon.ru. Описание покупки: оплата заказа №1232. | Вам выставлен счет. Получатель средств: ${MerchantName}. Описание покупки: ${OrderDescription}. | You've received an invoice. Merchant: ${MerchantName}. Description of purchase: ${OrderDescription}. |
| **Изображение** | \<Не передается, или, в зависимости от настройки мерчанта, передается нормализованного лого мерчанта указанное в его настройках\> |  |  |

Ответ для режима 1 (отправка сервисного сообщения в Viber):

| HTTP/1.1 200 OK |
| :---- |
| X-Wallet-Timestamp: 2016-08-04T07:44:29X-Wallet-Signature: H49Q9kk/OxXCGHMNHNfc3g== |
| {     "Payment": {         "PaymentId":64115070,         "Amount":2.19,         "CurrencyId":643,         "PaymentCode":"345775450837",         "PaymentCodeType":"InvoiceId",         "CreateDate":"2015-10-29T21:00:04",         "UpdateDate":"2015-10-29T21:00:04",         "PaymentStateId":"AuthRequired",         "PaymentTypeId":"ViberRUB",         "ExternalAccountId":null     },     "Authorization": {         "AuthType":"PayerConfirmation"     } } |

Ответ для режима 2 (получение ссылки на оплату для переадресации пользователя):

| HTTP/1.1 200 OK |
| :---- |
| X-Wallet-Timestamp: 2016-08-04T07:44:29X-Wallet-Signature: H49Q9kk/OxXCGHMNHNfc3g== |
| {     "Payment": {         "PaymentId":64115070,         "Amount":2.19,         "CurrencyId":643,         "PaymentCode":"345775450837",         "PaymentCodeType":"InvoiceId",         "CreateDate":"2015-10-29T21:00:04",         "UpdateDate":"2015-10-29T21:00:04",         "PaymentStateId":"AuthRequired",         "PaymentTypeId":"ViberRUB",         "ExternalAccountId":null     },     "Authorization": {         "AuthType":"Redirect",         "HttpForm": {                    	              "Action":"https://wallet.integration.viber.com /pay?invoiceid=12f74e25-0221-4645-a79c-e6b5e277fced",             "Method":"GET"         }     } } |

Режим 2 имеет смысл использовать для случаев когда пользователь интернет магазина использует мобильное устройство, но это не запрещает использовать режим 1 

## Оплата Qiwi кошельком {#оплата-qiwi-кошельком}

Процесс оплаты с qiwi кошелька аналогичен процессам оплаты, описанным выше \- он также включает шаги создания инвойса и проведения платежа. Для создания инвойса понадобится метод “[Создание инвойса](https://docs.google.com/document/d/1jXNREvEQ6IUr5W5sdT7ZBy-VhhkgyE5EtVU0LjqtqOg/edit#heading=h.psz7i7txu81z)” c указанием "PaymentTypeId":"QiwiWalletRUB":

| POST {baseUrl}/invoices Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 {     "Amount":2.91,     "CurrencyId":643,     "PaymentTypeId":"QiwiWalletRUB",     "OrderId":"GQYOE-MJNPO-49623" } |
| :---- |

Ответ:

| HTTP/1.1 200 OK |
| :---- |
| X-Wallet-Timestamp: 2016-08-04T07:43:50X-Wallet-Signature: 1SVVi80EB1FRp8tvPaGqRQ== |
| {     "Invoice": {         "InvoiceId":341234567891,         "Amount":2.19,         "CurrencyId":643,         "InvoiceStateId":"Created",         "CreateDate":"2014-06-10T09:54:05.033",         "UpdateDate":"2014-06-10T09:54:05.033",         "Payment": {             "PaymentId":123456,             "PaymentCode":"341234567891,             "PaymentCodeType":"InvoiceId"             "CreateDate":"2017-09-13T07:13:23.983",             "UpdateDate":"2017-09-13T07:13:23.983",             "PaymentStateId":"Created",             "PaymentTypeId":"QiwiWalletRUB"         }         	      },    "CanSaveAsExternalAccount": false,    "PayerCommissionAmounts": {        "MinCommissionAmount": 0.0,        "MaxCommissionAmount": 0.0    },    "InvoiceUrl": "http://wl.walletone.com/checkout/checkout/Index/?i=341234567891\&m=119973638979" } |

Для оплаты инвойса необходимо отправить запрос содержащий данные платежного инструмента:

| POST {baseUrl}/payments/123456/process |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2016-08-04T05:34:53 X-Wallet-UserId: 119973638979 |
| {    "AuthData":    {        "PhoneNumber":"+79368956698"    }} |

Ответ:

| HTTP/1.1 200 OK |
| :---- |
| X-Wallet-Timestamp: 2016-08-04T07:44:29X-Wallet-Signature: H49Q9kk/OxXCGHMNHNfc3g== |
| {    "Payment": {        "MasterField": "79368956698",        "PaymentId": 123456,        "Amount": 2.19,        "CurrencyId": 643,        "PaymentCode": "340579408612",        "PaymentCodeType": "InvoiceId",        "CreateDate": "2017-09-13T07:13:23.983",        "UpdateDate": "2017-09-13T07:13:23.983",        "PaymentStateId": "Processing",        "PaymentTypeId": "QiwiWalletRUB"    },    "Authorization": {        "AuthType": "Redirect",        "HttpForm": { "Action":"https://wl.walletone.com/checkout/Process/123456/Index",            "Method": "GET"    }  }} |

Ответ содержит объект Authorization, указывающий на то, что требуется доп. авторизация. Значение поля AuthType отражает требуемое действие, а именно необходимо произвести перенаправление пользователя способом указанным в поле HttpForm.Method на урл HttpForm.Action.

## Оплата с помощью Яндекс денег {#оплата-с-помощью-яндекс-денег}

Процесс оплаты с яндекс денег аналогичен процессам оплаты, описанным выше \- он также включает шаги создания инвойса и проведения платежа. Для создания инвойса понадобится метод “[Создание инвойса](https://docs.google.com/document/d/1jXNREvEQ6IUr5W5sdT7ZBy-VhhkgyE5EtVU0LjqtqOg/edit#heading=h.psz7i7txu81z)” c указанием "PaymentTypeId":"YandexMoneyRub":

| POST {baseUrl}/invoices Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 {     "Amount":2.91,     "CurrencyId":643,     "PaymentTypeId":"YandexMoneyRub",     "OrderId":"GQYOE-MJNPO-49623" } |
| :---- |

Ответ:

| HTTP/1.1 200 OK |
| :---- |
| X-Wallet-Timestamp: 2016-08-04T07:43:50X-Wallet-Signature: 1SVVi80EB1FRp8tvPaGqRQ== |
| {     "Invoice": {         "InvoiceId":341234567891,         "Amount":2.19,         "CurrencyId":643,         "InvoiceStateId":"Created",         "CreateDate":"2014-06-10T09:54:05.033",         "UpdateDate":"2014-06-10T09:54:05.033",         "Payment": {             "PaymentId":123456,             "PaymentCode":"341234567891,             "PaymentCodeType":"InvoiceId"             "CreateDate":"2017-09-13T07:13:23.983",             "UpdateDate":"2017-09-13T07:13:23.983",             "PaymentStateId":"Created",             "PaymentTypeId":"YandexMoneyRub"         }         	      },    "CanSaveAsExternalAccount": false,    "PayerCommissionAmounts": {        "MinCommissionAmount": 0.0,        "MaxCommissionAmount": 0.0    },    "InvoiceUrl": "http://wl.walletone.com/checkout/checkout/Index/?i=341234567891\&m=119973638979" } |

Для оплаты инвойса необходимо отправить запрос содержащий пустой объект:

| POST {baseUrl}/payments/123456/process |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2016-08-04T05:34:53 X-Wallet-UserId: 119973638979 |
| {} |

Ответ:

| HTTP/1.1 200 OK |
| :---- |
| X-Wallet-Timestamp: 2016-08-04T07:44:29X-Wallet-Signature: H49Q9kk/OxXCGHMNHNfc3g== |
| {    "Payment": {        "MasterField": "79368956698",        "PaymentId": 123456,        "Amount": 2.19,        "CurrencyId": 643,        "PaymentCode": "340579408612",        "PaymentCodeType": "InvoiceId",        "CreateDate": "2017-09-13T07:13:23.983",        "UpdateDate": "2017-09-13T07:13:23.983",        "PaymentStateId": "Processing",        "PaymentTypeId": "YandexMoneyRub"    },    "Authorization": {        "AuthType": "Redirect",        "HttpForm": { "Action":"https://wl.walletone.com/checkout/Process/123456/Index",            "Method": "GET"    }  }} |

Ответ содержит объект Authorization, указывающий на то, что требуется доп. авторизация. Значение поля AuthType отражает требуемое действие, а именно необходимо произвести перенаправление пользователя способом указанным в поле HttpForm.Method на урл HttpForm.Action.

## Оплата через Альфа-Клик {#оплата-через-альфа-клик}

Процесс оплаты через Альфа-Клик  аналогичен процессам оплаты, описанным выше \- он также включает шаги создания инвойса и проведения платежа. Для создания инвойса понадобится метод “[Создание инвойса](https://docs.google.com/document/d/1jXNREvEQ6IUr5W5sdT7ZBy-VhhkgyE5EtVU0LjqtqOg/edit#heading=h.psz7i7txu81z)” c указанием "PaymentTypeId":"AlfaclickRub":

| POST {baseUrl}/invoices Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 {     "Amount":2.91,     "CurrencyId":643,     "PaymentTypeId":"AlfaclickRub",     "OrderId":"GQYOE-MJNPO-49623" } |
| :---- |

Ответ:

| HTTP/1.1 200 OK |
| :---- |
| X-Wallet-Timestamp: 2016-08-04T07:43:50X-Wallet-Signature: 1SVVi80EB1FRp8tvPaGqRQ== |
| {     "Invoice": {         "InvoiceId":341234567891,         "Amount":2.19,         "CurrencyId":643,         "InvoiceStateId":"Created",         "CreateDate":"2014-06-10T09:54:05.033",         "UpdateDate":"2014-06-10T09:54:05.033",         "Payment": {             "PaymentId":123456,             "PaymentCode":"341234567891,             "PaymentCodeType":"InvoiceId"             "CreateDate":"2017-09-13T07:13:23.983",             "UpdateDate":"2017-09-13T07:13:23.983",             "PaymentStateId":"Created",             "PaymentTypeId":"AlfaclickRub"         }         	      },    "CanSaveAsExternalAccount": false,    "PayerCommissionAmounts": {        "MinCommissionAmount": 0.0,        "MaxCommissionAmount": 0.0    },    "InvoiceUrl": "http://wl.walletone.com/checkout/checkout/Index/?i=341234567891\&m=119973638979" } |

Для оплаты инвойса необходимо отправить запрос содержащий пустой объект:

| POST {baseUrl}/payments/123456/process |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2016-08-04T05:34:53 X-Wallet-UserId: 119973638979 |
| {} |

Ответ:

| HTTP/1.1 200 OK |
| :---- |
| X-Wallet-Timestamp: 2016-08-04T07:44:29X-Wallet-Signature: H49Q9kk/OxXCGHMNHNfc3g== |
| {    "Payment": {        "MasterField": "79368956698",        "PaymentId": 123456,        "Amount": 2.19,        "CurrencyId": 643,        "PaymentCode": "340579408612",        "PaymentCodeType": "InvoiceId",        "CreateDate": "2017-09-13T07:13:23.983",        "UpdateDate": "2017-09-13T07:13:23.983",        "PaymentStateId": "Processing",        "PaymentTypeId": "AlfaclickRub"    },    "Authorization": {        "AuthType": "Redirect",        "HttpForm": { "Action":"https://wl.walletone.com/checkout/Process/123456/Index",            "Method": "GET"    }  }} |

Ответ содержит объект Authorization, указывающий на то, что требуется доп. авторизация. Значение поля AuthType отражает требуемое действие, а именно необходимо произвести перенаправление пользователя способом указанным в поле HttpForm.Method на урл HttpForm.Action.

## Оплата через банк-клиент Промсвязьбанк {#оплата-через-банк-клиент-промсвязьбанк}

Процесс оплаты через банк-клиент Промсвязьбанк аналогичен процессам оплаты, описанным выше \- он также включает шаги создания инвойса и проведения платежа. Для создания инвойса понадобится метод “[Создание инвойса](https://docs.google.com/document/d/1jXNREvEQ6IUr5W5sdT7ZBy-VhhkgyE5EtVU0LjqtqOg/edit#heading=h.psz7i7txu81z)” c указанием "PaymentTypeId":"PsbRetailRUB":

| POST {baseUrl}/invoices Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2015-01-07T21:45:33 X-Wallet-UserId: 119973638979 {     "Amount":2.91,     "CurrencyId":643,     "PaymentTypeId":"PsbRetailRUB",     "OrderId":"GQYOE-MJNPO-49623" } |
| :---- |

Ответ:

| HTTP/1.1 200 OK |
| :---- |
| X-Wallet-Timestamp: 2016-08-04T07:43:50X-Wallet-Signature: 1SVVi80EB1FRp8tvPaGqRQ== |
| {     "Invoice": {         "InvoiceId":341234567891,         "Amount":2.19,         "CurrencyId":643,         "InvoiceStateId":"Created",         "CreateDate":"2014-06-10T09:54:05.033",         "UpdateDate":"2014-06-10T09:54:05.033",         "Payment": {             "PaymentId":123456,             "PaymentCode":"341234567891,             "PaymentCodeType":"InvoiceId"             "CreateDate":"2017-09-13T07:13:23.983",             "UpdateDate":"2017-09-13T07:13:23.983",             "PaymentStateId":"Created",             "PaymentTypeId":"PsbRetailRUB"         }         	      },    "CanSaveAsExternalAccount": false,    "PayerCommissionAmounts": {        "MinCommissionAmount": 0.0,        "MaxCommissionAmount": 0.0    },    "InvoiceUrl": "http://wl.walletone.com/checkout/checkout/Index/?i=341234567891\&m=119973638979" } |

Для оплаты инвойса необходимо отправить запрос содержащий пустой объект:

| POST {baseUrl}/payments/123456/process |
| :---- |
| Content-Type: application/json; charset=utf-8 X-Wallet-Signature: 0J7Qv9C70LDRgtCwINC30LDQutCw0LfQsA== X-Wallet-Timestamp: 2016-08-04T05:34:53 X-Wallet-UserId: 119973638979 |
| {} |

Ответ:

| HTTP/1.1 200 OK |
| :---- |
| X-Wallet-Timestamp: 2016-08-04T07:44:29X-Wallet-Signature: H49Q9kk/OxXCGHMNHNfc3g== |
| {    "Payment": {        "MasterField": "79368956698",        "PaymentId": 123456,        "Amount": 2.19,        "CurrencyId": 643,        "PaymentCode": "340579408612",        "PaymentCodeType": "InvoiceId",        "CreateDate": "2017-09-13T07:13:23.983",        "UpdateDate": "2017-09-13T07:13:23.983",        "PaymentStateId": "Processing",        "PaymentTypeId": "PsbRetailRUB"    },    "Authorization": {        "AuthType": "Redirect",        "HttpForm": { "Action":"https://wl.walletone.com/checkout/Process/123456/Index",            "Method": "GET"    }  }} |

Ответ содержит объект Authorization, указывающий на то, что требуется доп. авторизация. Значение поля AuthType отражает требуемое действие, а именно необходимо произвести перенаправление пользователя способом указанным в поле HttpForm.Method на урл HttpForm.Action.

## Возвраты по принятым платежам {#возвраты-по-принятым-платежам}

Данное API позволяет интернет магазинам осуществлять возвраты, по ранее оплаченным платежам. Чтобы осуществить возврат по конкретному платежу, данный платеж должен находиться в статусе Paid и шлюз, через который прошел платеж, должен поддерживать данный функционал, на данный момент не все шлюзы поддерживают возвраты. 

Возвраты \- асинхронная операция и состоит из двух этапов:

- создание запроса на осуществление возврата по указанному платежу,  
- получение статуса запроса на осуществление возврата по указанному платежу.

В процессе обработки запроса на возврат по платежу, Refund изменяет свой статус до получения конечного, при этом могут возникнуть ошибки и по инициативе интернет магазина запрос можно повторить.

Не допускается повторная отправка запроса, если по указанному платежу ранее был создан Refund и он находится в следующих статусах (Created, Updated, Processing,  Accepted). В случае отправки запроса будет возвращена ошибка.

Для работы с возвратами в API предусмотрены 3 метода:

- [cоздание запроса на осуществление возврата](#создание-запроса-на-осуществление-возврата),  
- [получение информации о возврате](#получение-информации-о-возврате),  
- [получение информации о произведенных возвратах](#получение-информации-о-произведенных-возвратах).