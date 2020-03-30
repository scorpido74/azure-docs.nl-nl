---
title: Gegevensquery's in Preview - Azure Time Series Insights | Microsoft Documenten
description: Concepten voor gegevensquery's en HET HTTP REST API-overzicht in Azure Time Series Insights Preview.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/25/2020
ms.custom: seodec18
ms.openlocfilehash: 23094ec71dac5780def10e16b90de0b818ef3c68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284888"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Gegevensquery's in de preview-voor-dag van Azure Time Series Insights

Azure Time Series Insights maakt het mogelijk om gegevens op te vragen over gebeurtenissen en metagegevens die in de omgeving zijn opgeslagen via API's voor openbare oppervlakken. Deze API's worden ook gebruikt door de [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).

Er zijn drie primaire API-categorieën beschikbaar in Time Series Insights:

* **OmgevingsAPI's**: Deze API's maken query's mogelijk op de Time Series Insights-omgeving zelf. Deze kunnen worden gebruikt om de lijst met omgevingen te verzamelen waarde beller toegang heeft tot en omgevingmetagegevens.
* **Time Series Model-Query (TSM-Q) API's:** Hiermee kunnen crud-bewerkingen (2000, lezen, bijwerken en verwijderen) worden ingeschakeld op metagegevens die zijn opgeslagen in het Tijdreeksmodel van de omgeving. Deze kunnen worden gebruikt om de instanties, typen en hiërarchieën te openen en te bewerken.
* **Time Series Query (TSQ) API's:** Maakt het ophalen van telemetrie- of gebeurtenissengegevens mogelijk zoals deze zijn opgenomen bij de bronprovider en maakt performante berekeningen en aggregaties op de gegevens mogelijk met behulp van geavanceerde scalaire en geaggregeerde functies.

Time Series Insights gebruikt een uitgebreide op tekenreeksen gebaseerde expressietaal, [Time Series Expression (TSX),](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)voor het uitdrukken van berekeningen.

## <a name="azure-time-series-insights-core-apis"></a>Azure Time Series Insights kern-API's

De volgende basis-API's worden ondersteund.

[![Overzicht van tijdreeksquery's](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Milieu-API's

* [API voor get Environments:](https://docs.microsoft.com/rest/api/time-series-insights/management/environments/get)geeft als resultaat de lijst met omgevingen die de beller heeft geautoriseerd om toegang te krijgen.
* [BeschikbaarheidsAPI voor omgevingen opdoen:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability)geeft als `$ts`resultaat de verdeling van het aantal gebeurtenissen over de gebeurtenistijdstempel . Deze API helpt bepalen of er gebeurtenissen in de omgeving zijn door het aantal gebeurtenissen dat is opgesplitst in tijdsintervallen terug te geven, indien aanwezig.
* [Gebeurtenisschema-API opvragen:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)geeft als resultaat de metagegevens van het gebeurtenisschema voor een bepaalde zoekperiode. Deze API helpt bij het ophalen van alle metagegevens en eigenschappen die beschikbaar zijn in het schema voor de opgegeven zoekperiode.

## <a name="time-series-model-query-tsm-q-apis"></a>Tijdreeksmodelquery (TSM-Q) API's

De meeste van deze API's ondersteunen batchuitvoeringsbewerking om batch CRUD-bewerkingen in te schakelen op meerdere Time Series-modelentiteiten:

* [Modelinstellingen-API:](https://docs.microsoft.com/rest/api/time-series-insights/preview#model-settings-api)Hiermee schakelt *u GET* en *PATCH* in op het standaardtype en de modelnaam van de omgeving.
* [Typen API:](https://docs.microsoft.com/rest/api/time-series-insights/preview#types-api)Hiermee maakt CRUD op tijdreekstypen en de bijbehorende variabelen.
* [Api voor hiërarchieën:](https://docs.microsoft.com/rest/api/time-series-insights/preview#hierarchies-api)hiermee kunnen CRUD-hiërarchieën in de tijdreeksen en de bijbehorende veldpaden worden ingeschakeld.
* [Instances API](https://docs.microsoft.com/rest/api/time-series-insights/preview#instances-api): Hiermee schakelt CRUD op Time Series-exemplaren en de bijbehorende instantievelden in. Daarnaast ondersteunt de Instances API de volgende bewerkingen:
  * [Zoeken:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search)hiermee wordt een gedeeltelijke lijst met treffers opgehaald voor het zoeken naar tijdreeksinstanties op basis van instantiekenmerken.
  * [Stel:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest)Zoekt uit en stelt een gedeeltelijke lijst met hits voor in zoekopdrachten naar tijdreeksexemplaren op basis van instantiekenmerken.

## <a name="time-series-query-tsq-apis"></a>Time Series Query (TSQ) API's

Deze API's zijn beschikbaar in alle beide winkels in onze meerlaagse opslagoplossing in Time Series Insights. Url-parameters voor query's worden gebruikt om het [winkeltype](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) op te geven waarop de query moet worden uitgevoerd:

* [Get Events API:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents)Hiermee u onbewerkte gebeurtenissen en de bijbehorende gebeurtenistijdstempels opvragen en ophalen terwijl ze zijn opgenomen in Time Series Insights van de bronprovider. Deze API maakt het ophalen van ruwe gebeurtenissen voor een bepaalde Time Series ID en zoekpanne mogelijk. Deze API ondersteunt paginatie om de volledige antwoordgegevensset voor de geselecteerde invoer op te halen. 

* [Get Series API:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)Hiermee kunnen gegevensverzamelingen en de bijbehorende gebeurtenistijdstempels worden uitgevoerd door berekeningen toe te passen die zijn gedefinieerd door variabelen op ruwe gebeurtenissen. Deze variabelen kunnen worden gedefinieerd in het Time Series Model of inline in de query. Deze API ondersteunt paginatie om de volledige antwoordgegevensset voor de geselecteerde invoer op te halen. 

* [Api voor de samengevoegde reeks:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)hiermee kunnen geaggregeerde waarden en de bijbehorende intervaltijdstempels worden opgevraagd door berekeningen toe te passen die zijn gedefinieerd door variabelen op ruwe gebeurtenissen. Deze variabelen kunnen worden gedefinieerd in het Time Series Model of inline in de query. Deze API ondersteunt paginatie om de volledige antwoordgegevensset voor de geselecteerde invoer op te halen. 
  
  Voor een opgegeven zoekperiode en -interval retourneert deze API een geaggregeerde respons per variabele per interval voor een tijdreeks-id. Het aantal intervallen in de responsgegevensset wordt berekend door tijdvakteken te tellen (het aantal milliseconden dat is verstreken sinds het Unix-tijdperk - 1 januari 1970) en de teken te delen door de intervalspangrootte die in de query is opgegeven.

  De tijdstempels die in de antwoordset worden geretourneerd, zijn van de linkerintervalgrenzen, niet van de bemonsterde gebeurtenissen uit het interval. 

## <a name="next-steps"></a>Volgende stappen

- Lees meer over verschillende variabelen die kunnen worden gedefinieerd in het [Time Series Model](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm).
- Lees meer over het opvragen van gegevens uit de [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).
