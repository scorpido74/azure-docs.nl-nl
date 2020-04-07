---
title: De REST API voor sessiebeheer
description: Beschrijft hoe u sessies beheert
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 46560f067e020236031487677ad4f48a9560d4e1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681244"
---
# <a name="use-the-session-management-rest-api"></a>De REST-API voor sessiebeheer gebruiken

Als u azure remote rendering-functionaliteit wilt gebruiken, moet u een *sessie*maken. Elke sessie komt overeen met een virtuele machine (VM) die wordt toegewezen in Azure en wacht op een clientapparaat om verbinding te maken. Wanneer een apparaat verbinding maakt, maakt de VM de gevraagde gegevens weer en dient het resultaat als een videostream. Tijdens het maken van sessies kiest u op welk type server u wilt draaien, wat de prijzen bepaalt. Zodra de sessie niet meer nodig is, moet deze worden gestopt. Als deze niet handmatig wordt gestopt, wordt deze automatisch afgesloten wanneer de *leasetijd* van de sessie afloopt.

We bieden een PowerShell-script in de [ARR-samplesrepository](https://github.com/Azure/azure-remote-rendering) in de map *Scripts,* *genaamd RenderingSession.ps1,* die het gebruik van onze service aantoont. Het script en de configuratie worden hier beschreven: [Voorbeeld PowerShell-scripts](../samples/powershell-example-scripts.md)

> [!TIP]
> De PowerShell-opdrachten op deze pagina zijn bedoeld als aanvulling op elkaar. Als u alle scripts in volgorde uitvoert binnen dezelfde PowerShell-opdrachtprompt, worden ze op elkaar gebouwd.

## <a name="regions"></a>Regio's

Zie de [lijst met beschikbare regio's](../reference/regions.md) voor de basis-URL's waarnaar de aanvragen moeten worden verzonden.

Voor de onderstaande voorbeeldscripts hebben we gekozen voor de regio *westus2.*

### <a name="example-script-choose-an-endpoint"></a>Voorbeeldscript: een eindpunt kiezen

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>Accounts

Als u geen Extern rendering-account hebt, [maakt u er een](create-an-account.md). Elke resource wordt geïdentificeerd door een *accountId*, die wordt gebruikt tijdens de sessie API's.

### <a name="example-script-set-accountid-and-accountkey"></a>Voorbeeldscript: AccountId en accountKey instellen

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
```

## <a name="common-request-headers"></a>Kopteksten met algemene aanvraag

* De *autorisatiekop* moet de`Bearer TOKEN`waarde hebben`TOKEN`van " ", waar " het verificatietoken is dat wordt [geretourneerd door de Secure Token Service](tokens.md).

### <a name="example-script-request-a-token"></a>Voorbeeldscript: Een token aanvragen

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>Algemene antwoordkoppen

* De *MS-CV* header kan door het productteam worden gebruikt om het gesprek binnen de service te traceren.

## <a name="create-a-session"></a>Een sessie maken

Met deze opdracht wordt een sessie gemaakt. Hiermee wordt de id van de nieuwe sessie geretourneerd. Je hebt de sessie-ID nodig voor alle andere commando's.

| URI | Methode |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessies/maken | POST |

**Aanvraaginstantie:**

* maxLeaseTime (timespan): een time-outwaarde wanneer de VM automatisch wordt ontmanteld
* modellen (array): URL's voor assetcontainer om vooraf te laden
* grootte (tekenreeks): de VM-grootte (**"standaard"** of **"premium").** Zie specifieke [beperkingen voor vm-grootte](../reference/limits.md#overall-number-of-polygons).

**Reacties:**

| Statuscode | JSON-payload | Opmerkingen |
|-----------|:-----------|:-----------|
| 202 | - sessionId: GUID | Geslaagd |

### <a name="example-script-create-a-session"></a>Voorbeeldscript: Een sessie maken

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/create" -Method Post -ContentType "application/json" -Body "{ 'maxLeaseTime': '4:0:0', 'models': [], 'size': 'standard' }" -Headers @{ Authorization = "Bearer $token" }
```

Voorbeelduitvoer:

```PowerShell
StatusCode        : 202
StatusDescription : Accepted
Content           : {"sessionId":"d31bddca-dab7-498e-9bc9-7594bc12862f"}
RawContent        : HTTP/1.1 202 Accepted
                    MS-CV: 5EqPJ1VdTUakDJZc6/ZhTg.0
                    Content-Length: 52
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:17:50 GMT
                    Location: accounts/11111111-1111-1111-11...
Forms             : {}
Headers           : {[MS-CV, 5EqPJ1VdTUakDJZc6/ZhTg.0], [Content-Length, 52], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:17:50 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 52
```

### <a name="example-script-store-sessionid"></a>Voorbeeldscript: WinkelsessieId

Het antwoord van de bovenstaande aanvraag bevat een **sessionId**, die u nodig hebt voor alle follow-up verzoeken.

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="update-a-session"></a>Een sessie bijwerken

Met deze opdracht worden de parameters van een sessie bijgewerkt. Momenteel u de leasetijd van een sessie alleen verlengen.

> [!IMPORTANT]
> De leasetijd wordt altijd gegeven als een totale tijd sinds het begin van de sessie. Dat betekent dat als u een sessie hebt gemaakt met een leasetijd van een uur en u de leasetijd met een uur wilt verlengen, u de maxLeaseTime moet bijwerken tot twee uur.

| URI | Methode |
|-----------|:-----------|
| /v1/accounts/*accountID*/sessies/*sessionId* | Patch |

**Aanvraaginstantie:**

* maxLeaseTime (timespan): een time-outwaarde wanneer de VM automatisch wordt ontmanteld

**Reacties:**

| Statuscode | JSON-payload | Opmerkingen |
|-----------|:-----------|:-----------|
| 200 | | Geslaagd |

### <a name="example-script-update-a-session"></a>Voorbeeldscript: een sessie bijwerken

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Patch -ContentType "application/json" -Body "{ 'maxLeaseTime': '5:0:0' }" -Headers @{ Authorization = "Bearer $token" }
```

Voorbeelduitvoer:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: Fe+yXCJumky82wuoedzDTA.0
                    Content-Length: 0
                    Date: Thu, 09 May 2019 16:27:31 GMT


Headers           : {[MS-CV, Fe+yXCJumky82wuoedzDTA.0], [Content-Length, 0], [Date, Thu, 09 May 2019 16:27:31 GMT]}
RawContentLength  : 0
```

## <a name="get-active-sessions"></a>Actieve sessies krijgen

Met deze opdracht wordt een lijst met actieve sessies geretourneerd.

| URI | Methode |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessies | GET |

**Reacties:**

| Statuscode | JSON-payload | Opmerkingen |
|-----------|:-----------|:-----------|
| 200 | - sessies: scala aan sessie-eigenschappen | zie sectie 'Sessie-eigenschappen downloaden' voor een beschrijving van sessie-eigenschappen |

### <a name="example-script-query-active-sessions"></a>Voorbeeldscript: actieve sessies opvragen

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Voorbeelduitvoer:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : []
RawContent        : HTTP/1.1 200 OK
                    MS-CV: WfB9Cs5YeE6S28qYkp7Bhw.1
                    Content-Length: 15
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 25 Jul 2019 16:23:50 GMT

                    {"sessions":[]}
Forms             : {}
Headers           : {[MS-CV, WfB9Cs5YeE6S28qYkp7Bhw.1], [Content-Length, 2], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 25 Jul 2019 16:23:50 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 2
```

## <a name="get-sessions-properties"></a>Eigenschappen voor sessies ophalen

Met deze opdracht retourneert u informatie over een sessie, zoals de vm-hostnaam.

| URI | Methode |
|-----------|:-----------|
| /v1/accounts/accountId /sessies/*sessionId*/properties*accountId* | GET |

**Reacties:**

| Statuscode | JSON-payload | Opmerkingen |
|-----------|:-----------|:-----------|
| 200 | - bericht: tekenreeks<br/>- sessionElapsedTime: tijdspanne<br/>- sessionHostname: tekenreeks<br/>- sessionId: tekenreeks<br/>- sessionMaxLeaseTime: tijdspanne<br/>- sessionSize: enum<br/>- sessionStatus: enum | enum sessionStatus { starten, klaar, stoppen, gestopt, verlopen, fout}<br/>Als de status 'fout' of 'verlopen' is, bevat het bericht meer informatie |

### <a name="example-script-get-session-properties"></a>Voorbeeldscript: Sessie-eigenschappen ophalen

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId/properties" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Voorbeelduitvoer:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {"message":null,"sessionElapsedTime":"00:00:01","sessionHostname":"5018fee8-817e-4366-9179-556af79a4240.remoterenderingvm.westeurope.mixedreality.azure.com","sessionId":"e13d2c44-63e0-4591-991e-f9e05e599a93","sessionMaxLeaseTime":"04:00:00","sessionStatus":"Ready"}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: CMXegpZRMECH4pbOW2j5GA.0
                    Content-Length: 60
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:30:38 GMT

                    {"message":null,...
Forms             : {}
Headers           : {[MS-CV, CMXegpZRMECH4pbOW2j5GA.0], [Content-Length, 60], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:30:38 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 60
```

## <a name="stop-a-session"></a>Een sessie stoppen

Met deze opdracht wordt een sessie gestopt. De toegewezen VM wordt kort daarna teruggewonnen.

| URI | Methode |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessies/*sessionId* | DELETE |

**Reacties:**

| Statuscode | JSON-payload | Opmerkingen |
|-----------|:-----------|:-----------|
| 204 | | Geslaagd |

### <a name="example-script-stop-a-session"></a>Voorbeeldscript: Een sessie stoppen

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Delete -Headers @{ Authorization = "Bearer $token" }
```

Voorbeelduitvoer:

```PowerShell
StatusCode        : 204
StatusDescription : No Content
Content           : {}
RawContent        : HTTP/1.1 204 No Content
                    MS-CV: YDxR5/7+K0KstH54WG443w.0
                    Date: Thu, 09 May 2019 16:45:41 GMT


Headers           : {[MS-CV, YDxR5/7+K0KstH54WG443w.0], [Date, Thu, 09 May 2019 16:45:41 GMT]}
RawContentLength  : 0
```

## <a name="next-steps"></a>Volgende stappen

* [Voorbeeld PowerShell-scripts](../samples/powershell-example-scripts.md)
