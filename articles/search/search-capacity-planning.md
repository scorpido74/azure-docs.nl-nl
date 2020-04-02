---
title: Capaciteit voor query- en indexworkloads aanpassen
titleSuffix: Azure Cognitive Search
description: Pas partitie- en replicacomputerbronnen aan in Azure Cognitive Search, waarbij elke resource is geprijsd in factureerbare zoekeenheden.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 270ff3c3e8e4cffbb1f4b1987ee497530d0c0982
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546267"
---
# <a name="adjust-capacity-in-azure-cognitive-search"></a>Capaciteit aanpassen in Azure Cognitive Search

Voordat [u een zoekservice indient](search-create-service-portal.md) en een specifieke prijscategorie vergrendelt, neemt u enkele minuten de tijd om inzicht te krijgen in de rol van replica's en partities in een service en hoe u een service aanpassen aan pieken en dalen in de vraag naar resources.

Capaciteit is een functie van de [laag die u kiest](search-sku-tier.md) (lagen bepalen hardwarekenmerken) en de replica- en partitiecombinatie die nodig is voor geprojecteerde workloads. Afhankelijk van de laag en de grootte van de aanpassing kan het toevoegen of verkleinen van de capaciteit van 15 minuten tot enkele uren duren. 

Wanneer u de toewijzing van replica's en partities wijzigt, raden we u aan de Azure-portal te gebruiken. Het portaal handhaaft limieten voor toegestane combinaties die onder de maximale limieten van een laag blijven. Als u echter een op scripts gebaseerde of codegebaseerde inrichtingsbenadering nodig hebt, zijn de [Azure PowerShell](search-manage-powershell.md) of de [API beheerrest](https://docs.microsoft.com/rest/api/searchmanagement/services) alternatieve oplossingen.

## <a name="terminology-replicas-and-partitions"></a>Terminologie: replica's en partities

|||
|-|-|
|*Partities* | Biedt indexopslag en I/O voor lees-/schrijfbewerkingen (bijvoorbeeld bij het opnieuw opbouwen of vernieuwen van een index). Elke partitie heeft een deel van de totale index. Als u drie partities toewijst, wordt uw index verdeeld in derden. |
|*Replica's* | Exemplaren van de zoekservice, voornamelijk gebruikt voor het laden van balansquerybewerkingen. Elke replica is één kopie van een index. Als u drie replica's toewijst, zijn er drie exemplaren van een index beschikbaar voor het onderhoud van queryaanvragen.|

## <a name="when-to-add-nodes"></a>Wanneer knooppunten toevoegen

In eerste instantie wordt een service toegewezen aan een minimaal niveau van resources bestaande uit een partitie en een replica. 

