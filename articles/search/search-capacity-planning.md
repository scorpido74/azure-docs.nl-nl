---
title: Partities en replica's schalen om capaciteit toe te voegen voor de werk belasting van query's en indexen
titleSuffix: Azure Cognitive Search
description: Pas de resources van de partitie en de replica computer aan in azure Cognitive Search, waarbij elke resource wordt geprijsd in factureer bare Zoek eenheden.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8613ddc668df338c4f96a9d37f32120718513925
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792508"
---
# <a name="scale-up-partitions-and-replicas-to-add-capacity-for-query-and-index-workloads-in-azure-cognitive-search"></a>Partities en replica's schalen om capaciteit toe te voegen voor de werk belasting van query's en indexen in azure Cognitive Search

Nadat u [een prijs categorie hebt gekozen](search-sku-tier.md) en [een zoek service hebt ingericht](search-create-service-portal.md), is de volgende stap het verhogen van het aantal replica's of partities dat door uw service wordt gebruikt. Elke laag biedt een vast aantal facturerings eenheden. In dit artikel wordt uitgelegd hoe u deze eenheden toewijst voor een optimale configuratie waarmee u de vereisten voor het uitvoeren van query's, het indexeren en de opslag kunt verdelen.

Resource configuratie is beschikbaar wanneer u een service instelt op de [basis-laag](https://aka.ms/azuresearchbasic) of een van de [standaard-of opslag geoptimaliseerde lagen](search-limits-quotas-capacity.md). Voor services in deze lagen wordt de capaciteit gekocht in stappen van *Zoek eenheden* (SUs) waarbij elke partitie en replica als één su worden geteld. 

Als u minder SUs-resultaten gebruikt, wordt er een proportionele lagere factuur in rekening gebracht. De facturering is van kracht zolang de service is ingesteld. Als u tijdelijk geen service gebruikt, is de enige manier om facturering te voor komen door de service te verwijderen en deze vervolgens opnieuw te maken wanneer u deze nodig hebt.

> [!Note]
> Als u een service verwijdert, wordt alles verwijderd. Er is geen faciliteit in azure Cognitive Search voor het maken van back-ups en het herstellen van blijvende Zoek gegevens. Als u een bestaande index voor een nieuwe service opnieuw wilt implementeren, moet u het programma uitvoeren dat is gebruikt om het oorspronkelijk te maken en te laden. 

## <a name="terminology-replicas-and-partitions"></a>Terminologie: replica's en partities
Replica's en partities zijn de primaire resources die back-ups maken van een zoek service.

| Bron | Definitie |
|----------|------------|
|*Partities* | Biedt index opslag en I/O voor lees-en schrijf bewerkingen (bijvoorbeeld bij het opnieuw samen stellen of vernieuwen van een index).|
|*Replica's* | Exemplaren van de zoek service, worden voornamelijk gebruikt voor taak verdeling van query bewerkingen. Elke replica fungeert altijd als host voor één exemplaar van een index. Als u 12 replica's hebt, hebt u 12 kopieën van elke index die in de service is geladen.|

> [!NOTE]
> Het is niet mogelijk om rechtstreeks te manipuleren of te beheren welke indexen worden uitgevoerd op een replica. Eén exemplaar van elke index op elke replica maakt deel uit van de service architectuur.
>


## <a name="how-to-allocate-replicas-and-partitions"></a>Replica's en partities toewijzen
In azure Cognitive Search wordt in eerste instantie een mini maal niveau aan resources toegewezen dat bestaat uit één partitie en één replica. Bij lagen die ondersteuning bieden, kunt u de reken resources incrementeel aanpassen door partities te verhogen als u meer opslag en I/O nodig hebt, of als u meer replica's voor grotere query volumes of betere prestaties wilt toevoegen. Eén service moet voldoende resources hebben om alle werk belastingen (indexering en query's) af te handelen. U kunt geen werk lasten onderverdelen over meerdere services.

Als u de toewijzing van replica's en partities wilt verg Roten of wijzigen, kunt u het beste de Azure Portal gebruiken. De portal dwingt limieten af voor toegestane combi naties die onder maximum limieten blijven. Als u een op scripts of code gebaseerde inrichtings benadering nodig hebt, zijn de [Azure PowerShell](search-manage-powershell.md) of het [beheer rest API](https://docs.microsoft.com/rest/api/searchmanagement/services) alternatieve oplossingen.

Over het algemeen hebben Zoek toepassingen meer replica's nodig dan partities, met name wanneer de service bewerkingen worden uitgevoerd op query werkbelastingen. In het gedeelte over [hoge Beschik baarheid](#HA) wordt uitgelegd waarom.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) en selecteer de zoek service.

