---
stoplight-id: x018xzwx0dlp3
---

# Работа с API



# Формирование запросов для API



## Request

Каждый запрос к API проходит процесс аутентификации. Это осуществляется путём подписания данных, передаваемых в запросе, партнёром и последующей проверкой подписи на сервере.

Для аутентификации необходимо передать три параметра в заголовке (Header) каждого запроса:

- PartnerId - уникальный идентификатор партнёра.
- Signature - запрос, подписанный асимметричным ключом, рассчитывается по формуле \*
- Timestamp - время отправки запроса в формате (yyyy-MM-dd'T'HH:mm:ss), например, 2023-01-16T07:35:15. Время не должно быть старше 5 минут, иначе запрос не пройдет аутентификацию. Это время должно быть указано в UTC+0.

`*` - `Signature = BASE64(SignatureMethod(UTF8(URL + Timestamp + PartnerId + Request body)))`

Параметры:

| Наименование    | Описание                                                                                                                                                                                  |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| BASE64          | Функция, преобразующая бинарные данные (массив байт) в строку BASE64.                                                                                                                     |
| SignatureMethod | Криптографическая функция, используемая для вычисления электронной цифровой подписи (метод подписи ключа партнёра SHA1withRSA).                                                           |
| UTF8            | Функция, преобразующая строку в кодировку UTF8 и возвращающая массив байт.                                                                                                                |
| URL             | URL-адрес, на который направляется запрос, включая протокол, домен и параметры запроса. (Должен полностью совпадать с URL запроса)                                                        |
| Timestamp       | Время отправки запроса. (Должно совпадать с аналогичным параметром в заголовке)                                                                                                           |
| partnerId       | Идентификатор партнёра. (Должен совпадать с аналогичным параметром в заголовке)                                                                                                           |
| Request body    | JSON-строка, представляющая тело запроса. Необходимо соблюдать алфавитный порядок полей для подписания. Должны отсутствовать пробелы и переносы строк. (Должна совпадать с телом запроса) |

> Необходимо соблюдать алфавитный порядок полей в Request body для подписания, в body должны отсутствовать пробелы и переносы строк.



### Пример json request (метод RegisterUser):

```json
Request method:	POST
Request URI:	http://10.10.10.10/wallet
Proxy:			<none>
Request params:	<none>
Query params:	<none>
Form params:	<none>
Path params:	<none>
Headers:		Accept=application/json, application/javascript, text/javascript, text/json
				partnerId=5f7b2f86bed7df7e9b2bc500
        Signature=HKdr7LY8GMPO+GbErGkNzLWF5LvrWBy0mm+zo+RnTBZUNNb+wiTLFXle6gTYHTeZ6AyWntRg6HUWzeJ8fKJsHV5v+ICY1J+5UeW
				Timestamp=2023-02-07T16:54:45
				Content-Type=application/json
Cookies:		<none>
Multiparts:		<none>
Body:
{
    "email": "test@test.com",
    "externalId": "79281234567"
}
```

Request body из этого примера (т.е. строка для подписания):
`{"email":"test@test.com","externalId":"79281234567"}`

### Пример формированиея подписи PHP 5.6:

```json
* @param string $url
   *  Url, участвующий в формировании подписи.
   * @param string $timestamp
   *  Время, участвующее в формировании подписи.
   * @param string $bodyString
   *  Request body в виде строки, участвующий в формировании подписи.
   * $absolutePathToCertificate
   *  Абсолютный путь к сертификату.
   * @param string $encryptMethod
   *  Метод шифрования, SHA1withRSA.
   * @return sign
   */
  public function getSign($url, $timestamp, $bodyString, $encryptMethod) {
    
//извлечение из сертификата приватного ключа
$certs = array();
$text = file_get_contents($absolutePathToCertificate); 
openssl_pkcs8_read($text, $certs, $certificatePassw);
    	$secretKey = openssl_get_privatekey($certs['pkey']);	

$sign;
$stringForSign = $url + $timestamp + $bodyString;
//формирование подписи через секретный ключ и подпись
    	openssl_sign($stringForSign, $sign, $secretKey, $encryptMethod);
    	openssl_free_key($secretKey);
    	$sign = base64_encode($sign);

    return $sign;
  }
```

### Пример составления запроса через openssl (метод GET User Balance):

```openssl
DATE=$(date -u +"%FT%T");
WALLET='ПОДСТАВИТЬ_walletNumber';
ADDRESS='ПОДСТАВИТЬ_АДРЕС';
PARTNER='ПОДСТАВИТЬ_partnerId';
PRIVKEY='ПОДСТАВИТЬ_ПУТЬ_ДО_СЕРТИФИКАТА';

STRING_TO_SIGN=$(printf "%s%s%s" "http://${ADDRESS}/wallet/${WALLET}/balance" "${DATE}" "${PARTNER}")

SIGNATURE=$(echo -n "${STRING_TO_SIGN}" | openssl dgst -sha1 -sign "${PRIVKEY}" | base64 | tr -d '\n')

curl -v -H "Signature: ${SIGNATURE}" -H "partnerId: ${PARTNER}" -H "Timestamp: ${DATE}" -H "Content-Type: application/json" "http://${ADDRESS}/wallet/${WALLET}/balance"
```

