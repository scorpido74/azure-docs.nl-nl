---
title: Servicebeheer in het portaal
titleSuffix: Azure Cognitive Search
description: Beheer een Azure Cognitive Search-service, een gehoste cloudzoekservice op Microsoft Azure, met behulp van de Azure-portal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3abbf2c8e0734d17aabadd2ae5f61cc03889964b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282924"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Servicebeheer voor Azure Cognitive Search in de Azure-portal
> [!div class="op_single_selector"]
> * [Powershell](search-manage-powershell.md)
> * [REST-API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Cognitive Search is een volledig beheerde, cloudgebaseerde zoekservice die wordt gebruikt voor het bouwen van een uitgebreide zoekervaring in aangepaste apps. In dit artikel worden de servicebeheertaken die u uitvoeren in de [Azure-portal](https://portal.azure.com) voor een zoekservice die u al hebt ingericht. Servicebeheer is lichtgewicht door het ontwerp, beperkt tot de volgende taken:

> [!div class="checklist"]
> * Beheer de toegang tot de *api-toetsen* die worden gebruikt voor lees- of schrijftoegang tot uw service.
> * De servicecapaciteit aanpassen door de toewijzing van partities en replica's te wijzigen.
> * Houd het resourcegebruik in de gaten ten opzichte van de maximale limieten van uw servicelaag.

Merk op dat *de upgrade* niet wordt vermeld als een administratieve taak. Omdat resources worden toegewezen wanneer de service wordt ingericht, vereist het verplaatsen naar een andere laag een nieuwe service. Zie Een [Azure Cognitive Search-service maken](search-create-service-portal.md)voor meer informatie.

U het queryvolume en andere statistieken controleren en deze inzichten gebruiken om uw service aan te passen voor snellere responstijden. Zie Voor meer informatie [de gegevens over het gebruik en de query en](search-monitor-usage.md) prestaties en optimalisatie [controleren.](search-performance-optimization.md)

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Beheerdersrechten
Het inrichten of ontmantelen van de service zelf kan worden gedaan door een Azure-abonnementsbeheerder of co-beheerder.

Binnen een service heeft iedereen met toegang tot de service-URL en een admin api-key lees-schrijftoegang tot de service. Lees-schrijftoegang biedt de mogelijkheid om serverobjecten toe te voegen, te verwijderen of te wijzigen, waaronder api-sleutels, indexen, indexeerders, gegevensbronnen, schema's en roltoewijzingen zoals geïmplementeerd via [RBAC-gedefinieerde rollen.](search-security-rbac.md)

Alle gebruikersinteractie met Azure Cognitive Search valt binnen een van deze modi: lees-schrijftoegang tot de service (beheerdersrechten) of alleen-lezen toegang tot de service (queryrechten). Zie [De api-toetsen beheren](search-security-api-keys.md)voor meer informatie.

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Logboekregistratie en systeeminformatie
Azure Cognitive Search stelt logboekbestanden voor een afzonderlijke service niet bloot via de portal of programmatische interfaces. Op de basislaag en hoger controleert Microsoft alle Azure Cognitive Search-services op 99,9% beschikbaarheid per servicelevel (SLA). Als de service traag is of de doorvoer van aanvragen onder SLA-drempelwaarden valt, controleren ondersteuningsteams de logbestanden die voor hen beschikbaar zijn en pakken ze het probleem op.

In termen van algemene informatie over uw service, u informatie verkrijgen op de volgende manieren:

* In de portal, op het servicedashboard, via meldingen, eigenschappen en statusberichten.
* PowerShell of de [API Beheer REST](https://docs.microsoft.com/rest/api/searchmanagement/) gebruiken om [service-eigenschappen](https://docs.microsoft.com/rest/api/searchmanagement/services)of status op het gebruik van indexbronnen te krijgen. [PowerShell](search-manage-powershell.md)


<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Resourcegebruik controleren
In het dashboard is resourcecontrole beperkt tot de informatie in het servicedashboard en een paar statistieken die u verkrijgen door de service te bevragen. In het servicedashboard u in de sectie Gebruik snel bepalen of de resourceniveaus voor partitiebronnen geschikt zijn voor uw toepassing. U externe bronnen inrichten, zoals Azure-bewaking, als u geregistreerde gebeurtenissen wilt vastleggen en blijven behouden. Zie [Azure Cognitive Search controleren](search-monitor-usage.md)voor meer informatie.

Met behulp van de rest-api van zoekservice u programmatisch rekenen op documenten en indexen: 

* [Indexstatistieken ophalen](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Documenten tellen](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Noodherstel en serviceuitval

Hoewel we uw gegevens kunnen redden, biedt Azure Cognitive Search geen directe failover van de service als er een storing is op cluster- of datacenterniveau. Als een cluster uitvalt in het datacenter, detecteert en werkt het operations team aan het herstellen van de service. U zult downtime ervaren tijdens de servicerestauratie, maar u servicecredits aanvragen om de onbeschikbaarheid van de service te compenseren volgens de [Service Level Agreement (SLA).](https://azure.microsoft.com/support/legal/sla/search/v1_0/) 

Als continue service vereist is in het geval van catastrofale fouten buiten de controle van Microsoft, u een extra service in een andere regio [inrichten](search-create-service-portal.md) en een georeplicatiestrategie implementeren om ervoor te zorgen dat indexen volledig redundant zijn voor alle services.

Klanten die [indexeerders](search-indexer-overview.md) gebruiken om indexen te vullen en te vernieuwen, kunnen herstel na noodgevallen verwerken via geospecifieke indexeerders die dezelfde gegevensbron gebruiken. Twee services in verschillende regio's, elk met een indexer, kunnen dezelfde gegevensbron indexeren om georedundantie te bereiken. Als u indexeert uit gegevensbronnen die ook georedundant zijn, moet u er rekening mee houden dat Azure Cognitive Search-indexeerders alleen incrementele indexering (het samenvoegen van updates van nieuwe, gewijzigde of verwijderde documenten) van primaire replica's kunnen uitvoeren. In een failovergebeurtenis moet u de indexeropnieuw naar de nieuwe primaire replica wijzen. 

Als u geen indexeerders gebruikt, gebruikt u uw toepassingscode om objecten en gegevens parallel naar verschillende zoekservices te pushen. Zie [Prestaties en optimalisatie in Azure Cognitive Search](search-performance-optimization.md)voor meer informatie.

## <a name="backup-and-restore"></a>Back-ups en herstellen

Omdat Azure Cognitive Search geen primaire oplossing voor gegevensopslag is, bieden we geen formeel mechanisme voor selfserviceback-up en herstel. U echter de voorbeeldcode voor het herstellen van **de index-back-up** in deze [azure cognitive search .net-voorbeeldrepo](https://github.com/Azure-Samples/azure-search-dotnet-samples) gebruiken om een back-up te maken van uw indexdefinitie en momentopname naar een reeks JSON-bestanden en deze bestanden vervolgens te gebruiken om de index te herstellen, indien nodig. Met deze tool kunnen ook indexen tussen servicelagen worden verplaatst.

Anders is uw toepassingscode die wordt gebruikt voor het maken en vullen van een index de de facto hersteloptie als u per ongeluk een index verwijdert. Als u een index wilt herbouwen, verwijdert u deze (ervan uitgaande dat deze bestaat), maakt u de index in de service opnieuw en herlaadt u door gegevens uit uw primaire gegevensarchief op te halen.

<a id="scale"></a>

## <a name="scale-up-or-down"></a>Omhoog of omlaag schalen
Elke zoekservice begint met minimaal één replica en één partitie. Als u zich hebt aangemeld voor een [laag met speciale resources,](search-limits-quotas-capacity.md)klikt u op de tegel **SCHAAL** in het servicedashboard om het resourcegebruik aan te passen.

Wanneer u capaciteit toevoegt via een van beide bronnen, gebruikt de service deze automatisch. Er is geen verdere actie vereist van uw kant, maar er is een lichte vertraging voordat de impact van de nieuwe bron wordt gerealiseerd. Het kan 15 minuten of langer duren om extra resources in te richten.

 ![][10]

### <a name="add-replicas"></a>Replica's toevoegen
Het verhogen van query's per seconde (QPS) of het bereiken van hoge beschikbaarheid wordt gedaan door het toevoegen van replica's. Elke replica heeft één kopie van een index, dus het toevoegen van nog een replica vertaalt naar nog een index die beschikbaar is voor het afhandelen van servicequeryaanvragen. Er zijn minimaal 3 replica's nodig voor een hoge beschikbaarheid (zie [Capaciteitsplanning](search-capacity-planning.md) voor meer informatie).

Een zoekservice met meer replica's kan saldoqueryaanvragen laden over een groter aantal indexen. Gezien een niveau van queryvolume zal de querydoorvoer sneller zijn wanneer er meer kopieën van de index beschikbaar zijn om de aanvraag te bedienen. Als u querylatentie ondervindt, u een positieve invloed op de prestaties verwachten zodra de extra replica's online zijn.

Hoewel de querydoorvoer omhoog gaat als u replica's toevoegt, wordt deze niet bepaald verdubbeld of verdrievoudigd als u replica's aan uw service toevoegt. Alle zoektoepassingen zijn onderhevig aan externe factoren die de queryprestaties kunnen beïnvloeden. Complexe query's en netwerklatentie zijn twee factoren die bijdragen aan variaties in queryresponstijden.

### <a name="add-partitions"></a>Partities toevoegen
De meeste servicetoepassingen hebben een ingebouwde behoefte aan meer replica's in plaats van partities. Voor die gevallen waarin een verhoogd aantal documenten vereist is, u partities toevoegen als u zich hebt aangemeld voor de standaardservice. De basislaag voorziet niet in extra partities.

Op de standaardlaag worden partities toegevoegd in veelvouden van 12 (specifiek, 1, 2, 3, 4, 6 of 12). Dit is een artefact van scherven. Een index wordt gemaakt in 12 shards, die allemaal kunnen worden opgeslagen op 1 partitie of gelijk verdeeld in 2, 3, 4, 6 of 12 partities (een scherf per partitie).

### <a name="remove-replicas"></a>Replica's verwijderen
Na perioden met hoge queryvolumes u de schuifregelaar gebruiken om replica's te verminderen nadat het laadt op zoekopdrachten is genormaliseerd (bijvoorbeeld nadat de verkoop van de vakantie voorbij is). Er zijn geen verdere stappen vereist van uw kant. Als u het aantal replica's verlaagt, worden virtuele machines in het datacenter opgegeven. Uw query- en gegevensopnamebewerkingen worden nu uitgevoerd op minder VM's dan voorheen. De minimale vereiste is één replica.

### <a name="remove-partitions"></a>Partities verwijderen
In tegenstelling tot het verwijderen van replica's, die geen extra inspanning van uw kant vereist, heb je misschien wat werk te doen als je meer opslagruimte gebruikt dan kan worden verminderd. Als uw oplossing bijvoorbeeld drie partities gebruikt, genereert het inkrimpen tot een of twee partities een fout als de nieuwe opslagruimte kleiner is dan vereist voor het hosten van uw index. Zoals u zou verwachten, zijn uw keuzes om indexen of documenten binnen een bijbehorende index te verwijderen om ruimte vrij te maken of de huidige configuratie te behouden.

Er is geen detectiemethode die u vertelt welke indexshards ten minste op specifieke partities worden opgeslagen. Elke partitie biedt ongeveer 25 GB opslagruimte, dus u moet de opslag beperken tot een grootte die kan worden aangepast door het aantal partities dat u hebt. Als u wilt terugkeren naar één partitie, moeten alle 12 shards passen.

Als u wilt helpen met toekomstige planning, u de opslag controleren (met [indexstatistieken van get)](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)om te zien hoeveel u daadwerkelijk hebt gebruikt. 

<a id="next-steps"></a>

## <a name="next-steps"></a>Volgende stappen
Zodra u de concepten achter servicebeheer begrijpt, u overwegen [PowerShell](search-manage-powershell.md) te gebruiken om taken te automatiseren.

We raden ook aan om het [artikel over prestaties en optimalisatie te](search-performance-optimization.md)bekijken.

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



