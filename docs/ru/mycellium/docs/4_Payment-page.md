---
stoplight-id: 7807ltfavdido
---

# Платежные страницы


###### История изменений документа
(версия 1.0)


| Дата       | Версия документа | Описание                          
| ---------- | ---------------- | --------------------------------- 
| 03.07.2024 | 1.0              | Создана страница




Платежные страницы, предназначены для взаимодействия клиентов партнеров 
Mycellium с сервисами 
Mycellium:
- KYC идентификация (ФНС/Госуслуги);
- Подача заявок на полную идентификацию;
- Внесение средств на счет клиента;
- Вывод средств со счета клиента;
- Просмотр истории транзакций, связанных с аккаунтом клиента.


Клиент может пополнять кошелек любым из доступных способов. 
(Настройка, включение/отключение способов утверждается по договору с Партнером)

Для обращения к платежным страницам используется метод "**GET /bySignature**".

## **Безопасность**

Подлинность передаваемых данных обеспечивается использованием секретного ключа Партнера. Выработка, обмен, и прочие действия с ключевой информацией осуществляются в рамках Соглашения об Электронном документообороте.




#### GET /bySignature

Для перехода на платежные страницы необходимо перенаправить пользователя в 
Mycellium, сделав форму (или сгенерировать ссылку) на адрес https://partnername.w1.money/authorize/bySignature со следующими параметрами:

> Параметры необходимо передавать в алфавитном порядке.

> Все параметры необходимо экранировать, т.е. производить UrlEncode.

**Параметры строки запроса:**

| **Название параметра** | **Тип данных** | **Обязательность** | **Описание** |
| --- | --- | --- | --- |
| **account** | Long | + | id пользователя (12 цифр) |
| **date** | DateTime | + | Время создания запроса по UTC. (Может быть в пределах +- 10 минут) |
| **esiaKycRedirectUrl** | String | - | URL-ссылка для перенаправления пользователя после ИД через Госуслуги. Если параметр отсутствует, после ИД на Госуслугах пользователя возвращает на интерфейс платежных страниц |
| **identType** | String | - | Тип идентификации (описание возможных значений в справочнике) |
| **isOperator** | String | - | Роль инициатора (описание возможных значений в справочнике) |
| **mobileSiteUrl** | String | - | URL-ссылка  |
| **partnerId** | String | + | Идентификатор партнера |
| **redirectto** | String | + | Адрес назначения для перенаправления  |
| **refillFailUrlMobile** | String | - | Адрес назначения, в случаи ошибки |
| **refillSuccessUrlMobile** | String | - | Адрес назначения, в случаи успешной обработки |
| **theme** | String | - | Тема платежных страниц (Можно договорится о индивидуальных цветах на платежных страницах для партнера ) |
| **sign** | String | + | Подпись,  формируется приватным ключом партнера* |
| **transactionId** | String | - | Номер транзакции |


#### Формирование sign

***sign=BASE64(SignatureMethod(UTF8(URL))), где:**

| **Наименование** |  **Описание** |
| --- | --- |
| **BASE64** | Функция преобразования бинарных данных (массива байт) к BASE64 строке |
| **SignatureMethod** | Криптографическая функция используемая для вычисления ЭЦП (метод подписи ключа Партнера SHA1withRSA) |
| **UTF8** | Функция, которая преобразовывает строку к кодировке UTF8 и возвращает массив байт |
| **URL** | Url-адрес, на который направляется запрос *|


*Параметры, передаваемые в **URL**:

| **Название параметра** | **Тип данных** | **Описание** |
| --- | --- | --- |
| **account** | Long | id пользователя (12 цифр) |
| **date** | DateTime | Время создания запроса по UTC. Может быть в пределах +- 10 минут (Время создания запроса по UTC. Может быть в пределах +- 10 минут) |
| **partnerId** | String | Идентификатор партнера |
| **redirectto** | String | Адрес назначения для пользователя, кодированный в UrlEncode |