## Response

На каждый запрос API приходит ответ, который включает:

- Headers: IncomingSignature, ResponseSignature;
- код ответа;
- тело ответа.

| Наименование      | Описание                                                                                                                                                                                                                          |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| IncomingSignature | Подписанный запрос, который пришел в Header Signature.                                                                                                                                                                            |
| ResponseSignature | Подписанный ответ (body response со стороны API подписывается private ключом и передается в Header. ResponseSignature необходим для верификации открытого body Response и его подписанного аналога, который содержится в Header). |

### Пример Response:

```json
HTTP/1.1 200 
IncomingSignature: RSyIKQnrfBSLUOjRWmb0jWfOF/of2ekJ8azwLgY6F0OG6LDfD6j4afPQN6DyYHNR3u06r9ZnaW1HTxWAhyQWCek+l0e/8fbd2pkJY23Ai96/U9ThafXg/RZsZbX594eHQJmPFCB1LjVnKmUevRnF+Mxw3E9pN+YdjdFiGjrQRoZjUj5NQsEGC0wqwNPHqDx9432MOKajUEtnJD3zLa3in6UthFtaDp+y4uB1lkTucE2RXo96YHnm0JfZjSAdt64x+d6bMaYGTmooOIA+DGJSuYz5MYsW+fw0WOtv4aGabzbBuEJSoTwuJPUy1V4XxAKH6eX57QHmx/q1rzc/GnHf9Q==
ResponseSignature: LuB80mi0iTMtgjI/8+1EO8mxzm2ZvBzNKPngL4suF4PWW8c2l/XFOzyroD0zzKCuRGW+M7vX94BugzFUWMXITl4tSxuRsktYkXNdAb1VEIOn/nugYcqjwsRCRCliXwbN4cFdNvYXUANPAEHgUZhzQX4xbVGsZqF0mqHUh8LfiDp5y5cZwuBDxmyZ+Eucqod/W6AjDPD1IFwewtmRp45Yrit74qFnVdB2pLfHM4/vMXpT+tgS3qcgrvHRp3DpcUswi9NJeRkvW5ofyh1Qkilzjhei4V7tqFzbEGy7BAX0ugo1AF02EuSqhFWD2KgEmWTm1TrWW15uPlTsF3SW9QS8+A==
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Pragma: no-cache
Expires: 0
X-Frame-Options: DENY
Content-Type: application/json
Content-Length: 52
Date: Mon, 13 Feb 2023 20:59:44 GMT
Keep-Alive: timeout=60
Connection: keep-alive

{
    "registerUserResult": {
        "walletNumber": 736346605141
    }
}
```

### Проверка ResponseSignature при получении ответа от API

Для проверки и предотвращения подмены ответа от API в заголовке (header) ответа возвращается параметр ResponseSignature. Он формируется путём подписания тела ответа (response body) с использованием приватного ключа.

Для верификации необходимо декодировать значение ResponseSignature из Base64 и проверить его с помощью алгоритма SHA1withRSA.

> Данные, передаваемые в метод verify, должны быть телом ответа, отсортированным по алфавиту, без пробелов и переносов строк, с учётом camelCase.

Пример тела ответа (response body):
`{"userBalance":{"amount":1400.00,"availableAmount":1400.00,"currencyId":643,"kycLevel":5,"kycRequestStateId":"ACCEPTED","overLimitAmount":0.00,"overdraft":0,"safeAmount":0.00}}`

### Пример кода реализации проверки ResponseSignature на Java:

```java
public static boolean verifySign(String publicKeyPath, String sign, byte[] data) {
        byte[] signature = Base64.getDecoder().decode(sign);
        PublicKey publicKey = getPemPublicKeyFromP7b(getFileContent(publicKeyPath));
        try {
            Signature s = Signature.getInstance("SHA1withRSA");
            s.initVerify(publicKey);
            s.update(data);
            return s.verify(signature);
        } catch (Exception cause) {
            throw new IllegalStateException("Failed verify signature. " + cause.getMessage());
        }
    }
```

### Проверка ResponseSignature с помощью OpenSSL:

1. Извлекаем ключ из полученного сертификата от Mycellium:

`openssl x509 -in publicCertificate.pem -noout -pubkey  > pubkey.pem`
2\. Копируем подпись из header ResponseSignature в файл responseSignature.txt
3\. Декодируем эту подпись из Base64 и кладем в файл
`base64 --decode responseSignature.txt > responseSignatureDecodedBase64.txt`
4\. Json body из response cортируем, удаляем переносы строк и пробелы и кладем в файл responseBody.txt
5\. Верифицируем Json body из response и подпись из header ResponseSignature
`openssl sha1 -verify pubkey.pem -signature responseSignatureDecodedBase64.txt responseBody.txt`