Eén service moet over voldoende resources beschikken om alle workloads (indexering en query's) te verwerken. Geen van beide workloads wordt op de achtergrond uitgevoerd. U indexering plannen voor momenten waarop queryaanvragen natuurlijk minder frequent zijn, maar de service zal de ene taak niet prioriteren boven de andere. Bovendien wordt de queryprestaties voor queryprestaties vloeiender gemaakt wanneer services of knooppunten intern worden bijgewerkt.

Als algemene regel hebben zoektoepassingen meestal meer replica's nodig dan partities, vooral wanneer de servicebewerkingen zijn bevooroordeeld ten opzichte van queryworkloads. De sectie over [hoge beschikbaarheid](#HA) legt uit waarom.

> [!NOTE]
> Het toevoegen van meer replica's of partities verhoogt de kosten van het uitvoeren van de service en kan kleine variaties introduceren in de manier waarop resultaten worden geordend. Controleer de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om inzicht te krijgen in de factureringsimplicaties van het toevoegen van meer knooppunten. De [onderstaande grafiek](#chart) kan u helpen bij het vergelijken van het aantal zoekeenheden dat nodig is voor een specifieke configuratie. Zie [Resultaten bestellen](search-pagination-page-layout.md#ordering-results)voor meer informatie over de invloed van extra replica's op queryverwerking.

## <a name="how-to-allocate-replicas-and-partitions"></a>Replica's en partities toewijzen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) en selecteer de zoekservice.

1. Open **in Instellingen**de pagina **Schaal** om replica's en partities te wijzigen. 

   De volgende schermafbeelding toont een standaardservice die is ingericht met één replica en partitie. De formule onderaan geeft aan hoeveel zoekeenheden worden gebruikt (1). Als de prijs per eenheid was $ 100 (niet een echte prijs), de maandelijkse kosten van het uitvoeren van deze dienst zou worden $ 100 gemiddeld.

   ![Schaalpagina met huidige waarden](media/search-capacity-planning/1-initial-values.png "Schaalpagina met huidige waarden")

1. Gebruik de schuifregelaar om het aantal partities te verhogen of te verkleinen. De formule onderaan geeft aan hoeveel zoekeenheden worden gebruikt.

   In dit voorbeeld wordt de capaciteit verdubbeld, met elk twee replica's en partities. Let op het aantal zoekeenheden; het is nu vier omdat de factureringsformule replica's is vermenigvuldigd met partities (2 x 2). Verdubbeling van de capaciteit meer dan verdubbelt de kosten van het uitvoeren van de dienst. Als de kosten van de zoekeenheid $ 100 waren, zou de nieuwe maandelijkse factuur nu $ 400 zijn.

   Ga voor de huidige kosten per eenheid van elke laag naar de [prijspagina](https://azure.microsoft.com/pricing/details/search/).

   ![Replica's en partities toevoegen](media/search-capacity-planning/2-add-2-each.png "Replica's en partities toevoegen")

1. Klik **op Opslaan** om de wijzigingen te bevestigen.

   ![Wijzigingen in schaal en facturering bevestigen](media/search-capacity-planning/3-save-confirm.png "Wijzigingen in schaal en facturering bevestigen")

   Het duurt enkele uren voordat capaciteitswijzigingen zijn doorgevoerd. U niet annuleren zodra het proces is gestart en er geen realtime bewaking is voor replica- en partitieaanpassingen. Het volgende bericht blijft echter zichtbaar terwijl er wijzigingen aan de gang zijn.

   ![Statusbericht in de portal](media/search-capacity-planning/4-updating.png "Statusbericht in de portal")

> [!NOTE]
> Nadat een service is ingericht, kan deze niet worden geüpgraded naar een hogere laag. U moet een zoekservice maken op de nieuwe laag en uw indexen opnieuw laden. Zie [Een Azure Cognitive Search-service maken in de portal](search-create-service-portal.md) voor hulp bij het inrichten van services.
>
> Bovendien worden partities en replica's uitsluitend en intern beheerd door de service. Er is geen concept van processoraffiniteit of het toewijzen van een werkbelasting aan een specifiek knooppunt.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Partitie- en replicacombinaties

Een Basic-service kan precies één partitie en maximaal drie replica's hebben, voor een maximumvan drie SU's. De enige instelbare bron is replica's. U hebt minimaal twee replica's nodig voor hoge beschikbaarheid op query's.

Alle standaard- en opslaggeoptimaliseerde zoekservices kunnen de volgende combinaties van replica's en partities aannemen, onder voorbehoud van de limiet van 36 SU. 

|   | **1 partitie** | **2 partities** | **3 partities** | **4 partities** | **6 partities** | **12 partities** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replica** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 replica's** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 replica's** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 replica's** |4 SU |8 SU |12 SU |16 SU |24 SU |N.v.t. |
| **5 replica's** |5 SU |10 SU |15 SU |20 SU |30 SU |N.v.t. |
| **6 replica's** |6 SU |12 SU |18 SU |24 SU |36 SU |N.v.t. |
| **12 replica's** |12 SU |24 SU |36 SU |N.v.t. |N.v.t. |N.v.t. |

SU's, prijzen en capaciteit worden in detail uitgelegd op de Azure-website. Zie [Prijsdetails](https://azure.microsoft.com/pricing/details/search/)voor meer informatie .

> [!NOTE]
> Het aantal replica's en partities verdeelt gelijkmatig in 12 (specifiek, 1, 2, 3, 4, 6, 12). Dit komt omdat Azure Cognitive Search elke index vooraf verdeelt in 12 shards, zodat deze in gelijke delen over alle partities kan worden verspreid. Als uw service bijvoorbeeld drie partities heeft en u een index maakt, bevat elke partitie vier shards van de index. Hoe Azure Cognitive Search een index shardt, is een implementatiedetail, onder voorbehoud van wijzigingen in toekomstige releases. Hoewel het aantal is 12 vandaag, moet je niet verwachten dat dat aantal altijd 12 in de toekomst.
>

<a id="HA"></a>

## <a name="high-availability"></a>Hoge beschikbaarheid

Omdat het eenvoudig en relatief snel is om op te schalen, raden we u over het algemeen aan om te beginnen met één partitie en een of twee replica's en vervolgens op te schalen naar queryvolumes. Queryworkloads worden voornamelijk uitgevoerd op replica's. Als u meer doorvoer of hoge beschikbaarheid nodig hebt, hebt u waarschijnlijk extra replica's nodig.

Algemene aanbevelingen voor hoge beschikbaarheid zijn:

* Twee replica's voor een hoge beschikbaarheid van alleen-lezen workloads (query's)

* Drie of meer replica's voor hoge beschikbaarheid van lees-/schrijfworkloads (query's plus indexering als afzonderlijke documenten worden toegevoegd, bijgewerkt of verwijderd)

Servicelevelagreements (SLA) voor Azure Cognitive Search zijn gericht op querybewerkingen en op indexupdates die bestaan uit het toevoegen, bijwerken of verwijderen van documenten.

Basic tier tops uit op een partitie en drie replica's. Als u de flexibiliteit wilt hebben om onmiddellijk te reageren op schommelingen in de vraag naar zowel indexering als querydoorvoer, moet u een van de standaardlagen overwegen.  Als u merkt dat uw opslagvereisten veel sneller toenemen dan uw querydoorvoer, u een van de niveaus voor geoptimaliseerde opslag overwegen.

## <a name="disaster-recovery"></a>Herstel na noodgeval

Momenteel is er geen ingebouwd mechanisme voor noodherstel. Het toevoegen van partities of replica's zou de verkeerde strategie zijn voor het behalen van disaster recovery-doelstellingen. De meest voorkomende aanpak is het toevoegen van redundantie op serviceniveau door het opzetten van een tweede zoekservice in een andere regio. Net als bij de beschikbaarheid tijdens een index opnieuw opbouwen, moet de omleiding of failover logica afkomstig zijn van uw code.

## <a name="estimate-replicas"></a>Replica's schatten

Bij een productieservice moet u drie replica's toewijzen voor SLA-doeleinden. Als u trage queryprestaties ondervindt, u replica's toevoegen zodat extra kopieën van de index online worden gebracht om grotere queryworkloads te ondersteunen en de aanvragen over de meerdere replica's te laden.

We geven geen richtlijnen voor het aantal replica's dat nodig is om querybelastingen aan te kunnen. Queryprestaties zijn afhankelijk van de complexiteit van de query en concurrerende workloads. Hoewel het toevoegen van replica's duidelijk resulteert in betere prestaties, is het resultaat niet strikt lineair: het toevoegen van drie replica's garandeert geen drievoudige doorvoer.

Zie [Schalen voor prestaties](search-performance-optimization.md)en [Monitorquery's](search-monitor-queries.md) voor richtlijnen bij het schatten van QPS voor uw oplossing

## <a name="estimate-partitions"></a>Partities schatten

De [door u gekozen laag](search-sku-tier.md) bepaalt de grootte en snelheid van de partitie en elke laag is geoptimaliseerd rond een reeks kenmerken die in verschillende scenario's passen. Als u een hogere laag kiest, hebt u mogelijk minder partities nodig dan wanneer u voor S1 gaat. Een van de vragen die u moet beantwoorden door middel van self-directed testen is of een grotere en duurdere partitie levert betere prestaties dan twee goedkopere partities op een dienst ingericht op een lagere laag.

Zoektoepassingen waarvoor bijna realtime gegevens moeten worden vernieuwd, hebben verhoudingsgewijs meer partities nodig dan replica's. Het toevoegen van partities verspreidt lees-/schrijfbewerkingen over een groter aantal compute resources. Het geeft u ook meer schijfruimte voor het opslaan van extra indexen en documenten.

Grotere indexen duren langer om te vragen. Als zodanig u merken dat elke incrementele toename van partities een kleinere maar evenredige toename van replica's vereist. De complexiteit van uw query's en queryvolume houdt rekening met hoe snel query-uitvoering wordt omgedraaid.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een prijscategorie kiezen voor Azure Cognitive Search](search-sku-tier.md)