Возможные значения для **redirectto**
| **Значение** | **Описание** |
| --- | --- |
| **/info/history** | Страница истории операций пользователя |
| **/Kyc** | Страница заявки на идентификацию (УПРИД, Полная идентификация) |
| **/refill** | Страница ввода денежных средств |
| **/refill?id=agentId** | Страница ввода денежных средств с предустановленными agentId * |
| **/withdraw** | Страница вывода денежных средств |
| **/withdraw?id=provairderId** | Страница вывода денежных средств с предустановленными provairderId ** |

*agentId - id агента в системе Mycellium

**provairderId - id провайдера в системе Mycellium

\*Пример URL для подписи:
```json
https://partnername.w1.money/api/bySignature?account=123456789012&date=2022-12-14T15:02:45&redirectto=%2Frefill&refillSuccessUrlMobile=https:%2F%2Fwww.partnername.ru%2Fprivate%2Fwallet%2Frefill%2Fsuccess&refillFailUrlMobile=https:%2F%2Fwww.partnername.ru%2Fprivate%2Fwallet&mobileSiteUrl=https:%2F%2Fwww.partnername.ru%2Fprivate%2Fwallet
```



> Параметры необходимо передавать в алфавитном порядке.


# Пример вызова GET /bySignature

## **Request**

### **Header:**
```json
Request method:	GET
Request URI:	http://10.202.37.1:3008/bySignature?account=701234567890&date=2023-02-09T16%3A33%3A05&partnerId=5f7b2f86bed7df7e9b2bc500&redirectto=%2Finfo%2Fhistory&sign=g2o7JrkMrCTjy49MJ%2FQlQIWxBApr7Ggt%2B4tH4JPY1mHGtxrRLbP1mpGtV9ad6%2BjPN0EMcZBrWIUFhjZf4owbnH3sYwRMlEQNA907OWrXT6GkO8h5sPKqmQdr44n0OJQ0XS%2F%2F4kr67STR7VnCwNM95P%2FAyaP1GiypwplOTaPcgijvYHu%2F5UkeodpIPQMCBkJXU3BnzhySJpmFMiej48TkiQiPtQIyXxLGQgOLz6F8LUEa%2FR9dk6ggeaOyF4dpExg9%2FxNOjNZTIIVdPEnaAfeGtPF5tYbT%2Fob955jGlkNCnw57TBW8A6fEAVM4FsGgvfCI%2FLJsfuSt9JH7qTqzgkL%2B6A%3D%3D
Proxy:			<none>
Request params:	account=701234567890
				date=2023-02-09T16:33:05
				partnerId=5f7b2f86bed7df7e9b2bc500
				redirectto=/info/history
				sign=g2o7JrkMrCTjy49MJ/QlQIWxBApr7Ggt+4tH4JPY1mHGtxrRLbP1mpGtV9ad6+jPN0EMcZBrWIUFhjZf4owbnH3sYwRMlEQNA907OWrXT6GkO8h5sPKqmQdr44n0OJQ0XS//4kr67STR7VnCwNM95P/AyaP1GiypwplOTaPcgijvYHu/5UkeodpIPQMCBkJXU3BnzhySJpmFMiej48TkiQiPtQIyXxLGQgOLz6F8LUEa/R9dk6ggeaOyF4dpExg9/xNOjNZTIIVdPEnaAfeGtPF5tYbT/ob955jGlkNCnw57TBW8A6fEAVM4FsGgvfCI/LJsfuSt9JH7qTqzgkL+6A==
Query params:	<none>
Form params:	<none>
Path params:	<none>
Headers:		Accept=application/json, application/javascript, text/javascript, text/json
				Content-Type=application/json
Cookies:		<none>
Multiparts:		<none>
Body:			<none>
HTTP/1.1 200 
Vary: Origin
Vary: Access-Control-Request-Method
Vary: Access-Control-Request-Headers
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Pragma: no-cache
Expires: 0
X-Frame-Options: DENY
Content-Type: application/json
Content-Length: 667
Date: Thu, 09 Feb 2023 16:33:06 GMT
Keep-Alive: timeout=60
Connection: keep-alive
```


