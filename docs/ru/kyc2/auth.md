# Авторизация

В сервисе используется схема с OAuth-авторизацией. В качестве провайдера авторизации выступает Keycloak с client_id и client_secret для получения api-key. Пример вызова:

```curl
curl --request POST \
  --url http://kyc-keycloak-url/auth/realms/smev3realm/protocol/openid-connect/token \
  --header 'content-type: application/x-www-form-urlencoded' \
  --data grant_type=client_credentials \
  --data client_id=client_id \
  --data client_secret=b2934d61-6348-441a-a8f9-aa2181b69941
```

Пример ответа:

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICJhd1gxVHZwWDVaMmtzdGZCX3BoTHVTOUxlUUwtcHpseFdRUWc5cF9iTTJVIn0.eyJleHAiOjE3NDM5Mjk5NDksImlhdCI6MTc0MzkyOTY0OSwianRpIjoiZjZiMWUyMjMtZDNjOS00NmRkLThmZTQtYjRhMWYzZWRkY2UzIiwiaXNzIjoiaHR0cDovLzEwLjIwMi4xOS4xMDA6NDY4Ni9hdXRoL3JlYWxtcy9zbWV2M3JlYWxtIiwiYXVkIjpbInNtZXYzLWZpb2lubiIsImFjY291bnQiXSwic3ViIjoiYWNjYmJjNGEtOWFmNy00MDYxLWJiZGItODllZmEwOGU3YTEwIiwidHlwIjoiQmVhcmVyIiwiYXpwIjoic3RvbG90b19kZW1vIiwiYWNyIjoiMSIsInJlYWxtX2FjY2VzcyI6eyJyb2xlcyI6WyJvZmZsaW5lX2FjY2VzcyIsInVtYV9hdXRob3JpemF0aW9uIl19LCJyZXNvdXJjZV9hY2Nlc3MiOnsic21ldjMtZmlvaW5uIjp7InJvbGVzIjpbIlJPTEVfVVNFUiIsIlVTRVIiXX0sImFjY291bnQiOnsicm9sZXMiOlsibWFuYWdlLWFjY291bnQiLCJtYW5hZ2UtYWNjb3VudC1saW5rcyIsInZpZXctcHJvZmlsZSJdfX0sInNjb3BlIjoiZW1haWwgcHJvZmlsZSIsImNsaWVudEhvc3QiOiIxMC4yMDIuMjAuMzQiLCJjbGllbnRJZCI6InN0b2xvdG9fZGVtbyIsImVtYWlsX3ZlcmlmaWVkIjpmYWxzZSwicHJlZmVycmVkX3VzZXJuYW1lIjoic2VydmljZS1hY2NvdW50LXN0b2xvdG9fZGVtbyIsImNsaWVudEFkZHJlc3MiOiIxMC4yMDIuMjAuMzQifQ.Ym-93sZKoYjnazrIasDq2YnjYZ522JdBcYhWRiUDzchPmTNsQr-wA8iDV8n9XNeOQE7O58C0_k5josqE2kbgWsxrwpmBvWlDeOPWfEeHSUjZSCf3zR3SvtayAKLVJhmmezwNftz4oxdFJSeIKWPcZy9BLrZsS0IC8GYKHfYbZAyjAGKAz4wAg0vljxGT-7Phsr7Q6ufKS2CK-XcwYuH3rzEIrYVwXmqrkFO1tIzgFPI5hE_ELnMW6zJGfOYNZtkEBnnwfpQ2R7Ijpu9uzYrKFtb383OHlVrNsUqeuPsDMmCnaB8vgGltIFTIAza5vst3EhzXRiDJg2W81k-HoLIflw",
  "expires_in": 300,
  "refresh_expires_in": 0,
  "token_type": "Bearer",
  "not-before-policy": 0,
  "scope": "email profile"
}
```

Полученный в поле `access_token` токен передается в заголовке `Authorization`. Пример вызова:

```curl
curl https://kyc-public-api/method \
  -H "Authorization: Bearer <OAuth-токен>"
```