---
stoplight-id: 9hmcuuvjpqo5o
---

# Реестры Мицелиум для Партнеров

# **Описание**

Реестры процессинга Мицелиум используются в интеграционном взаимодействии и проведения различных сверок между Мицелиумом и внешними по отношению к нему организациями.

Выгрузка данных идет в формате CSV, ежедневно, за прошедший день.

Для партнеров возможны следующие варианты получения реестров:

- рассылка на email (любое количество адресатов);
- выгрузка файлов на партнерский sftp/ftps.

## 1. TransferRegistry

#### (версия 1.0)
Описание: Покупки, выплаты, рефанды

**FileName:**
TransferRegistry.{PeriodFrom:yyyyMMddHHmmss}-{PeriodTo:yyyyMMddHHmmss}

Type: **csv**

| Название         | Тип      | Описание                                                                                           | Пример                               |
|------------------|----------|----------------------------------------------------------------------------------------------------|--------------------------------------|
| TransferId       | Int64    | ИД операции перевода м/у кошельками                                                                | 15979850                             |
| FromUserId       | Int64    | Отправитель  (walletNumberFrom)                                                                    | 100000405712                         |
| UserExternalId   | Int64    | ExternalId кошелька пользователя                                                                   | 5d117d21-feb9-4ecd-9beb-744d04a7ad36 |
| ToUserId         | Int64    | Получатель (walletNumberTo)                                                                        | 743551549997                         |
| TransferType     | String   | Тип перевода **                                                                                     | Transfer                             |
| CurrencyId       | Int32    | Валюта ***                                                                                          | 643                                  |
| Amount           | Decimal  | Сумма операции (safe + unsafe)                                                                     | 193573300                            |
| SafeAmount       | Decimal  | Сумма крашеных средств (marked amount). (Средства от Refill, с ними можно сделать только purchase) | 0.0000                               |
| UnsafeAmount     | Decimal  | Сумма неокраш. Средств (main body amount )                                                         | 193573300                            |
| CommissionAmount | Decimal  | Сумма комиссии по операции                                                                         | 0.0000                               |
| State            | String   | Статус   ****                                                                                      | Accepted                             |
| CreateDate       | DateTime | Дата создания                                                                                      | 2023-03-09T12:50:06                  |
| UpdateDate       | DateTime | Дата обновления операции                                                                           | 2023-03-09T12:50:06                  |
| ExternalId       | String   | Внешний ИД                                                                                         | 5a03c88f-4604-4bf4-a95e-7296ada4a9c3 |
| OperationType       | String  | Тип операции *                                         | PURCHASE                                                                                                                               |
| Description      | String   | Описание                                                                                           | test text                            |



`*` - возможные значения:

| Значение     | Тип операции       | 
|------------|------------------------|
| PAYOUT     | Выплата выигрыша       |   
| PURCHASE   | Покупка                |   
| REFILL     | Пополнение             |   
| WITHDRAW   | Вывод денег с кошелька |   
| REFUND     | Возврат денег          |   
| SMZ_PAYOUT | Выплата самозанятому   |   
| DONATE     | Донат                  |   


`**` - всегда Transfer

`***` - всегда 643 (рубль)

`****` - всегда Accepted, т.к. все реестры выгружают только COMPLETED операции




## 2. Registry

#### (версия 1.0)
Описание: Пополнения кошелька: Донаты, Рефил

**FileName:**
Registry.{PeriodFrom:yyyyMMddHHmmss}-{PeriodTo:yyyyMMddHHmmss}

Type: **csv**

| Название     | Тип      | Описание                                                                                                                     | Пример                                                                                                                                  |
|--------------|----------|------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|
| PaymentDate  | DateTime | Дата платежа                                                                                                                 | 2023-02-17 10:29:28                                                                                                                     |
| TransferId   | Int64    | ИД проводки                                                                                                                  | 15978380                                                                                                                                |
| UserId       | Int64    | ИД кошелька (walletNumber)                                                                                                   | 776123780502                                                                                                                            |
| UserExternalId       | Int64    | ExternalId кошелька пользователя                                                                                                    | 5d117d21-feb9-4ecd-9beb-744d04a7ad36                                                                                                                            |
| TargetAmount | Decimal  | Сумма операции (body amount) (Сумма на крашеный счёт + сумма на оверпей крашенный) полная сумма операции, без учета комиссии | 15.5500                                                                                                                                 |
| Refund       | Decimal  | Сумма вознаграждения/комиссии (rnkoComission = walletFrom: Partner, walletTo: rnko )                                         | 2774 400                                                                                                                                |
| PaymentType  | String   | Тип платежа                                                                                                                  | CREDITCARDRUS; MTC; MEGAFON; YOTA; BEELINE; TELE2; QIWI; TINKOFFMONEY; PSB; YANDEXMONEY; ALFACLICK; RUSSIANSTANDARDBANK; MOBILECOMMERCE |
| OperationType       | String  | Тип операции *                                         | REFILL                                                                                                                                |

