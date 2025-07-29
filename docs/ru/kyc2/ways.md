# Способы проверки

KYC2 предоставляет различные способы проверки для различных сцеариев использования. На данный момент доступно два вида:

1. проверка по стоп-базам
1. проверка с помощью методов идентификации
1. ввод данных пользователем на страницах KYC2

Ниже рассмотрим каждый способ и его сецнарии отдельно:

## Проверка по стоп-базам

### Синхронная проверка

Этот способ предназначен для синхронной проверки данных участников операции в момент ее выполнения. Реализуют его методы контроллера [`CheckByStopBases`](openapi/CheckByStopBases/). Так как для проверки по стоп-базам не требуется обращения к внешним источникам и актуальные списки загружаются напрямую в сервис, методы отрабатывают очень быстро.

Пример вызова:

![ways](https://plantuml.w1.money/png/LOqn2i9044NxFSMGLaB0qYAWbXO6OsEvXYc2HjVT2PXEOcFX8VG6GOWM0Rt2x8tS5IsiPlZlpPzXHbiDYQnGWpIWX9AQyYnNSaCGkBn0jwGLOH5u0VVqhqmMu_w7hAfC21N6pkZr1i1NVlADMtkm9sxShC45B-wuiRKz2W3V5SRkpEl9D9r395KU6TggS2aDXXft9HhogaOgzGOy6paqGrEkINnJld3B3tlapfvjxVlXzyY1EpTl "ways")

### Асинхронная проверка

Этот способ подходит для случаев, когда проверка может выполняться какое-то время или по какой-то иной причине такой способ интеграции удобен. Однако, так как такой способ предполагает проверку лишь по одному справочнику за раз, удобнее использовать проверку с помощью `метода идентификации`.

Пример вызова:

![ways](https://plantuml.w1.money/png/TOzFIq915CNtyoa6j_PGNBeGIIIYHQ9jMaxFeHxlZzFCFF0HGJuX20ANxQAFy92UMf9-XNE_KOmL9D7odlixzvpRDbPecyOHOqfe6_Y14edbFP59pONXQa-kTCTMwaH43eXbOlaWCrR65OVAiTs3Jyux3QU7cS-OybfoRAlL9iSpJMXA3oXfGZdcMDCC9UKSMwnHK8uI2yOvTmUzLfZvpjKx7Ppncb11pTYHyYw4aPwMrwaqTiTgQLETy4QzFWgPCtejrUHuGa4ppF6EWjC46opnYW8VA0uuDbXWjPlVNEgVpAFkVv6r6oST2YCxLz8FW-Io_xKu7jx-hlD3zQL98yk-LRoWn9AcMDCZvUvbrsMBakvGq3rMM37MbiamZQDF "ways")

## Методы идентификации

Методы идентификации - основной способ проверки в сервисе, так как они предоставляют возможность вызывать сервис-провайдеров, а так же комбинировать проверки по разным источникам, что существенно упрощает интеграцию.

Для получения доступного списка методов необходимо использовать метод [`GET/api/identification-methods`](openapi/IdentificationMethods/apiidentification-methods-get.html). Не все методы доступны всем партнерам, поэтому, если нужного вам нет в списке, запросите его, как описано в [{#T}](ru/kyc2/intro.md). Доступные методы перечислены в разделе [Методы идентификации](methods)

Непосредственная проверка в сервисе выполняется на основе заявки на идентификацию. Чтобы ее создать, потребуется заполнить соответствующий набор документов. Для разных методов он может отличаться. Подробнее требуемый список смотрите на странице описания самого метода.

Пример вызова:

![ways](https://plantuml.w1.money/png/XL3DIiD06BplKtpqMaB1Om-zI17nOB5UFAx92ejj4fCD64He_RjKxKbyZL9QA6sJliAtR-Hi0c8HCOUmkz_CV3CZVHCbr9EvIaYc58jO9aRxEfQXeKRtrgWab0FgvwbHmuQ3g5kp1pgvE3nmxzUvBqJiTJ1lDjlyoJCxvJclUKPSkD-A5xpamZxmoZxXXebzmVbTo84X_h1Ztl3AWPV4Epi2UC6b7OCmWz06SXjoJxosZxojjUn443uizRnEt2RxXka8XC9E2Amvu0ktVSTBEyAAqfc1sAj3s6Sd271z4IeC26R4jsO7gP26UgVzSsh9MBTqe4AZhxGlZOv2Br4tcKfDshhClI3oio6cdWxIIY5H9aj2sgUSrOpZG8X15CKmMKNTsgaxJc2qH0-5YuTofdKL9OB0Fpf3GVEgyryUZxh_MBpxmyZzJxEfaIPBgoQSlIy0 "ways")

## Ввод данных пользователем на страницах KYC2

Этот способ является надстройкой над `методами идентификации` и преданзначен для случая, когда партнер или вызывающая система не хочет брать на себя ответственность за передачу персональных данных или просто не обладает такой возможностью. Тогда, с помощью метода [`POST /api/generate-identification-url`](openapi/GeneratePageUrl/apigenerate-identification-url-post.html) можно сгенерировать страницу, на которую перенаправить пользователя для ввода своих данных. 

Пример вызова:

![ways](https://plantuml.w1.money/png/PL71IiD05BplLpoybb3mc4CfI97YmMBru759rXAQ9kjc0mqYb29UAlReqLy8eS7GclONPlz8DgrYzRA7D_Dcvirs4ysLJYSXuvwE5Gru9XJnX2IJNEd02oIFD9tqfbgeY8Sqp18j9YUM8gRwY7HnUtPgv-FCOqmw7J7LhPQBD-HcYG9hv8JQFXLMA56REIhpXCeYvXcLUMMCYAokqnbdNglbqk1oU4rjBeFsI4H2SIsSm1UH3ku2Z-iWZfnKXSsM4ZfL4Ka-4ZSg_54Yn-cGT0dlACtCsYB7pioGey36B56QEM67BJRc1H_Oea1kvYWjkC_xBMaLPPFC9HGeiCKAbLrV8KUDsYnGCXR6iIGKZTcdrIZCOc-0kl6lxD16i5usr1eLeMZw-rV0UUzm_v-pbRXFHQAJzeCuV4t_Y72rn_l-u-zs4irrcZ0H-MHZigw8_7GIVW40 "ways")
