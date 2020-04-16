---
title: REST API-foutcodes - Azure Key Vault
description: Deze foutcodes kunnen worden geretourneerd door een bewerking op een Azure Key Vault-webservice.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: bbb30c0ad41babca4158391c9e4e5c5d4d25cbf9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432058"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Foutcodes azure Key Vault REST API
 
De volgende foutcodes kunnen worden geretourneerd door een bewerking op een Azure Key Vault-webservice.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: Niet-geverifieerde aanvraag

401 betekent dat de aanvraag niet is geverifieerd voor Key Vault. 

Een aanvraag wordt geverifieerd als:

- De sleutelkluis kent de identiteit van de beller; En
- De beller mag proberen toegang te krijgen tot Key Vault-bronnen. 

Er zijn verschillende redenen waarom een verzoek 401 kan retourneren.

### <a name="no-authentication-token-attached-to-the-request"></a>Er is geen verificatietoken aan het verzoek gekoppeld. 

Hier is een voorbeeld PUT verzoek, het instellen van de waarde van een geheim:

``` 
PUT https://putreqexample.vault.azure.net//secrets/DatabaseRotatingPassword?api-version=7.0 HTTP/1.1
x-ms-client-request-id: 03d275a2-52a4-4bed-82c8-6fe15165affb
accept-language: en-US
Authorization: Bearer     eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9.eyJhdWQiOiJodHRwczovL3ZhdWx0LmF6dXJlLm5ldCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpYXQiOjE1NDg2OTc1MTMsIm5iZiI6MTU0ODY5NzUxMywiZXhwIjoxNTQ4NzAxNDEzLCJhaW8iOiI0MkpnWUhoODVqaVBnZHF5ZlRGZE5TdHY3bGUvQkFBPSIsImFwcGlkIjoiZmFkN2Q1YjMtNjlkNi00YjQ4LTkyNTktOGQxMjEyNGUxY2YxIiwiYXBwaWRhY3IiOiIxIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsIm9pZCI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInN1YiI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjItZ3JoUmtlSWs2QmVZLUxuNDJtQUEiLCJ2ZXIiOiIxLjAifQ.fgubiz1MKqTJTXI8dHIV7t9Fle6FdHrkaGYKcBeVRX1WtLVuk1QVxzIFDlZKLXJ7QPNs0KWpeiWQI9IWIRK-8wO38yCqKTfDlfHOiNWGOpkKddlG729KFqakVf2w0GPyGPFCONRDAR5wjQarN9Bt8I8YbHwZQz_M1hztlnv-Lmsk1jBmech9ujD9-lTMBmSfFFbHcqquev119V7sneI-zxBZLf8C0pIDkaXf1t8y6Xr8CUJDMdlWLslCf3pBCNIOy65_TyGvy4Z4AJryTPBarNBPwOkNAtjCfZ4BDc2KqUZM5QN_VK4foP64sVzUL6mSr0Gh7lQJIL5b1qIpJxjxyQ
User-Agent: FxVersion/4.7.3324.0 OSName/Windows OSVersion/6.2.9200.0 Microsoft.Azure.KeyVault.KeyVaultClient/3.0.3.0
Content-Type: application/json; charset=utf-8
Host: putreqexample.vault.azure.net
Content-Length: 31

{
   "value": "m*gBJ7$Zuoz)"
}
```

De koptekst 'Autorisatie' is het toegangstoken dat vereist is bij elk gesprek naar de Key Vault voor gegevensbewerkingen. Als de koptekst ontbreekt, moet het antwoord 401 zijn.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>Het token mist de juiste bron die eraan is gekoppeld. 

Bij het aanvragen van een toegangstoken van het Azure OAUTH-eindpunt is een parameter met de naam "resource" verplicht. De waarde is belangrijk voor de tokenprovider omdat het token geschikt is voor het beoogde gebruik. De bron voor **alle** tokens om toegang te krijgen tot een Key Vault is *https:\//vault.keyvault.net* (zonder trailing slash).