`*` - возможные значения:

| Значение     | Тип операции       | 
|------------|------------------------|
| REFILL     | Пополнение             |   
| DONATE     | Донат                  |   

## 3. PaymentRegistry

#### (версия 1.0)


**FileName:**
PaymentRegistry.{PeriodFrom:yyyyMMddHHmmss}-{PeriodTo:yyyyMMddHHmmss}

Type: **csv**

| Название         | Тип      | Описание                                                                                                                                     | Пример                      |
|------------------|----------|----------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------|
| PaymentId        | Int64    | ИД платежа Withdraw (идентификатор процессинга - отображается на странице вывода после подтверждения операции) (withdraw request confirm Id). Данное поле актуально для операции withdraw с подтверждением, если операция withdraw без подтверждения - поле будет пустым | 43                          |
| TransferId       | Int64    | ИД перевода (в реестре в1 - уходит ИД проводки) Можно слать operation TransferId                                                             | 15979239                    |
| UserId           | Int64    | Номер кошелька (WalletNumber)                                                                                                                | 709114810366                |
| UserExternalId       | Int64    | ExternalId кошелька пользователя                                                                                                    | 5d117d21-feb9-4ecd-9beb-744d04a7ad36                                                                                                                            |
| ProviderTitle    | String   | Название провайдера                                                                                                                          | Qiwi; Карточный вывод; МТС  |
| ProviderId       | String   | ИД провайдера                                                                                                                                | emt2; mts1; vqwq            |
| Amount           | Decimal  | Сумма операции без комиссии (4 знака после разделителя)  (body проводка)                                                                     | 132044200                   |
| CommissionAmount | Decimal  | Сумма комиссии по платежу(4 знака после разделителя) (комиссия с пользователя)                                                               | 29.8000                     |
| CreateDate       | DateTime | Дата создания (CreatedAt)                                                                                                                    | 2023-02-17T09:23:38         |
| PaymentDate      | DateTime | Дата проведения платежа (completedAt)                                                                                                        | 2023-02-17T10:30:37         |
| MasterField      | String   | Ключевое поле (реквизит платежа) **                                                                                                          | 1234567893 411111******1111 |
| CurrencyId       | Int32    | Валюта *                                                                                                                                     | 643                         |

`*` - всегда 643 (рубль)

`**` - "4111*********" для номера карты. Телефоны не маскируем


## 4. TransferCreditRegistry (W1)

#### (версия 1.0)


**FileName:**
TransferCreditRegistry.{PeriodFrom:yyyyMMddHHmmss}-{PeriodTo:yyyyMMddHHmmss}


Type: **csv**

| Название         | Тип      | Описание                                                                                           | Пример                               |
|------------------|----------|----------------------------------------------------------------------------------------------------|--------------------------------------|
| DATE       | DateTime    | Дата перехода транзакции в терминальный статус                                                                | 2023-02-17 10:29:28                            |
| PRICE       | Decimal    | 	Сумма транзакции                                                            | 14985.00                         |
| W1_TRNS_ID         | long    | id транзакции в W1 *                                                                        | 1555842                         |
| GATE_TRNS_ID     | String   | 	id транзакции, передаваемый внешней системой*                                                                                     | a5a4a6f8-c6fd-11ed-b977-bf2af79b5e1f                             |

`*` - целое число, как счетчик

## 5. UserKycStateRegistry

#### (версия 1.0)


**FileName:**
UserKycStateRegistry.{PeriodFrom:yyyyMMddHHmmss}-{PeriodTo:yyyyMMddHHmmss}

Type: **xml**