### **Response 200:**
```json

{
    "token": "eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiI3NjczODA3MDQ5NzIiLCJob2xkQXV0aG9yaXRpZXMiOm51bGwsImNyZWF0ZWQiOjE2NzU5NjAzODU5NjMsInN5c3RlbVVzZXJJZCI6IjYzZTUyMDQxZTkxNGI5MzYwOWEzNDVmNSIsImV4cCI6MTY3NTk2NjM4NSwiYXV0aG9yaXRpZXMiOlsiVVNFUiJdfQ.babuW1CFgpiH8jbr0haWGQqgCZA7OX-Ryqo0inn5pJ8KjaOFs5bJXmS57rV11F53adxhJGUP4t-oafCXSANi9Q",
    "refreshToken": "eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiI3NjczODA3MDQ5NzIiLCJob2xkQXV0aG9yaXRpZXMiOlsiVVNFUiJdLCJjcmVhdGVkIjoxNjc1OTYwMzg2NjAwLCJzeXN0ZW1Vc2VySWQiOiI2M2U1MjA0MWU5MTRiOTM2MDlhMzQ1ZjUiLCJleHAiOjE2NzYwNDY3ODYsImF1dGhvcml0aWVzIjpbIlRPS0VOX1JFRlJFU0giXX0.xXRfx1Mj7_TWv5ZjYwNGsf2fZmOUmFTQ9NOOKHWtM84cRiGBwYSpK8OtW-Q_Fi4S3oMxZ0C9HvLesT0ovvQPPg"
}
```



###  **Пример реализации на Node.js:**
```js
const NodeRSA = require('node-rsa');

function generateSign(url: string): string {
  /**
   * Считываем файл приватного ключа с использованием кодировки UTF-8
   *
   * @param PATH_TO_PRIVATE_KEY путь до файла
   */
  const privateKey = fs.readFileSync('PATH_TO_PRIVATE_KEY', 'utf8');

  /**
   * Создаем экземпляр ключа NodeRSA и
   * устанавливаем схему для подписи pkcs1-sha1
   *
   * Ознакомиться с библиотекой можно по {@link https://www.npmjs.com/package/node-rsa#create-instance ссылке}
   *
   * Подробнее о параметрах можно узнать {@link https://www.npmjs.com/package/node-rsa#options тут}
   */
  const rsaKey = new NodeRSA(privateKey);
  rsaKey.setOptions({ signingScheme: 'pkcs1-sha1' });

  /**
   * Подписываем ключ с помощью URL и
   * кодируем методом BASE64
   */
  const sign = rsaKey.sign(url, 'base64');

  /**
   * Возвращаем результат с экранированием символов
   */
  return encodeURIComponent(sign);
}

```

###  **Пример реализации на PHP 5.6:**
```php

   * @param string $string
   *  Строка, участвующая в формировании подписи.
   * $absolutePathToCertificate
   *  Абсолютный путь к сертификату.
   * @param string $method
   *  Метод шифрования, SHA1withRSA.
   * @return string
   */
  public function getSign($string, $method) {
    


//извлечение из сертификата приватного ключа
$certs = array();
$text = file_get_contents($absolutePathToCertificate); 
openssl_pkcs8_read($text, $certs, $certificatePassw);
    	$secretKey = openssl_get_privatekey($certs['pkey']);	

//формирование подписи через секретный ключ и подпись
    	openssl_sign($string, $sign, $secretKey, $method);
    	openssl_free_key($secretKey);
    	$sign = base64_encode($sign);

    return $sign;
  }

/**
   * @param string $account
   *  Id аккаунта
   * @param string $partnerId
   *  Id партнера системе Mycellium
   * @param string $redirectTo
   *  Url для редиректа
   * @return string
   */
  public function getLink($account, $partnerId, $redirectTo) {
$actionUrl = 'http://domain/authorize/bySignature';
$date = gmdate('Y-m-d\TH:i:s', time());
$redirectTo = urlencode($redirectTo);

$link = $actionUrl.'?'
.'account='.$account
.'&date='.$date
.'&redirectto='.$redirectto;
$sign = urlencode(getSign($link));

$link = $link.'&sign='.$sign;

return $link;
  }
```



