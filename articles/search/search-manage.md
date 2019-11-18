---
title: Service beheer in de portal
titleSuffix: Azure Cognitive Search
description: Een Azure Cognitive Search-service beheren, een gehoste service voor zoeken in de Cloud op Microsoft Azure met behulp van de Azure Portal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e00a810e7977e1c45c1833e0b901ff6804f7fb32
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113286"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Service beheer voor Azure Cognitive Search in het Azure Portal
> [!div class="op_single_selector"]
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Cognitive Search is een volledig beheerde, op de cloud gebaseerde zoek service die wordt gebruikt voor het bouwen van een uitgebreide zoek ervaring in aangepaste apps. In dit artikel worden de Service beheer taken behandeld die u kunt uitvoeren in de [Azure Portal](https://portal.azure.com) voor een zoek service die u al hebt ingericht. Service beheer is lichter ontwerp, beperkt tot de volgende taken:

> [!div class="checklist"]
> * Beheer de toegang tot de *API-sleutels* die worden gebruikt voor lees-of schrijf toegang tot uw service.
> * Pas de service capaciteit aan door de toewijzing van partities en replica's te wijzigen.
> * Het resource gebruik bewaken ten opzichte van maximum limieten van uw servicelaag.

U ziet dat de *upgrade* niet wordt weer gegeven als een beheer taak. Omdat resources worden toegewezen wanneer de service wordt ingericht, is een nieuwe service vereist voor het verplaatsen naar een andere laag. Zie [een Azure Cognitive Search-service maken](search-create-service-portal.md)voor meer informatie.

U kunt het query volume en andere metrische gegevens bewaken en deze inzichten gebruiken om uw service aan te passen voor snellere reactie tijden. Zie het [gebruik en de metrische gegevens van Query's controleren](search-monitor-usage.md) en [prestaties en optimalisatie](search-performance-optimization.md)voor meer informatie.

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Beheerders rechten
Het inrichten of buiten gebruik stellen van de service zelf kan worden uitgevoerd door een beheerder van een Azure-abonnement of een mede beheerder.

Binnen een service heeft iedereen die toegang heeft tot de service-URL en een beheer-API-sleutel, lees-/schrijftoegang tot de service. Lees-/schrijftoegang biedt de mogelijkheid om Server objecten toe te voegen, te verwijderen of te wijzigen, met inbegrip van API-sleutels, indexen, Indexeer functies, gegevens bronnen, planningen en roltoewijzingen zoals geïmplementeerd via door [RBAC gedefinieerde rollen](search-security-rbac.md).

Alle gebruikers interactie met Azure Cognitive Search valt binnen een van deze modi: lees-/schrijftoegang tot de service (beheerders rechten) of alleen-lezen toegang tot de service (query rechten). Zie [Manage the API-Keys (](search-security-api-keys.md)Engelstalig) voor meer informatie.

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Logboek registratie-en systeem gegevens
Azure Cognitive Search maakt geen logboek bestanden voor een afzonderlijke service beschikbaar via de portal-of programmatische interfaces. Op de basis-laag en hierboven bewaakt micro soft alle Azure Cognitive Search Services voor 99,9% Beschik baarheid per service level agreements (SLA). Als de service traag is of de door Voer van een aanvraag onder de drempel waarde voor de SLA ligt, worden de beschik bare logboek bestanden door ondersteunings teams gecontroleerd en wordt het probleem opgelost.

Voor algemene informatie over uw service kunt u op de volgende manieren informatie verkrijgen:

* In de portal, op het service dashboard, via meldingen, eigenschappen en status berichten.
* Gebruik [Power shell](search-manage-powershell.md) of het [Management rest API](https://docs.microsoft.com/rest/api/searchmanagement/) om [service-eigenschappen](https://docs.microsoft.com/rest/api/searchmanagement/services)of status van het gebruik van index bronnen op te halen.


<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Resource gebruik bewaken
In het dash board is bron bewaking beperkt tot de informatie die wordt weer gegeven in het service dashboard en enkele metrische gegevens die u kunt verkrijgen door de service te doorzoeken. In het service dashboard, in de sectie gebruik, kunt u snel bepalen of partitie bron niveaus voldoende zijn voor uw toepassing. U kunt externe resources, zoals Azure-bewaking, inrichten als u vastgelegde gebeurtenissen wilt vastleggen en persistent wilt maken. Zie [Azure Cognitive Search bewaken](search-monitor-usage.md)voor meer informatie.

Met de REST API van de zoek service kunt u via programma code een aantal op documenten en indexen ontvangen: 

* [Indexstatistieken ophalen](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Aantal documenten](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Herstel na nood gevallen en service storingen

Hoewel we uw gegevens kunnen bijvoegen, biedt Azure Cognitive Search geen directe failover van de service als er een storing optreedt op het niveau van het cluster of het Data Center. Als een cluster in het Data Center uitvalt, detecteert en werkt het operations-team de service te herstellen. U krijgt tijdens het herstellen van de service een uitval tijd, maar u kunt Service tegoed aanvragen om service niet-beschik baarheid per [Service Level Agreement (Sla)](https://azure.microsoft.com/support/legal/sla/search/v1_0/)te compenseren. 

Als er een continue service vereist is in het geval van fatale storingen buiten de controle van micro soft, kunt u [een extra service](search-create-service-portal.md) in een andere regio inrichten en een geo-replicatie strategie implementeren om ervoor te zorgen dat indexen volledig redundant zijn in alle services.

Klanten die [Indexeer functies](search-indexer-overview.md) gebruiken om indexen in te vullen en te vernieuwen, kunnen herstel na nood gevallen afhandelen via geo-specifieke Indexeer functies die gebruikmaken van dezelfde gegevens bron. Twee services in verschillende regio's, elk waarop een Indexeer functie wordt uitgevoerd, kunnen dezelfde gegevens bron indexeren om geo-redundantie te garanderen. Als u gegevens bronnen indexeert die ook geo-redundant zijn, moet u er rekening mee houden dat Azure Cognitive Search Indexeer functies alleen incrementele indexering van primaire replica's kunnen uitvoeren. Zorg er bij een failover-gebeurtenis voor dat u de Indexeer functie opnieuw naar de nieuwe primaire replica toewijst. 

Als u geen Indexeer functies gebruikt, gebruikt u uw toepassings code om objecten en gegevens parallel naar verschillende zoek services te pushen. Zie [prestaties en optimalisatie in Azure Cognitive Search](search-performance-optimization.md)voor meer informatie.

## <a name="backup-and-restore"></a>Back-up maken en terugzetten

Omdat Azure Cognitive Search geen primaire oplossing voor gegevens opslag is, bieden we geen formeel mechanisme voor Self-Service back-up en herstel. U kunt echter de voorbeeld code **index-Backup-Restore** in dit [Azure Cognitive Search .net-voor beeld opslag plaats](https://github.com/Azure-Samples/azure-search-dotnet-samples) gebruiken om een back-up te maken van de index definitie en moment opname naar een reeks json-bestanden en vervolgens deze bestanden te gebruiken om de index te herstellen, indien nodig. Met dit hulp programma kunt u ook indexen verplaatsen tussen service lagen.

Anders is uw toepassings code die wordt gebruikt voor het maken en vullen van een index de optie voor het terugzetten van de index. Als u een index opnieuw wilt samen stellen, verwijdert u deze (ervan uitgaande dat deze bestaat), maakt u de index opnieuw in de service en laadt u deze opnieuw door gegevens op te halen uit uw primaire gegevens opslag.

<a id="scale"></a>

## <a name="scale-up-or-down"></a>Omhoog of omlaag schalen
Elke zoek service begint met mini maal één replica en één partitie. Als u zich hebt geregistreerd voor een [laag die toegewezen resources biedt](search-limits-quotas-capacity.md), klikt u op de tegel **schaal** in het service dashboard om het resource gebruik aan te passen.

Wanneer u capaciteit toevoegt via een van de resources, gebruikt de service deze automatisch. Er is geen verdere actie vereist voor uw onderdeel, maar er is een lichte vertraging voordat de impact van de nieuwe resource wordt gerealiseerd. Het kan 15 minuten of langer duren om aanvullende resources in te richten.

 ![][10]

### <a name="add-replicas"></a>Replica's toevoegen
Het verhogen van query's per seconde (QPS) of het bereiken van hoge Beschik baarheid geschiedt door het toevoegen van replica's. Elke replica heeft één exemplaar van een index, dus er wordt nog één replica omgezet in een meer index die beschikbaar is voor het verwerken van aanvragen voor service query's. Er zijn mini maal drie replica's vereist voor hoge Beschik baarheid (Zie [capaciteits planning](search-capacity-planning.md) voor meer informatie).

Een zoek service met meer replica's kan Load Balancing query-aanvragen over een groter aantal indexen. Op basis van een niveau van het query volume is de query doorvoer sneller wanneer er meer exemplaren van de index beschikbaar zijn voor de service van de aanvraag. Als u een query latentie ondervindt, kunt u een positieve invloed op de prestaties verwachten zodra de extra replica's online zijn.

Hoewel de query doorvoer tijdens het toevoegen van replica's wordt bereikt, is dit niet precies twee of drie maal wanneer u replica's aan uw service toevoegt. Alle zoek toepassingen zijn onderhevig aan externe factoren die de query prestaties kunnen impinge. Complexe query's en netwerk latentie zijn twee factoren die bijdragen aan variaties in de reactie tijden van query's.

### <a name="add-partitions"></a>Partities toevoegen
De meeste service toepassingen hebben een ingebouwde nood zaak voor meer replica's in plaats van partities. In gevallen waarin een groter aantal documenten vereist is, kunt u partities toevoegen als u zich hebt geregistreerd voor de Standard-Service. De Basic-laag biedt geen extra partities.

In de laag standaard worden partities toegevoegd in veelvouden van 12 (met name 1, 2, 3, 4, 6 of 12). Dit is een artefact van sharding. Er wordt een index gemaakt in 12 Shards, die allemaal op één partitie kan worden opgeslagen of op dezelfde manier kan worden onderverdeeld in 2, 3, 4, 6 of 12 partities (één Shard per partitie).

### <a name="remove-replicas"></a>Replica's verwijderen
Na Peri Oden van hoge query volumes kunt u de schuif regelaar gebruiken om replica's te reduceren nadat de geladen Zoek query's zijn genormaliseerd (bijvoorbeeld nadat de verkoop van de feest dagen is voltooid). Er zijn geen verdere stappen voor uw onderdeel vereist. Het aantal replica's relinquishes virtuele machines in het Data Center verlagen. Uw bewerkingen voor het opnemen van query's en gegevens worden nu op minder Vm's uitgevoerd dan voorheen. De minimum vereiste is één replica.

### <a name="remove-partitions"></a>Partities verwijderen
In tegens telling tot het verwijderen van replica's, waarvoor u geen extra moeite hoeft te doen, hebt u mogelijk werk nodig als u meer opslag gebruikt dan kan worden verkleind. Als uw oplossing bijvoorbeeld drie partities gebruikt, genereert Overweeg naar een of twee partities een fout als de nieuwe opslag ruimte minder is dan vereist is om uw index te hosten. Zoals u kunt verwachten, zijn uw keuzes het verwijderen van indexen of documenten binnen een gekoppelde index om ruimte vrij te maken of de huidige configuratie te houden.

Er is geen detectie methode die aangeeft welke index Shards worden opgeslagen op specifieke partities. Elke partitie biedt ongeveer 25 GB opslag ruimte, dus u moet de opslag beperken tot een grootte die kan worden aangepast aan het aantal partities dat u hebt. Als u wilt terugkeren naar één partitie, moeten alle 12 Shards passen.

Als u hulp nodig hebt bij het plannen van de toekomst, wilt u mogelijk de opslag controleren (met behulp van [index statistieken ophalen](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) om te zien hoeveel u werkelijk hebt gebruikt. 

<a id="next-steps"></a>

## <a name="next-steps"></a>Volgende stappen
Wanneer u de concepten van het Service beheer begrijpt, kunt u [Power shell](search-manage-powershell.md) gebruiken om taken te automatiseren.

We raden u ook aan het [artikel over prestaties en optimalisatie te](search-performance-optimization.md)bekijken.

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