### <a name="the-token-is-expired"></a>Het token is verlopen

Tokens zijn base64 gecodeerd en de waarden kunnen [http://jwt.calebb.net](http://jwt.calebb.net)worden gedecodeerd op websites zoals . Hier is het bovenstaande token gedecodeerd:

```
    {
 typ: "JWT",
 alg: "RS256",
 x5t: "nbCwW11w3XkB-xUaXwKRSLjMHGQ",
 kid: "nbCwW11w3XkB-xUaXwKRSLjMHGQ"
}.
{
 aud: "https://vault.azure.net",
 iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 iat: 1548697513,
 nbf: 1548697513,
 exp: 1548701413,
 aio: "42JgYHh85jiPgdqyfTFdNStv7le/BAA=",
 appid: "fad7d5b3-69d6-4b48-9259-8d12124e1cf1",
 appidacr: "1",
 idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 oid: "3975aeed-7d08-453b-b6f4-445f32698091",
 sub: "3975aeed-7d08-453b-b6f4-445f32698091",
 tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",
 uti: "2-grhRkeIk6BeY-Ln42mAA",
 ver: "1.0"
}.
[signature]
```

We zien veel belangrijke onderdelen in dit token:

- aud (publiek): de bron van het token. Merk op <https://vault.azure.net>dat dit . Dit token werkt NIET voor resources die niet expliciet overeenkomen met deze waarde, zoals grafiek.
- iat (uitgegeven bij): Het aantal teken sinds het begin van het tijdperk toen het token werd uitgegeven.
- nbf (niet eerder): Het aantal teken sinds het begin van het tijdperk wanneer dit token geldig wordt.
- exp (vervaldatum): Het aantal teken sinds het begin van het tijdperk wanneer dit token verloopt.
- appid (applicatie-id): de GUID voor de toepassings-ID die dit verzoek indient.
- tid (tenant ID): De GUID voor de tenant-ID van de opdrachtgever die dit verzoek doet

Het is belangrijk dat alle waarden goed worden geïdentificeerd in het token om het verzoek te laten werken. Als alles klopt, dan zal het verzoek niet resulteren in 401.

### <a name="troubleshooting-401"></a>Probleemoplossing 401

401s moeten worden onderzocht vanaf het punt van tokengeneratie, voordat het verzoek wordt gedaan naar de sleutelkluis. Over het algemeen wordt code gebruikt om het token aan te vragen. Zodra het token is ontvangen, wordt het doorgegeven aan de Key Vault-aanvraag. Als de code lokaal wordt uitgevoerd, u Fiddler `https://login.microsoftonline.com`gebruiken om de aanvraag/het antwoord op. Een aanvraag ziet er als volgt uit:

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

De volgende door de gebruiker verstrekte informatie brij correct zijn:

- De sleutelkluistenant-id
- Resourcewaarde ingesteld op https%3A%2F%2Fvault.azure.net (URL gecodeerd)
- Client-id
- Clientgeheim

Zorg ervoor dat de rest van het verzoek vrijwel identiek is.

Als u alleen het antwoordtoegangstoken krijgen, u het (zoals hierboven weergegeven) decoderen om ervoor te zorgen dat de tenant-id, de client-id (app-id) en de bron worden bepaald.

## <a name="http-403-insufficient-permissions"></a>HTTP 403: Onvoldoende machtigingen

HTTP 403 betekent dat de aanvraag is geverifieerd (het de identiteit van het aanvragen kent), maar dat de identiteit geen toestemming heeft om toegang te krijgen tot de gevraagde bron. Er zijn twee oorzaken:

- Er is geen toegangsbeleid voor de identiteit.
- Het IP-adres van de aanvragende bron staat niet op de witte lijst in de firewall-instellingen van de sleutelkluis.

HTTP 403 treedt vaak op wanneer de toepassing van de klant de client-ID niet gebruikt die de klant denkt dat het is. Dat betekent meestal dat het toegangsbeleid niet correct is ingesteld voor de werkelijke belidentiteit.

### <a name="troubleshooting-403"></a>Probleemoplossing 403

Schakel eerst logboekregistratie in. Zie [Azure Key Vault-logboekregistratie](logging.md)voor instructies over hoe u dit moet doen).