Пример:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Registry DateEnd="2020-10-01" DateStart="2020-09-30" >
 <Users>
  <User UserId="100019463885" KycDate="2020-09-30T12:06:02.927" KycLevel="0" CreateDate="2020-09-30T12:06:02.927"/>
  <User UserId="100021307606" KycDate="2020-09-30T15:17:30.317" KycLevel="0" CreateDate="2020-09-30T12:06:02.927"/>
  <User UserId="100042002262" KycDate="2020-09-30T17:03:55.840" KycLevel="0" CreateDate="2020-09-30T17:03:55.840"/>
  <User UserId="100044724670" KycDate="2020-09-30T17:34:43.623" KycLevel="0" CreateDate="2020-09-30T17:34:43.623"/>
  <User UserId="100061961762" KycDate="2020-09-30T09:01:36.867" KycLevel="0" CreateDate="2020-09-30T09:01:36.867"/>
  <User UserId="100111711785" KycDate="2020-09-30T09:15:31.863" KycLevel="0" CreateDate="2020-09-30T09:15:31.863"/>
  <User UserId="100120018111" KycDate="2020-09-30T07:16:23.987" KycLevel="0" CreateDate="2020-09-30T07:16:23.987"/>
  <User UserId="100129031250" KycDate="2016-08-08T06:41:05.800" KycLevel="5" CreateDate="2016-08-08T06:41:05.800"/>
 </Users>
</Registry>
```


| Название            | Тип      | Описание                       |   |
|---------------------|----------|--------------------------------|---|
| DateStart           | DateTime | Дата начала периода реестра    |   |
| DateEnd             | DateTime | Дата окончания периода реестра |   |
| UserId              | Int32    | Id пользователя                |   |
| KycDate             | DateTime | Дата проведения идентификации  |   |
| KycLevel            | Int32    | Уровень ИД                     |   |
| CreateDate          | DateTime | Дата создания (пользователя)?  |   |


## 6. UnboundUserRegistry

#### (версия 1.0)


**FileName:**
UnboundUserRegistry.{PeriodFrom:yyyyMMddHHmmss}-{PeriodTo:yyyyMMddHHmmss}

Описание: реестр отвязавшихся пользователей СМЗ от платформы из ФНС

Type: **csv**

|      Столбец      |                          Пояснение                         |            Пример формата            | 
|:--------------|:----------------------------------------------------------|:------------------------------------|
| walletNumber   | Кошелек пользователя                                       | 717849038833                         | 
| UserExternalId | Внешний id. ExternalId, используемый при создании кошелька | 5a03c88f-4604-4bf4-a95e-7296ada4a9c3 |
| Inn            | ИНН пользователя                                           | 520205004556                         | 
| UnboundDate    | Дата отвязки                                               | 2020-09-30 17:03:55                  | 


## 7. PartnerBalanceRegistry
#### (версия 1.0)

Описание:
Реестр по балансу кошелька партнера за прошедший день.


**FileName:**
PartnerBalanceRegistry.{PeriodFrom:yyyyMMddHHmmss}-{PeriodTo:yyyyMMddHHmmss}

Type: **csv**

| Название         | Тип      | Описание                                                                                           | Пример                               |
|------------------|----------|----------------------------------------------------------------------------------------------------|--------------------------------------|
| Date       | date    | Дата, за которую выгружается реестр в формате DD.MM.YYYY                                          | 15.07.2024                           |
| BalanceStartDay       | real    | Баланс кошелька партнера на начала дня выгрузки реестра (прошлый день 00:00:00)                                                               | 1900000.63                         |
| BalanceFinishDay   | real    | Баланс кошелька партнера на конец дня выгрузки реестра (прошлый день 23:59:59)                                             | 1800000.63 |
| IncomePartnerBalance         | real    | Сумма пополнений баланса кошелька партнера | 1000000.00      
| OutcomePartnerBalance         | real    | Сумма уменьшений баланса кошелька партнера | 1000000.00                      |
| PayoutToUsers     | real   | Сумма переводов с кошелька партнера на кошельки пользователей (payout и payoutSMZ) | 500000.00            
| PurchaseUsers     | real   | сумма всех операций purchase| 100000.00                      |
| CancelAmount       | real    | Сумма отмен всех операций payout (refund)| 150.00                                |


