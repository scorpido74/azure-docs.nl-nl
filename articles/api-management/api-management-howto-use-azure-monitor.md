---
title: In Azure API Management gepubliceerde API's bewaken | Microsoft Docs
description: Volg de stappen in deze zelfstudie voor informatie over het bewaken van uw API in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: b06301ab424a29d8f0e31e8f4dee26265327896b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238368"
---
# <a name="monitor-published-apis"></a>Gepubliceerde API's bewaken

Met Azure Monitor kunt u visualiseren, query's uitvoeren, routeren, archiveren en actie ondernemen op basis van de metrische gegevens en logboeken van resources in Azure.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Activiteitenlogboeken bekijken
> * Diagnostische logboeken weergeven
> * Metrische gegevens van uw API weergeven 
> * Een waarschuwingsregel instellen als uw API niet-gemachtigde aanroepen krijgt

In de volgende video kunt u zien hoe u API Management kunt bewaken met Azure Monitor. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]

## <a name="prerequisites"></a>Vereisten

+ Leer de [terminologie van Azure API Management](api-management-terminology.md).
+ Lees de volgende snelstartgids: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).
+ Voltooi tevens de volgende zelfstudie: [Uw eerste API importeren en publiceren](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Metrische gegevens van uw API's weergeven

API Management geeft elke minuut metrische gegevens vrij, waardoor u in vrijwel realtime inzicht hebt in de status van uw API's. Er volgt nu een overzicht van enkele beschikbare metrische gegevens:

* Capaciteit: helpt u bij het nemen van beslissingen over het upgraden/downgraden van uw APIM-services. Dit gegeven komt elke minuut beschikbaar en is een weerspiegeling van de capaciteit van de gateway ten tijde van de export. Het gegevensbereik loopt van 0 tot 100 en wordt berekend op basis van gateway-resources als CPU- en geheugengebruik.
* Totaal aantal gateway aanvragen: het aantal API-aanvragen per periode. 
* Geslaagde gateway-aanvragen: het aantal API-aanvragen die HTTP-responscodes met goed gevolg hebben ontvangen, waaronder 304, 307 en codes kleiner dan 301 (bijvoorbeeld 200).
* Mislukte gateway-aanvragen: het aantal API-aanvragen die HTTP-responscodes bij aanvraagfouten hebben ontvangen, waaronder 400 en codes groter dan 500.
* Niet-gemachtigde gateway-aanvragen: het aantal API-aanvragen die HTTP-responscodes hebben ontvangen, waaronder de nummers 401,403 en 429.
* Overige gateway-aanvragen: het aantal API-aanvragen die HTTP-responscodes hebben ontvangen die niet in een van de bovenstaande categorieën vallen (bijvoorbeeld 418).

![grafiek met metrische gegevens](./media/api-management-azure-monitor/apim-monitor-metrics.png)

Metrische gegevens openen:

1. Selecteer **Metrische gegevens** in het menu onder aan de pagina.

    ![metrics](./media/api-management-azure-monitor/api-management-metrics-blade.png)

1. Selecteer in de vervolgkeuzelijst de gewenste metrische gegevens. Bijvoorbeeld **aanvragen**. 
1. De grafiek toont het totale aantal API-aanroepen.
1. De grafiek kan worden gefilterd met behulp van de afmetingen van de statistiek **Aanvragen.** Klik bijvoorbeeld op **Filter toevoegen**, kies **Backend-antwoordcode**, voer 500 in als waarde. Nu toont de grafiek het aantal aanvragen dat is mislukt in de API-backend.   

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Een waarschuwingsregel instellen voor een niet-gemachtigde aanvraag

U kunt instellen dat u waarschuwingen ontvangt op basis van metrische gegevens en activiteitenlogboeken. Met Azure Monitor kunt u een waarschuwing zodanig configureren dat deze bij activering het volgende doet:

* Een e-mailmelding verzenden
* Een webhook aanroepen
* Een logische Azure-app aanroepen

Waarschuwingen configureren:

1. Selecteer **Waarschuwingen** op de menubalk onder aan de pagina.

    ![waarschuwingen](./media/api-management-azure-monitor/alert-menu-item.png)

2. Klik op een **nieuwe waarschuwingsregel** voor deze waarschuwing.
3. Klik op **Voorwaarde toevoegen**.
4. Selecteer **Statistieken** in de vervolgkeuzelijst Signaaltype.
5. Selecteer **Ongeautoriseerde gatewayaanvraag** als het signaal dat u wilt controleren.

    ![waarschuwingen](./media/api-management-azure-monitor/signal-type.png)

6. Geef in de weergave **Signaallogica configureren** een drempelwaarde op waarna de waarschuwing moet worden geactiveerd en klik op **Gereed**.

    ![waarschuwingen](./media/api-management-azure-monitor/threshold.png)

7. Selecteer een bestaande actiegroep of maak een nieuwe groep. In het onderstaande voorbeeld wordt een e-mail naar de beheerders verzonden. 

    ![waarschuwingen](./media/api-management-azure-monitor/action-details.png)

8. Geef een naam, beschrijving van de waarschuwingsregel op en kies het ernstniveau. 
9. Druk op **Waarschuwingsregel maken**.
10. Probeer nu de Conference API aan te roepen zonder API-sleutel. De waarschuwing wordt geactiveerd en e-mail wordt verzonden naar de beheerders. 

## <a name="activity-logs"></a>Activiteitenlogboeken

Activiteitenlogboeken bieden inzicht in de bewerkingen die zijn uitgevoerd voor uw API Management-services. Met activiteitenlogboeken kunt u het 'wat, wie en wanneer' bepalen voor schrijfbewerkingen (PUT, POST, DELETE) die voor uw API Management-services worden uitgevoerd.

> [!NOTE]
> Activiteitenlogboeken bevatten geen lees-bewerkingen (GET) of bewerkingen die zijn uitgevoerd in de Azure-portal of met behulp van de oorspronkelijke beheer-API's.

U kunt activiteitenlogboeken in uw API Management-service openen. U kunt alle logboeken van al uw Azure-resources in Azure Monitor openen. 

![activiteitenlogboeken](./media/api-management-azure-monitor/apim-monitor-activity-logs.png)

Activiteitenlogboeken weergeven:

1. Selecteer uw exemplaar van de APIM-service.
2. Klik op **Activiteitenlogboek**.

    ![activiteitenlogboek](./media/api-management-azure-monitor/api-management-activity-logs-blade.png)

3. Selecteer het gewenste filterbereik en klik op **Toepassen**.

## <a name="diagnostic-logs"></a>Diagnostische logboeken

Diagnoselogboeken bieden uitgebreide informatie over bewerkingen en fouten die belangrijk zijn voor zowel controles als het oplossen van problemen. Diagnoselogboeken verschillen van activiteitenlogboeken. Activiteitenlogboeken bieden inzicht in de bewerkingen die zijn uitgevoerd op uw Azure-resources. Diagnoselogboeken bieden inzicht in bewerkingen die door de resources zelf zijn uitgevoerd.

Diagnostische logboeken configureren:

1. Selecteer uw exemplaar van de APIM-service.
2. Klik op **Diagnostische instellingen**.

    ![diagnostische logboeken](./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png)

3. Klik op **Diagnostische gegevens inschakelen**. U kunt diagnostische logboeken samen met metrische gegevens naar een opslagaccount archiveren, ze naar een Event Hub streamen of ze naar Azure Monitor-logboeken verzenden. 

API Management biedt momenteel diagnostische logboeken (ingedeeld in batches per uur) over afzonderlijke API-aanvragen waarbij elk item het volgende schema heeft:

```json
{  
    "isRequestSuccess" : "",
    "time": "",
    "operationName": "",
    "category": "",
    "durationMs": ,
    "callerIpAddress": "",
    "correlationId": "",
    "location": "",
    "httpStatusCodeCategory": "",
    "resourceId": "",
    "properties": {   
        "method": "", 
        "url": "", 
        "clientProtocol": "", 
        "responseCode": , 
        "backendMethod": "", 
        "backendUrl": "", 
        "backendResponseCode": ,
        "backendProtocol": "",  
        "requestSize": , 
        "responseSize": , 
        "cache": "", 
        "cacheTime": "", 
        "backendTime": , 
        "clientTime": , 
        "apiId": "",
        "operationId": "", 
        "productId": "", 
        "userId": "", 
        "apimSubscriptionId": "", 
        "backendId": "",
        "lastError": { 
            "elapsed" : "", 
            "source" : "", 
            "scope" : "", 
            "section" : "" ,
            "reason" : "", 
            "message" : ""
        } 
    }      
}  
```

| Eigenschap  | Type | Beschrijving |
| ------------- | ------------- | ------------- |
| isRequestSuccess | booleaans | Waar als de HTTP-aanvraag is voltooid met een antwoordstatuscode binnen 2xx tot 3xx |
| tijd | date-time | Tijdstempel van wanneer de gateway de aanvraag start |
| operationName | tekenreeks | Constante waarde 'Microsoft.ApiManagement/GatewayLogs' |
| category | tekenreeks | Constante waarde 'GatewayLogs' |
| durationMs | geheel getal | Aantal milliseconden vanaf het moment dat gateway aanvraag heeft ontvangen tot het moment waarop het antwoord volledig is verzonden. Het bevat clienTime, cacheTime en backendTime. |
| callerIpAddress | tekenreeks | IP-adres van de onmiddellijke gatewayaanroeper (kan een tussenschakel zijn) |
| correlationId | tekenreeks | Unieke HTTP-aanvraag-ID die is toegewezen door API Management |
| location | tekenreeks | Naam van de Azure-regio waar de gateway die de aanvraag heeft verwerkt zicht bevond |
| httpStatusCodeCategory | tekenreeks | Categorie van de HTTP-antwoordstatuscode: geslaagd (301 of minder of 304 of 307), niet geautoriseerd (401, 403, 429), fout (400, tussen 500 en 600), anders |
| resourceId | tekenreeks | ID van de API Management\<resource /ABONNEMENTEN/\<abonnement>/RESOURCEGROUPS/ resourcegroep>/PROVIDERS/MICROSOFT. APIMANAGEMENT/SERVICE/naam\<> |
| properties | object | Eigenschappen van de huidige aanvraag |
| method | tekenreeks | HTTP-methode van de inkomende aanvraag |
| url | tekenreeks | URL van de binnenkomende aanvraag |
| clientProtocol | tekenreeks | HTTP-protocolversie van de inkomende aanvraag |
| responseCode | geheel getal | Statuscode van het HTTP-antwoord dat is verzonden naar een client |
| backendMethod | tekenreeks | HTTP-methode van de aanvraag die is verzonden naar een back-end |
| backendUrl | tekenreeks | URL van de aanvraag die is verzonden naar een back-end |
| backendResponseCode | geheel getal | Code van het HTTP-antwoord dat is ontvangen van een back-end |
| backendProtocol | tekenreeks | HTTP-protocolversie van de aanvraag die is verzonden naar een back-end | 
| requestSize | geheel getal | Aantal bytes dat van een client is ontvangen tijdens de verwerking van aanvragen | 
| responseSize | geheel getal | Aantal bytes dat naar een client is verzonden tijdens de verwerking van aanvragen | 
| cache | tekenreeks | Status van de betrokkenheid van de API Management-cache bij het verwerken van aanvragen (dat wil zeggen, treffer, misser, geen) | 
| cacheTime | geheel getal | Aantal milliseconden dat in totaal is besteed aan IO van de API Management-cache (verbinding maken, bytes verzenden en ontvangen) | 
| backendTime | geheel getal | Aantal milliseconden dat in totaal is besteed aan IO van de back-end (verbinding maken, bytes verzenden en ontvangen) | 
| clientTime | geheel getal | Aantal milliseconden dat in totaal is besteed aan IO van de client (verbinding maken, bytes verzenden en ontvangen) | 
| apiId | tekenreeks | ID van de API-entiteit voor de huidige aanvraag | 
| operationId | tekenreeks | ID van de bewerkingsentiteit voor de huidige aanvraag | 
| productId | tekenreeks | ID van de productentiteit voor de huidige aanvraag | 
| userId | tekenreeks | ID van de gebruikersentiteit voor de huidige aanvraag | 
| apimSubscriptionId | tekenreeks | ID van de abonnemententiteit voor de huidige aanvraag | 
| backendId | tekenreeks | ID van de back-end-entiteit voor de huidige aanvraag | 
| LastError | object | Laatste fout bij de aanvraagverwerking | 
| elapsed | geheel getal | Aantal milliseconden verstreken tussen het moment waarop de gateway de aanvraag heeft ontvangen en het moment waarop de fout is opgetreden | 
| source | tekenreeks | Naam van de interne handler voor beleid of verwerking die de fout heeft veroorzaakt | 
| scope | tekenreeks | Bereik van het beleidsdocument met het beleid dat de fout heeft veroorzaakt | 
| section | tekenreeks | Gedeelte van het beleidsdocument met het beleid dat de fout heeft veroorzaakt | 
| reason | tekenreeks | Reden voor de fout | 
| message | tekenreeks | Foutbericht | 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Activiteitenlogboeken bekijken
> * Diagnostische logboeken weergeven
> * Metrische gegevens van uw API weergeven
> * Een waarschuwingsregel instellen als uw API niet-gemachtigde aanroepen krijgt

Ga door naar de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Oproepen traceren](api-management-howto-api-inspector.md)
