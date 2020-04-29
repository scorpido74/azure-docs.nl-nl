---
title: Bewerkingen en activiteiten bewaken
titleSuffix: Azure Cognitive Search
description: Schakel logboek registratie in, Bekijk metrische gegevens over de query activiteit, het resource gebruik en andere systeem data van een Azure Cognitive Search-service.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 353e00f902a7314e5e5b7c8ee03e8b925a510b26
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77462323"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Bewerkingen en activiteiten van Azure Cognitive Search bewaken

In dit artikel vindt u een overzicht van het niveau van de service (resource), op het niveau van de werk belasting (query's en indexering), en wordt een framework voorgesteld voor het bewaken van gebruikers toegang.

In het spectrum gebruikt u een combi natie van ingebouwde infra structuur en Foundational services zoals Azure Monitor, evenals service-Api's die statistieken, aantallen en status retour neren. Het bereik van mogelijkheden kan u helpen een feedback-lus te maken, zodat u problemen kunt oplossen wanneer deze zich voordoen.

## <a name="use-azure-monitor"></a>Azure Monitor gebruiken

Veel services, met inbegrip van Azure Cognitive Search, maken gebruik van [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/) voor waarschuwingen, metrische gegevens en Diagnostische logboeken. Voor Azure Cognitive Search wordt de ingebouwde bewakings infrastructuur voornamelijk gebruikt voor bewaking op resource niveau (service status) en [query bewaking](search-monitor-queries.md).

De volgende scherm afbeelding helpt u bij het vinden van Azure Monitor functies in de portal.

+ Op het tabblad **controle** , dat zich op de overzichts pagina bevindt, worden belang rijke metrische gegevens in één oogopslag weer gegeven.
+ **Activiteiten logboek**, net onder overzicht, rapporten over acties op resource niveau: meldingen over de service status en API-sleutel aanvragen.
+ Door de lijst te **bewaken**kunt u waarschuwingen, metrische gegevens en Diagnostische logboeken configureren. Maak deze wanneer u ze nodig hebt. Zodra de gegevens zijn verzameld en opgeslagen, kunt u de informatie voor inzichten opvragen of visualiseren.

![Integratie in een zoek service Azure Monitor](./media/search-monitor-usage/azure-monitor-search.png
 "Integratie in een zoek service Azure Monitor")

### <a name="precision-of-reported-numbers"></a>Nauw keurigheid van gerapporteerde getallen

Portal pagina's worden elke paar minuten vernieuwd. De getallen die in de portal zijn opgenomen, zijn ongeveer zo ontworpen dat u een algemeen idee hebt van de manier waarop uw systeem bezig is met het verwerken van aanvragen. Werkelijke metrische gegevens, zoals query's per seconde (QPS), kunnen hoger of lager zijn dan het nummer dat op de pagina wordt weer gegeven.

## <a name="activity-logs-and-service-health"></a>Activiteiten logboeken en service status

In het [**activiteiten logboek**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) worden gegevens van Azure Resource Manager verzameld en rapporten over wijzigingen in de service status. U kunt het activiteiten logboek controleren op kritieke, fout-en waarschuwings voorwaarden met betrekking tot de service status.

Voor taken in de service, zoals query's, het indexeren of het maken van objecten, worden algemene informatieve meldingen weer geven, zoals *admin-sleutel ophalen* en *query sleutels ophalen* voor elke aanvraag, maar niet de specifieke actie zelf. Voor informatie over deze korrel moet u diagnostische logboek registratie configureren.

U kunt het **activiteiten logboek** openen vanuit het navigatie deel venster, of vanuit meldingen in de bovenste venster opdracht balk, of via de pagina **problemen vaststellen en oplossen** .

## <a name="monitor-storage"></a>Opslag bewaken

Pagina's met tabbladen die in het overzichts venster zijn ingebouwd, worden weer gegeven in het resource gebruik. Deze informatie wordt beschikbaar zodra u de service gaat gebruiken, zonder dat de configuratie is vereist en de pagina om de paar minuten wordt vernieuwd. 

Als u besluit [om te bepalen welke laag moet worden gebruikt voor werk belastingen voor de productie](search-sku-tier.md), of als u [het aantal actieve replica's en partities wilt aanpassen](search-capacity-planning.md), kunt u aan de hand van deze maat regelen zien hoe snel resources worden verbruikt en hoe goed de huidige configuratie de bestaande belasting afhandelt.

Waarschuwingen met betrekking tot opslag zijn momenteel niet beschikbaar. het opslag verbruik is niet geaggregeerd of aangemeld bij de **AzureMetrics** -tabel in azure monitor. U moet [een aangepaste oplossing bouwen](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-creating) die meldingen over resources verzendt, waarbij uw code controleert op opslag grootte en het antwoord verwerkt. Zie [service statistieken ophalen](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response)voor meer informatie over metrische gegevens over de opslag.

Voor visuele bewaking in de portal toont het tabblad **gebruik** de beschik baarheid van resources ten opzichte van de huidige [limieten](search-limits-quotas-capacity.md) die door de servicelaag worden opgelegd. 

De volgende afbeelding is voor de gratis service, die wordt afgelimiteerd bij 3 objecten van elk type en 50 MB aan opslag. Een Basic-of Standard-Service heeft hogere limieten en als u het aantal partities verhoogt, wordt de maximale opslag proportioneel.

![Gebruiks status relatief ten opzichte van laag limieten](./media/search-monitor-usage/usage-tab.png
 "Gebruiks status relatief ten opzichte van laag limieten")

## <a name="monitor-workloads"></a>Werk belastingen bewaken

Geregistreerde gebeurtenissen zijn onder andere degene die zijn gerelateerd aan indexering en query's. De **AzureDiagnostics** -tabel in log Analytics verzamelt operationele gegevens met betrekking tot query's en indexering.

De meeste geregistreerde gegevens zijn alleen-lezen-bewerkingen. Voor andere Create-update-delete-bewerkingen die niet in het logboek zijn vastgelegd, kunt u een query uitvoeren op de zoek service voor systeem info.

| OperationName | Beschrijving |
|---------------|-------------|
| ServiceStats | Met deze bewerking wordt een routine aanroep uitgevoerd om [service statistieken](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics)op te halen, direct of impliciet te worden aangeroepen om een portal overzichts pagina te vullen wanneer deze wordt geladen of vernieuwd. |
| Query. Search |  Query's aanvragen voor een index Zie [controle query's](search-monitor-queries.md) voor informatie over vastgelegde query's.|
| Indexeren. index  | Deze bewerking is een aanroep om [documenten toe te voegen, bij te werken of te verwijderen](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). |
| indexen. Drukvat | Dit is een index die is gemaakt met de wizard gegevens importeren. |
| Indexeer functies. maken | Maak expliciet of impliciet een Indexeer functie met behulp van de wizard gegevens importeren. |
| Indexeer functies. ophalen | Retourneert de naam van een Indexeer functie wanneer de Indexeer functie wordt uitgevoerd. |
| Indexeer functies. status | Retourneert de status van een Indexeer functie wanneer de Indexeer functie wordt uitgevoerd. |
| Gegevens bronnen. ophalen | Retourneert de naam van de gegevens bron wanneer een Indexeer functie wordt uitgevoerd.|
| Indexes. Get | Retourneert de naam van een index wanneer een Indexeer functie wordt uitgevoerd. |

### <a name="kusto-queries-about-workloads"></a>Kusto query's over workloads

Als u logboek registratie hebt ingeschakeld, kunt u een query uitvoeren op **AzureDiagnostics** voor een lijst met bewerkingen die zijn uitgevoerd op uw service en wanneer. U kunt activiteiten ook correleren om de prestatie wijzigingen te onderzoeken.

#### <a name="example-list-operations"></a>Voor beeld: bewerkingen weer geven 

Retourneert een lijst met bewerkingen en een telling van elke bewerking.

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Voor beeld: correlatie bewerkingen

Correleer een query aanvraag met indexerings bewerkingen en Genereer de gegevens punten in een tijd diagram om de bewerkingen samen te zien.

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>Zoek-Api's gebruiken

Zowel de Azure Cognitive Search REST API als de .NET SDK bieden programmatische toegang tot metrische gegevens van services, index-en indexerings informatie en document tellingen.

+ [Service statistieken ophalen](/rest/api/searchservice/get-service-statistics)
+ [Index statistieken ophalen](/rest/api/searchservice/get-index-statistics)
+ [Aantal documenten ophalen](/rest/api/searchservice/count-documents)
+ [De Indexeer functie-status ophalen](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>Gebruikers toegang controleren

Omdat zoek indexen een onderdeel zijn van een grotere client toepassing, is er geen ingebouwde methodologie voor het beheren of bewaken van toegang per gebruiker tot een index. Er wordt van uitgegaan dat er aanvragen worden opgehaald uit een client toepassing, voor beheerders-of query aanvragen. Lees-en schrijf bewerkingen door de beheerder omvatten het maken, bijwerken en verwijderen van objecten in de gehele service. Alleen-lezen bewerkingen zijn query's voor de verzameling documenten, met een bereik van één index. 

Wat u in de activiteiten logboeken ziet, zijn verwijzingen naar aanroepen met behulp van beheer sleutels of query sleutels. De juiste sleutel is opgenomen in aanvragen die afkomstig zijn van client code. De service is niet ingericht voor het afhandelen van identiteits tokens of imitatie.

Wanneer er bedrijfs vereisten bestaan voor autorisatie per gebruiker, wordt de aanbeveling geïntegreerd met Azure Active Directory. U kunt $filter en gebruikers identiteiten gebruiken om de [Zoek resultaten](search-security-trimming-for-azure-search-with-aad.md) van documenten die een gebruiker niet mag zien te knippen. 

Het is niet mogelijk om deze informatie afzonderlijk te registreren vanuit de query reeks die de para meter $filter bevat. Zie [query's bewaken](search-monitor-queries.md) voor meer informatie over het rapporteren van query teken reeksen.

## <a name="next-steps"></a>Volgende stappen

Fluency met Azure Monitor is essentieel voor het toezicht op elke Azure-service, inclusief resources zoals Azure Cognitive Search. Als u niet bekend bent met Azure Monitor, neemt u de tijd voor het beoordelen van artikelen die betrekking hebben op resources. Naast zelf studies is het volgende artikel een goede plaats om te beginnen.

> [!div class="nextstepaction"]
> [Azure-resources bewaken met Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
