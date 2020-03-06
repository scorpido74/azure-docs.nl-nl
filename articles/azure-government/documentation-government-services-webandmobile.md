---
title: Azure Government Web, mobiel en API | Microsoft Docs
description: Dit biedt een vergelijking van de functies en richt lijnen voor het ontwikkelen van toepassingen voor Azure Government
services: azure-government
cloud: gov
documentationcenter: ''
author: gsacavdm
manager: pathuff
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/22/2019
ms.author: gsacavdm
ms.openlocfilehash: 15c6936b7a8c319c4ddef86eddc47546966782b4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396340"
---
# <a name="azure-government-web--mobile"></a>Azure Government Web en mobiel

In dit artikel vindt u een overzicht van de web-en Mobile-Services voor de Azure Government omgeving, met een beschrijving van de functie variaties die verschillen van de open bare versie en eventuele specifieke overwegingen voor de omgeving.

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

[Azure Cognitive Search](https://docs.microsoft.com/azure/search/) is algemeen verkrijgbaar in azure Government. Ga voor een zelfgestuurde exploratie van de zoek functionaliteit met behulp van gegevens van de open bare overheid naar de website [Content Search en intelligence](https://documentsearch.azurewebsites.net/home/) , selecteer de GEGEVENSSET ' Amerikaanse rechtbank of bezwaars district 1 ' en kies een van de demo opties.

Zoek functies die veel worden toegepast in zoek toepassingen op basis van de overheid bevatten [cognitieve vaardig heden](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro), die nuttig zijn voor het uitpakken van structuur en informatie uit grote, ongedifferentieerde tekst documenten.

Eenvoudige query syntaxis, query's formuleren om te zoeken naar grote hoeveel heden inhoud, is ook relevant voor toepassings ontwikkelaars. Azure Cognitive Search ondersteunt twee syntaxis: [eenvoudig](https://docs.microsoft.com/azure/search/query-simple-syntax) en [volledig](https://docs.microsoft.com/azure/search/query-lucene-syntax). U kunt voor [beelden van query-expressies](https://docs.microsoft.com/azure/search/search-query-simple-examples) bekijken voor een afdruk stand.

### <a name="variations"></a>Verschillen
Het eind punt voor zoek services die in Azure Government zijn gemaakt, is als volgt:

| Servicetype | Open bare Azure | Azure Government |
| ------------ | ------------ | ---------------- |
| Azure Cognitive Search-service |\*. search.windows.net |\*. search.windows.us|

Alle algemeen beschik bare en preview-functies in de open bare Cloud zijn ook beschikbaar in Azure Government.

### <a name="considerations"></a>Overwegingen

De volgende informatie bevat de Azure Government grens voor Azure Cognitive Search:

| Gereguleerde/beheerde gegevens toegestaan | Gereguleerde/beheerde gegevens niet toegestaan |
| ----------------------------------- | --------------------------------------- |
| Alle opgeslagen en verwerkte gegevens in azure Cognitive Search kunnen Azure Government-gereglementeerde gegevens bevatten. | De meta gegevens van Azure Cognitive Search mogen geen gegevensgestuurde gegevens bevatten. Deze meta gegevens bevatten alle configuratie gegevens die zijn ingevoerd bij het maken en onderhouden van uw service. Voer geen gereguleerde/beheerde gegevens in de volgende velden in: abonnements naam, resource groepen, service naam, resource namen, resource Tags of een object dat u een naam geeft of beschrijft in Cognitive Search (indexen, Indexeer functies, gegevens bronnen, synoniemen kaarten en vaardig heden). Neem geen gevoelige gegevens op in HTTP-headers die worden verzonden naar de REST API PR in zoek-en query reeksen die als onderdeel van de API zijn verzonden.|

## <a name="app-services"></a>App Services
### <a name="variations"></a>Verschillen
Azure-app Services is algemeen beschikbaar in Azure Government.

Het adres voor Azure App Service-apps die zijn gemaakt in Azure Government wijkt af van de apps die zijn gemaakt in de open bare Cloud:

| Servicetype | Open bare Azure | Azure Government |
| --- | --- | --- |
| App Service |\*. azurewebsites.net |\*. azurewebsites.us|
| Service-Principal-ID| abfa0a7c-a6b6-4736-8310-5855508787cd | 6a02c803-DaFD-4136-b4c3-5a6f318b4714 |

Sommige App Service-functies die beschikbaar zijn in de open bare Cloud zijn nog niet beschikbaar in Azure Government:

- Problemen vaststellen en oplossen
- Implementatie
    - Implementatie opties: alleen lokale Git-opslag plaats en externe opslag plaats zijn beschikbaar.
    - Implementatie centrum
- Instellingen
    - Application Insights
- Ontwikkel Hulpprogramma's
    - Prestatietest
    - Resource Verkenner
    - PHP-fout opsporing
- Ondersteuning voor & probleem oplossing
    - App Service Advisor
- App Service Certificates


### <a name="considerations"></a>Overwegingen
De volgende informatie bevat de Azure Government grens voor App Service:

| Gereguleerde/beheerde gegevens toegestaan | Gereguleerde/beheerde gegevens niet toegestaan |
| --- | --- |
| Gegevens die in Azure App Service worden ingevoerd, opgeslagen en verwerkt, kunnen gegevensgestuurde gegevens bevatten. Binaire bestanden die binnen Azure App Service worden uitgevoerd. Statische verificatoren, zoals wacht woorden en smartcard pincodes voor toegang tot onderdelen van het Azure-platform. Persoonlijke sleutels van certificaten die worden gebruikt voor het beheren van onderdelen van het Azure-platform. SQL-verbindings reeksen. Andere beveiligings informatie/geheimen, zoals certificaten, versleutelings sleutels, hoofd sleutels en opslag sleutels die zijn opgeslagen in Azure-Services. |Meta gegevens mogen geen door het export beheer beheerde informatie bevatten. Deze meta gegevens bevatten alle configuratie gegevens die zijn ingevoerd bij het maken en onderhouden van uw Azure App Service. Voer geen gereguleerde/beheerde gegevens in de volgende velden in: resource groepen, resource namen, resource Tags|

## <a name="api-management"></a>API-beheer
Zie [Azure API Management-documentatie](../api-management/index.yml)voor meer informatie over deze service en hoe u deze kunt gebruiken.

### <a name="variations"></a>Verschillen

Azure API Management service is over het algemeen beschikbaar in Azure Government. Functies die momenteel niet beschikbaar zijn in API Management service voor Azure Government zijn:

- Integratie van Azure AD B2C 
    - Integratie met Azure AD B2C is niet beschikbaar in Azure Government 

De Url's voor toegang tot Azure-API Management in Azure Government verschillen:

| Servicetype | Open bare Azure | Azure Government |
| --- | --- | --- |
|API Management Gateway| \*. azure-api.net| \*. azure-api.us|
|API Management Portal | \*. portal.azure-api.net |\*. portal.azure-api.us| 
|API Management beheer| \*. management.azure-api.net |\*. management.azure-api.us|

### <a name="considerations"></a>Overwegingen
De volgende informatie bevat de Azure Government grens voor de Azure API Management-service:

| Gereguleerde/beheerde gegevens toegestaan | Gereguleerde/beheerde gegevens niet toegestaan |
| --- | --- |
|Alle gegevens die zijn opgeslagen en verwerkt in de Azure API Management-service, kunnen Azure Government-gereglementeerde gegevens bevatten.|De meta gegevens van de Azure API Management-service mogen geen gegevensgestuurde gegevens bevatten. Voer geen gereguleerde/beheerde gegevens in de volgende velden in: API Management service naam, naam van abonnement, resource groepen, resource Tags.|

## <a name="next-steps"></a>Volgende stappen
Voor aanvullende informatie en updates kunt u zich abonneren op het [Microsoft Azure Government blog.](https://blogs.msdn.microsoft.com/azuregov/)