2. Open in **instellingen**de pagina **schalen** om replica's en partities te wijzigen. 

   De volgende scherm afbeelding toont een standaard service die is ingericht met één replica en partitie. De formule aan de onderkant geeft aan hoeveel Zoek eenheden er worden gebruikt (1). Als de eenheids prijs $100 is (geen echte prijs), is de maandelijkse kosten van het uitvoeren van deze service gemiddeld $100.

   ![Pagina schalen met huidige waarden](media/search-capacity-planning/1-initial-values.png "Pagina schalen met huidige waarden")

3. Gebruik de schuif regelaar om het aantal partities te verhogen of te verlagen. De formule aan de onderkant geeft aan hoeveel Zoek eenheden er worden gebruikt.

   In dit voor beeld wordt de capaciteit met twee replica's en partities verdubbeld. Let op het aantal Zoek eenheden; het is nu vier omdat de facturerings formule replica's is vermenigvuldigd met partities (2 x 2). Als u de capaciteit verdubbelet, verdubbelt u de kosten van het uitvoeren van de service. Als de kost prijs van de zoek eenheid $100 was, zou de nieuwe maandelijkse factuur nu $400 zijn.

   Voor de huidige kosten per eenheid van elke laag gaat u naar de [pagina met prijzen](https://azure.microsoft.com/pricing/details/search/).

   ![Replica's en partities toevoegen](media/search-capacity-planning/2-add-2-each.png "Replica's en partities toevoegen")

3. Klik op **Opslaan** om de wijzigingen te bevestigen.

   ![Wijzigingen voor schalen en facturering bevestigen](media/search-capacity-planning/3-save-confirm.png "Wijzigingen voor schalen en facturering bevestigen")

   De wijzigingen in de capaciteit nemen enkele uren in beslag. U kunt niet annuleren nadat het proces is gestart en er is geen realtime-bewaking voor replica-en partitie aanpassingen. Het volgende bericht blijft echter zichtbaar wanneer er wijzigingen worden aangebracht.

   ![Status bericht in de portal](media/search-capacity-planning/4-updating.png "Status bericht in de portal")


> [!NOTE]
> Nadat een service is ingericht, kan deze niet meer worden bijgewerkt naar een hogere SKU. U moet een zoek service op de nieuwe laag maken en uw indexen opnieuw laden. Zie [een Azure Cognitive Search-service maken in de portal](search-create-service-portal.md) voor hulp bij het inrichten van services.
>
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Combi Naties van partities en replica's

Een Basic-service kan precies één partitie hebben en Maxi maal drie replica's, voor een maximum limiet van drie SUs. De enige aanpas bare resource is replica's. U hebt mini maal twee replica's nodig voor hoge Beschik baarheid van query's.

Alle standaard-en opslag geoptimaliseerde zoek services kunnen de volgende combi Naties van replica's en partities aannemen, afhankelijk van de limiet van 36. 

|   | **1 partitie** | **2 partities** | **3 partities** | **4 partities** | **6 partities** | **12 partities** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replica** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 replica's** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 replica's** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 replica's** |4 SU |8 SU |12 SU |16 SU |24 SU |N/A |
| **5 replica's** |5 SU |10 SU |15 SU |20 SU |30 SU |N/A |
| **6 replica's** |6 SU |12 SU |18 SU |24 SU |36 SU |N/A |
| **12 replica's** |12 SU |24 SU |36 SU |N/A |N/A |N/A |

SUs, prijzen en capaciteit worden gedetailleerd beschreven op de Azure-website. Zie [prijs informatie](https://azure.microsoft.com/pricing/details/search/)voor meer informatie.

> [!NOTE]
> Het aantal replica's en partities worden gelijkmatig verdeeld in 12 (met name 1, 2, 3, 4, 6, 12). Dit komt doordat Azure Cognitive Search elke index vooraf opsplitst in 12 Shards, zodat deze in gelijke delen kan worden verdeeld over alle partities. Als uw service bijvoorbeeld drie partities heeft en u een index maakt, dan bevat elke partitie vier Shards van de index. Hoe Azure Cognitive Search Shards een index is een implementatie detail, die kan worden gewijzigd in toekomstige releases. Hoewel het nummer 12 vandaag is, mag u niet verwachten dat aantal in de toekomst altijd 12 is.
>


<a id="HA"></a>

## <a name="high-availability"></a>Hoge beschikbaarheid
Omdat het eenvoudig en relatief snel omhoog kan worden geschaald, raden we u aan om te beginnen met één partitie en een of twee replica's, en vervolgens omhoog te schalen als query volumes bouwen. Query werkbelastingen worden voornamelijk uitgevoerd op replica's. Als u meer door Voer of hoge Beschik baarheid nodig hebt, hebt u waarschijnlijk extra replica's nodig.

Algemene aanbevelingen voor hoge Beschik baarheid zijn:

* Twee replica's voor hoge Beschik baarheid van alleen-lezen workloads (query's)

* Drie of meer replica's voor hoge Beschik baarheid van werk belastingen voor lezen/schrijven (query's plus indexering als afzonderlijke documenten worden toegevoegd, bijgewerkt of verwijderd)

Service Level Agreements (SLA) voor Azure Cognitive Search zijn gericht op query bewerkingen en bij index updates die bestaan uit het toevoegen, bijwerken of verwijderen van documenten.

Basic-laag oplopend op één partitie en drie replica's. Als u wilt dat de flexibiliteit onmiddellijk reageert op schommelingen in de vraag naar het indexeren en door Voer van query's, overweeg dan een van de standaard lagen.  Als u vindt dat uw opslag vereisten veel sneller groeien dan de door Voer van query's, overweeg dan een van de geoptimaliseerde opslag lagen.

### <a name="index-availability-during-a-rebuild"></a>Index beschikbaarheid tijdens het opnieuw opbouwen

Hoge Beschik baarheid voor Azure Cognitive Search is van toepassing op query's en index updates die geen reconstructie van een index vereisen. Als u een veld verwijdert, een gegevens type wijzigt of de naam van een veld wijzigt, moet u de index opnieuw samen stellen. Als u de index opnieuw wilt samen stellen, moet u de index verwijderen, de index opnieuw maken en de gegevens opnieuw laden.

> [!NOTE]
> U kunt nieuwe velden toevoegen aan een Azure Cognitive Search-index zonder de index opnieuw samen te stellen. De waarde van het nieuwe veld is null voor alle documenten die al in de index staan.

Als u de index beschikbaarheid tijdens het opnieuw opbouwen wilt behouden, moet u een kopie van de index met een andere naam op dezelfde service hebben, of een kopie van de index met dezelfde naam op een andere service, en vervolgens omleidings-of failover-logica in uw code opgeven.

## <a name="disaster-recovery"></a>Herstel na noodgeval
Er is momenteel geen ingebouwd mechanisme voor herstel na nood gevallen. Het toevoegen van partities of replica's zou de verkeerde strategie zijn voor het bereiken van herstel na nood gevallen. De meest voorkomende benadering is het toevoegen van redundantie op service niveau door een tweede zoek service in een andere regio in te stellen. Net als bij het opnieuw opbouwen van een index, moet de omleiding of failover-logica afkomstig zijn van uw code.

## <a name="increase-query-performance-with-replicas"></a>Verbeter de query prestaties met replica's
Latentie van query's is een indicatie dat er extra replica's nodig zijn. Over het algemeen is het beter om de query prestaties te verbeteren door meer van deze resource toe te voegen. Bij het toevoegen van replica's worden extra kopieën van de index online gebracht ter ondersteuning van grotere query werkbelastingen en om de aanvragen te verdelen over de verschillende replica's.

Er kunnen geen harde schattingen worden geboden voor query's per seconde (QPS): query prestaties zijn afhankelijk van de complexiteit van de query en concurrerende werk belastingen. Hoewel het toevoegen van replica's duidelijk resulteert in betere prestaties, is het resultaat niet strikt lineair: het toevoegen van drie replica's biedt geen garantie voor triple-door voer.

Zie [overwegingen voor Azure Cognitive Search prestaties en optimalisatie](search-performance-optimization.md)voor hulp bij het schatten van qps voor uw workloads.

## <a name="increase-indexing-performance-with-partitions"></a>Indexerings prestaties verhogen met partities
Voor het zoeken naar toepassingen waarvoor bijna realtime gegevens moeten worden vernieuwd, moeten proportioneel meer partities dan replica's nodig zijn. Door partities toe te voegen worden lees-en schrijf bewerkingen verdeeld over een groter aantal reken resources. Daarnaast hebt u meer schijf ruimte voor het opslaan van aanvullende indexen en documenten.

Het duurt langer voor grotere indexen om query's uit te voeren. Als zodanig is het mogelijk dat elke incrementele toename in partities een kleinere, maar proportionele toename van replica's vereist. De complexiteit van uw query's en query volume wordt gemeten in hoe snel query's kunnen worden uitgevoerd.


## <a name="next-steps"></a>Volgende stappen

[Een prijs categorie kiezen voor Azure Cognitive Search](search-sku-tier.md)
