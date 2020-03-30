---
title: Activiteiten en activiteiten controleren
titleSuffix: Azure Cognitive Search
description: Schakel logboekregistratie in, ontvang gegevens over queryactiviteit, resourcegebruik en andere systeemgegevens van een Azure Cognitive Search-service.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 353e00f902a7314e5e5b7c8ee03e8b925a510b26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462323"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Bewerkingen en activiteiten van Azure Cognitive Search controleren

Dit artikel introduceert monitoring op serviceniveau (resource) op werkbelastingsniveau (query's en indexering) en stelt een framework voor voor het bewaken van gebruikerstoegang.

Over het hele spectrum gebruikt u een combinatie van ingebouwde infrastructuur en basisservices zoals Azure Monitor, evenals service-API's die statistieken, tellingen en status retourneren. Inzicht in het scala aan mogelijkheden kan u helpen bij het samenstellen van een feedbacklus, zodat u problemen oplossen wanneer deze zich voordoen.

## <a name="use-azure-monitor"></a>Azure Monitor gebruiken

Veel services, waaronder Azure Cognitive Search, maken gebruik van [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/) voor waarschuwingen, statistieken en diagnostische gegevens voor logboekregistratie. Voor Azure Cognitive Search wordt de ingebouwde bewakingsinfrastructuur voornamelijk gebruikt voor monitoring op resourceniveau (servicestatus) en [querybewaking.](search-monitor-queries.md)

Met de volgende schermafbeelding u azure-monitorfuncties in de portal vinden.

+ Het tabblad **Controle,** dat zich op de hoofdoverzichtspagina bevindt, toont in één oogopslag de belangrijkste statistieken.
+ **Activiteitenlogboek**, net onder Overzicht, rapporten over acties op resourceniveau: servicestatus en API-key request-meldingen.
+ **Monitoring**, verder in de lijst, biedt configureerbare waarschuwingen, statistieken en diagnostische logboeken. Maak deze wanneer je ze nodig hebt. Zodra gegevens zijn verzameld en opgeslagen, u de informatie opvragen of visualiseren voor inzichten.

![Azure Monitor-integratie in een zoekservice](./media/search-monitor-usage/azure-monitor-search.png
 "Azure Monitor-integratie in een zoekservice")

### <a name="precision-of-reported-numbers"></a>Precisie van gerapporteerde getallen

Portalpagina's worden om de paar minuten vernieuwd. Als zodanig, nummers gemeld in het portaal zijn bij benadering, bedoeld om u een algemeen gevoel van hoe goed uw systeem is onderhoud verzoeken. Werkelijke statistieken, zoals query's per seconde (QPS) kunnen hoger of lager zijn dan het aantal op de pagina.

## <a name="activity-logs-and-service-health"></a>Activiteitslogboeken en servicestatus

Het [**activiteitenlogboek**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) verzamelt informatie van Azure Resource Manager en rapporteert over wijzigingen in de servicestatus. U het activiteitenlogboek controleren op kritieke, fout- en waarschuwingsvoorwaarden met betrekking tot de servicestatus.

Voor in-service taken - zoals query's, indexering of het maken van objecten - ziet u algemene informatieve meldingen zoals *Beheersleutel ophalen* en *Querysleutels ophalen* voor elk verzoek, maar niet de specifieke actie zelf. Voor informatie over deze korrel moet u diagnostische logboekregistratie configureren.

U het **activiteitenlogboek** openen vanuit het linkernavigatiedeelvenster of vanuit Meldingen op de opdrachtbalk van het bovenste venster of via de pagina **Problemen diagnosticeren en oplossen.**

## <a name="monitor-storage"></a>Opslag controleren

Pagina's met tabbladen die zijn ingebouwd in de overzichtspagina rapporteren over het gebruik van resources. Deze informatie wordt beschikbaar zodra u de service gaat gebruiken, zonder dat de configuratie vereist is en de pagina om de paar minuten wordt vernieuwd. 

Als u de laatste hand geeft aan de beslissingen over [welke laag u moet gebruiken voor productieworkloads](search-sku-tier.md)of als u het aantal actieve [replica's en partities wilt aanpassen,](search-capacity-planning.md)kunnen deze statistieken u helpen bij deze beslissingen door u te laten zien hoe snel resources worden verbruikt en hoe goed de huidige configuratie de bestaande belasting verwerkt.

Waarschuwingen met betrekking tot opslag zijn momenteel niet beschikbaar; opslagverbruik wordt niet samengevoegd of aangemeld bij de **AzureMetrics-tabel** in Azure Monitor. U moet [een aangepaste oplossing maken](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-creating) die resourcegerelateerde meldingen uitzendt, waarbij uw code controleert op de grootte van de opslag en het antwoord verwerkt. Zie [Servicestatistieken ophalen](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response)voor meer informatie over opslagstatistieken.

Voor visuele controle in de portal ziet u op het tabblad **Gebruik** de beschikbaarheid van resources ten opzichte van de huidige [limieten](search-limits-quotas-capacity.md) die door de servicelaag worden opgelegd. 

De volgende illustratie is voor de gratis service, die is afgetopt op 3 objecten van elk type en 50 MB opslag. Een Basic- of Standard-service heeft hogere limieten en als u het aantal partities verhoogt, gaat de maximale opslag proportioneel omhoog.

![Gebruiksstatus ten opzichte van laaglimieten](./media/search-monitor-usage/usage-tab.png
 "Gebruiksstatus ten opzichte van laaglimieten")

## <a name="monitor-workloads"></a>Workloads controleren

Geregistreerde gebeurtenissen omvatten gebeurtenissen die betrekking hebben op indexering en query's. De tabel **AzureDiagnostics** in Log Analytics verzamelt operationele gegevens met betrekking tot query's en indexering.

De meeste geregistreerde gegevens zijn voor alleen-lezen bewerkingen. Voor andere bewerkingen voor het verwijderen van create-update-delete die niet in het logboek zijn vastgelegd, u de zoekservice opvragen naar systeemgegevens.

| OperationName | Beschrijving |
|---------------|-------------|
| ServiceStatistieken | Deze bewerking is een routineoproep voor [Servicestatistieken](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics), direct of impliciet aangeroepen om een portaloverzichtspagina in te vullen wanneer deze wordt geladen of vernieuwd. |
| Query.Zoeken |  Queryaanvragen op basis van een index Zie [Monitorquery's](search-monitor-queries.md) voor informatie over aangemelde query's.|
| Indexeren.Index  | Deze bewerking is een aanroep om documenten toe te voegen, bij te [werken of te verwijderen.](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) |
| Indexen. Prototype | Dit is een index die is gemaakt door de wizard Gegevens importeren. |
| Indexers.Maak | Maak een indexeerder expliciet of impliciet via de wizard Gegevens importeren. |
| Indexers.Get | Geeft als resultaat de naam van een indexeerder wanneer de indexer wordt uitgevoerd. |
| Indexers.Status | Geeft als resultaat de status van een indexer wanneer de indexer wordt uitgevoerd. |
| DataSources.Get | Geeft als resultaat de naam van de gegevensbron wanneer een indexer wordt uitgevoerd.|
| Indexen.Get | Geeft als resultaat de naam van een index wanneer een indexer wordt uitgevoerd. |

### <a name="kusto-queries-about-workloads"></a>Kusto-query's over workloads

Als u logboekregistratie hebt ingeschakeld, u **AzureDiagnostics** opvragen voor een lijst met bewerkingen die op uw service zijn uitgevoerd en wanneer. U ook activiteit correleren om veranderingen in prestaties te onderzoeken.

#### <a name="example-list-operations"></a>Voorbeeld: Lijstbewerkingen 

Retourneer een lijst met bewerkingen en een telling van elk van deze bewerkingen.

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Voorbeeld: Bewerkingen correleren

Correleer queryaanvraag met indexeringsbewerkingen en laat de gegevenspunten in een tijdgrafiek samenvallen.

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>Zoek-API's gebruiken

Zowel de Azure Cognitive Search REST API als de .NET SDK bieden programmatische toegang tot servicestatistieken, index- en indexergegevens en documenttellingen.

+ [GET-servicestatistieken](/rest/api/searchservice/get-service-statistics)
+ [GET-indexstatistieken](/rest/api/searchservice/get-index-statistics)
+ [GET-documenttellingen](/rest/api/searchservice/count-documents)
+ [Get Indexer-status](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>Gebruikerstoegang controleren

Omdat zoekindexen een onderdeel zijn van een grotere clienttoepassing, is er geen ingebouwde methodologie voor het controleren of bewaken van de toegang per gebruiker tot een index. Aanvragen worden verondersteld afkomstig te zijn van een clienttoepassing, voor beheerders- of queryaanvragen. Beheerbewerkingen omvatten het maken, bijwerken, verwijderen van objecten in de hele service. Alleen-lezen bewerkingen zijn query's tegen de verzameling van documenten, die zijn beperkt tot één index. 

Als zodanig ziet u verwijzingen naar oproepen met behulp van beheerderssleutels of querysleutels. De juiste sleutel is opgenomen in aanvragen die afkomstig zijn van clientcode. De service is niet uitgerust om identiteitstokens of imitatie te verwerken.

Wanneer er bedrijfsvereisten bestaan voor autorisatie per gebruiker, is de aanbeveling integratie met Azure Active Directory. U $filter en gebruikersidentiteiten gebruiken om zoekresultaten bij te [snijden](search-security-trimming-for-azure-search-with-aad.md) van documenten die een gebruiker niet mag zien. 

Er is geen manier om deze informatie apart te registreren van de querytekenreeks die de parameter $filter bevat. Zie [Query's controleren](search-monitor-queries.md) voor meer informatie over rapportagequerytekenreeksen.

## <a name="next-steps"></a>Volgende stappen

Vloeiendheid met Azure Monitor is essentieel voor het toezicht op elke Azure-service, inclusief bronnen zoals Azure Cognitive Search. Als u niet bekend bent met Azure Monitor, neemt u de tijd om artikelen met betrekking tot resources te bekijken. Naast tutorials, het volgende artikel is een goede plek om te beginnen.

> [!div class="nextstepaction"]
> [Azure-resources bewaken met Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