Zodra logboekregistratie is ingeschakeld, u bepalen of de 403 te wijten is aan toegangsbeleid of firewallbeleid.

#### <a name="error-due-to-firewall-policy"></a>Fout als gevolg van firewallbeleid

"Klantadres (00.00.00.00) is niet geautoriseerd en de beller is geen vertrouwde service"

Er is een beperkte lijst met "Azure Trusted Services". Azure-websites zijn **geen** vertrouwde Azure-service. Zie voor meer informatie de toegang tot Key Vault Firewall van het blogbericht [Key Vault Firewall door Azure App Services](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services).

U moet het IP-adres van de Azure-website toevoegen aan de Key Vault om het te laten werken.

Als u vanwege het toegangsbeleid: zoek de object-id voor de aanvraag en zorg ervoor dat de object-id overeenkomt met het object waaraan de gebruiker het toegangsbeleid probeert toe te wijzen. Er zullen vaak meerdere objecten in de AAD die dezelfde naam hebben, dus het kiezen van de juiste is zeer belangrijk. Door het toegangsbeleid te verwijderen en opnieuw toe te voegen, is het mogelijk om te zien of er meerdere objecten met dezelfde naam bestaan.

Bovendien vereisen de meeste toegangsbeleidsregels niet het gebruik van de "Geautoriseerde toepassing" zoals weergegeven in de portal. Geautoriseerde toepassing wordt gebruikt voor "on-behalf-of" authenticatie scenario's, die zeldzaam zijn. 


## <a name="http-429-too-many-requests"></a>HTTP 429: Te veel aanvragen

Beperking treedt op wanneer het aantal aanvragen het opgegeven maximum voor de termijn overschrijdt. Als er een beperking optreedt, is het antwoord van de Key Vault HTTP 429. Er zijn vermelde maxima voor soorten verzoeken. Bijvoorbeeld: het maken van een HSM 2048-bit sleutel is 5 aanvragen per 10 seconden, maar alle andere HSM-transacties hebben een limiet van 1000 aanvragen/10 seconden. Daarom is het belangrijk om te begrijpen welke soorten oproepen worden gemaakt bij het bepalen van de oorzaak van throttling.
Over het algemeen zijn aanvragen voor de Key Vault beperkt tot 2000 aanvragen/10 seconden. Uitzonderingen zijn key operations, zoals gedocumenteerd in [Key Vault-servicelimieten](service-limits.md)

### <a name="troubleshooting-429"></a>Probleemoplossing 429
Throttling wordt gewerkt rond met behulp van deze technieken:

- Verminder het aantal aanvragen voor de Key Vault door te bepalen of er patronen zijn voor een aangevraagde bron en probeer ze in de aanroepende toepassing in de cache te bewaren. 

- Wanneer Key Vault-beperking optreedt, past u de aanvragende code aan om een exponentiële back-off te gebruiken om opnieuw te proberen. Het algoritme wordt hier uitgelegd: [Hoe u uw app beperken](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- Als het aantal aanvragen niet kan worden verminderd door caching en getimede back-off niet werkt, u overwegen de sleutels op te splitsen in meerdere key vaults. De servicelimiet voor één abonnement is 5x de afzonderlijke Key Vault-limiet. Als u meer dan 5 key vaults gebruikt, moet worden overwogen om meerdere abonnementen te gebruiken. 

Gedetailleerde richtlijnen, inclusief verzoek om limieten te verhogen, vindt u hier: [Key Vault throttling guidance](overview-throttling.md)


