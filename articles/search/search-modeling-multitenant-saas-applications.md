---
title: Meerdere pacht modellen voor inhouds isolatie in één service-Azure Search
description: Meer informatie over veelgebruikte ontwerp patronen voor SaaS-toepassingen met meerdere tenants tijdens het gebruik van Azure Search.
manager: jlembicz
author: LiamCavanagh
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: liamca
ms.openlocfilehash: b3e47fc0c46c638a51e6555ccbdc1885f081c149
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640549"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Ontwerp patronen voor SaaS-toepassingen met meerdere tenants en Azure Search
Een multi tenant-toepassing biedt dezelfde services en mogelijkheden voor elk aantal tenants dat de gegevens van een andere Tenant niet kan zien of delen. In dit document worden de isolatie strategieën voor tenants besproken voor multi tenant-toepassingen die zijn gebouwd met Azure Search.

## <a name="azure-search-concepts"></a>Azure Search concepten
Als een Search-as-a-service-oplossing kunt u met Azure Search ontwikkel aars uitgebreidere Zoek ervaringen toevoegen aan toepassingen zonder dat ze een infra structuur hoeven te beheren of een expert te zijn in het ophalen van informatie. Gegevens worden naar de service geüpload en vervolgens opgeslagen in de Cloud. Door eenvoudige aanvragen te gebruiken voor de Azure Search-API, kunnen de gegevens worden gewijzigd en doorzocht. In [dit artikel](https://aka.ms/whatisazsearch)vindt u een overzicht van de service. Voordat u ontwerp patronen bespreekt, is het belang rijk dat u enkele concepten in Azure Search begrijpt.

### <a name="search-services-indexes-fields-and-documents"></a>Services, indexen, velden en documenten doorzoeken
Wanneer u Azure Search gebruikt, wordt er één geabonneerd op een *Zoek service*. Wanneer gegevens worden geüpload naar Azure Search, wordt deze opgeslagen in een *index* binnen de zoek service. Er kan een aantal indexen binnen één service zijn. Als u de vertrouwde concepten van data bases wilt gebruiken, kan de zoek service worden likened naar een Data Base terwijl de indexen binnen een service kunnen worden likened aan tabellen in een Data Base.

Elke index binnen een zoek service heeft een eigen schema, dat wordt gedefinieerd door een aantal aanpas bare *velden*. Gegevens worden toegevoegd aan een Azure Search index in de vorm van afzonderlijke *documenten*. Elk document moet worden geüpload naar een bepaalde index en moet overeenkomen met het schema van de index. Bij het zoeken naar gegevens met behulp van Azure Search, worden de Zoek opdrachten in volledige tekst op basis van een bepaalde index uitgegeven.  Om deze concepten te vergelijken met die van een Data Base, kunnen velden worden likened aan kolommen in een tabel en kunnen de documenten worden likened naar rijen.

### <a name="scalability"></a>Schaalbaarheid
Elke Azure Search-service in de [prijs categorie](https://azure.microsoft.com/pricing/details/search/) Standard kan worden geschaald in twee dimensies: opslag en beschik baarheid.

* Er kunnen *partities* worden toegevoegd om de opslag van een zoek service te verg Roten.
* *Replica's* kunnen worden toegevoegd aan een service om de door voer te verg Roten van aanvragen die door een zoek service kunnen worden verwerkt.

Door partities en replica's toe te voegen en te verwijderen, kan de capaciteit van de zoek service groeien met de hoeveelheid gegevens en het verkeer dat de toepassing vereist. Om een zoek service te kunnen bereiken voor een Lees- [Sla](https://azure.microsoft.com/support/legal/sla/search/v1_0/), zijn er twee replica's nodig. Om een service een [Sla](https://azure.microsoft.com/support/legal/sla/search/v1_0/)voor lezen/schrijven te kunnen krijgen, zijn er drie replica's nodig.

### <a name="service-and-index-limits-in-azure-search"></a>Service-en index limieten in Azure Search
Er zijn een aantal verschillende [prijs categorieën](https://azure.microsoft.com/pricing/details/search/) in azure Search. elk van de lagen heeft verschillende limieten [en quota's](search-limits-quotas-capacity.md). Sommige van deze limieten bevinden zich op het niveau van de service, sommige zijn op het niveau van de index en sommige zijn op partitie niveau.

|  | Basic | Standard1 | Standard2 | Standard3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| Maximum aantal Replica's per service |3 |12 |12 |12 |12 |
| Maximum aantal partities per service |1 |12 |12 |12 |3 |
| Maximum aantal Zoek eenheden (Replica's * partities) per service |3 |36 |36 |36 |36 (Maxi maal 3 partities) |
| Maximale opslag per service |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| Maximale opslag per partitie |2 GB |25 GB |100 GB |200 GB |200 GB |
| Maximum aantal indexen per service |5 |50 |200 |200 |3000 (max. 1000 indexen/partitie) |

#### <a name="s3-high-density"></a>S3-hoge dichtheid
In de prijs categorie S3 van Azure Search is er een optie voor de HD-modus (High density) die specifiek is ontworpen voor multi tenant scenario's. In veel gevallen is het nood zakelijk om een groot aantal kleinere tenants te ondersteunen onder één service om de voor delen van eenvoud en kosten efficiëntie te verhalen.

Met S3 HD kunnen de vele kleine indexen worden ingepakt onder het beheer van één zoek service door de mogelijkheid te bieden om indexen te schalen met behulp van partities voor de mogelijkheid om meer indexen in één service te hosten.

Een S3-service kan in concrete tussen 1 en 200 indexen bevatten die samen een tot 1.400.000.000 documenten kunnen hosten. Met een S3 HD daarentegen kunnen afzonderlijke indexen slechts tot 1.000.000 documenten gaan, maar kan er Maxi maal 1000 indexen per partitie worden verwerkt (Maxi maal 3000 per service) met een totaal aantal 200.000.000 documenten per partitie (Maxi maal 600.000.000 per service).

## <a name="considerations-for-multitenant-applications"></a>Overwegingen voor multi tenant-toepassingen
Multi tenant-toepassingen moeten bronnen efficiënt verdelen over de tenants met behoud van wat privacy niveau tussen de verschillende tenants. Er zijn enkele overwegingen bij het ontwerpen van de architectuur voor een dergelijke toepassing:

* *Tenant isolatie:* Toepassings ontwikkelaars moeten passende maat regelen nemen om ervoor te zorgen dat tenants geen ongeoorloofde of ongewenste toegang hebben tot de gegevens van andere tenants. Buiten het perspectief van de privacy van gegevens vereisen de isolatie strategieën voor tenants een effectief beheer van gedeelde bronnen en bescherming tegen lawaai van de ruis.
* *Kosten van Cloud resource:* Net als bij elke andere toepassing moeten software oplossingen kosten concurrerend zijn als onderdeel van een multi tenant-toepassing.
* *Gemakkelijke bewerkingen:* Bij het ontwikkelen van een architectuur met meerdere tenants is de invloed op de bewerkingen en complexiteit van de toepassing een belang rijke overweging. Azure Search heeft een [Sla van 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* *Algemene footprint:* Multi tenant-toepassingen moeten mogelijk doel treffend zijn voor tenants die over de hele wereld worden gedistribueerd.
* *Schaalbaarheid:* Ontwikkel aars van toepassingen moeten overwegen hoe ze zich afstemmen tussen het onderhouden van een voldoende laag niveau van toepassings complexiteit en het ontwerpen van de toepassing om te schalen met het aantal tenants en de grootte van de gegevens en workload van de tenants.

Azure Search biedt een aantal grenzen die kunnen worden gebruikt voor het isoleren van gegevens en werk belasting van tenants.

## <a name="modeling-multitenancy-with-azure-search"></a>Multitenancy met Azure Search model leren
In het geval van een multi tenant scenario gebruikt de ontwikkelaar van de toepassing een of meer zoek services en wordt de tenants verdeeld over services, indices of beide. Azure Search heeft enkele algemene patronen bij het model leren van een scenario met meerdere tenants:

1. *Index per Tenant:* Elke Tenant heeft een eigen index binnen een zoek service die wordt gedeeld met andere tenants.
2. *Service per Tenant:* Elke Tenant heeft een eigen toegewezen Azure Search service, die het hoogste niveau van gegevens en workload-schei ding biedt.
3. *Combi natie van:* Grotere, meer actieve tenants krijgen toegewezen services, terwijl bij kleinere tenants afzonderlijke indexen binnen gedeelde services worden toegewezen.

## <a name="1-index-per-tenant"></a>1. Index per Tenant
![Een portrayal van het model van de index-per-Tenant](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

Meerdere tenants in een model voor index-per-Tenant bevinden zich in één Azure Search-service waarbij elke Tenant een eigen index heeft.

Tenants bereiken gegevens isolatie, omdat alle Zoek opdrachten en document bewerkingen worden uitgegeven op index niveau in Azure Search. In de toepassingslaag moet u zich bewust zijn van het directe verkeer van de verschillende tenants naar de juiste indexen, terwijl u ook resources op service niveau voor alle tenants beheert.

Een sleutel kenmerk van het model van de index-per-Tenant is de mogelijkheid van de ontwikkelaar van de toepassing om de capaciteit van een zoek service over te nemen op basis van de tenants van de toepassing. Als de tenants een ongelijke verdeling van de werk belasting hebben, kan de optimale combi natie van tenants worden gedistribueerd over de indices van een zoek service voor een aantal uiterst actieve, tijdrovende tenants en tegelijkertijd een lange staart van minder actieve tenants. De afweging is het onvermogen van het model voor het afhandelen van situaties waarbij elke Tenant gelijktijdig Maxi maal actief is.

Het model van de index-per-Tenant vormt de basis voor een variabel kosten model, waarbij een hele Azure Search service vooraf wordt aangeschaft en vervolgens wordt gevuld met tenants. Hierdoor kan ongebruikte capaciteit worden aangegeven voor experimenten en gratis accounts.

Voor toepassingen met een algemene footprint is het model van de index-per-Tenant mogelijk niet het meest efficiënt. Als de tenants van een toepassing over de hele wereld worden gedistribueerd, kan er een afzonderlijke service nodig zijn voor elke regio, waardoor de kosten voor elk van de verschillende regio's kunnen worden gedupliceerd.

Azure Search kunt de schaal van zowel de afzonderlijke indexen als het totale aantal indexen dat moet worden uitgebreid. Als er een geschikte prijs categorie wordt gekozen, kunnen partities en replica's worden toegevoegd aan de gehele zoek service wanneer een afzonderlijke index binnen de service te groot is voor wat betreft opslag of verkeer.

Als het totale aantal indexen te groot is voor één service, moet een andere service worden ingericht voor de nieuwe tenants. Als indexen moeten worden verplaatst tussen Zoek Services terwijl nieuwe services worden toegevoegd, moeten de gegevens uit de index hand matig worden gekopieerd van de ene index naar de andere omdat Azure Search geen index toestaat.

## <a name="2-service-per-tenant"></a>2. Service per Tenant
![Een portrayal van het service-per-Tenant model](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

In een architectuur met een service-per-Tenant heeft elke Tenant een eigen zoek service.

In dit model bereikt de toepassing het maximale isolatie niveau voor de tenants. Elke service heeft specifieke opslag en door Voer voor het verwerken van een zoek opdracht en afzonderlijke API-sleutels.

Voor toepassingen waarbij elke Tenant een grote footprint heeft of als de werk belasting weinig variabiliteit heeft van Tenant naar Tenant, is het model van de service-per-Tenant een efficiënte keuze omdat resources niet worden gedeeld tussen de werk belastingen van verschillende tenants.

Een service per Tenant model biedt ook het voor deel van een voorspelbaar, vast kosten model. Er is geen investering vooraf in een volledige zoek service, totdat er een Tenant is om deze in te vullen. de kosten per Tenant zijn echter hoger dan een model per Tenant.

Het model service-per-Tenant is een efficiënte keuze voor toepassingen met een algemene footprint. Met geografisch gedistribueerde tenants is het eenvoudig om elke Tenant service in de juiste regio te hebben.

De uitdagingen bij het schalen van dit patroon ontstaan als afzonderlijke tenants hun service uitgroeien. Azure Search biedt momenteel geen ondersteuning voor de upgrade van de prijs categorie van een zoek service, zodat alle gegevens hand matig moeten worden gekopieerd naar een nieuwe service.

## <a name="3-mixing-both-models"></a>3. Beide modellen combi neren
Een ander patroon voor het model leren van multitenancy is het combi neren van zowel de strategie per Tenant als voor de service per Tenant.

Door de twee patronen te mengen, kunnen de grootste tenants van een toepassing speciale services innemen, terwijl de lange staart van minder actieve, kleinere tenants in een gedeelde service indexen kan innemen. Dit model zorgt ervoor dat de grootste tenants voortdurend hoge prestaties van de service hebben terwijl u de kleinere tenants van alle onrustige neighbors kunt beveiligen.

Het implementeren van deze strategie is echter afhankelijk van de prognose bij het voors pellen van de tenants die een specifieke service vereisen tegenover een index in een gedeelde service. Toepassings complexiteit neemt toe met de nood zaak om beide multitenancys modellen te beheren.

## <a name="achieving-even-finer-granularity"></a>Nauw keurigere granulariteit bereiken
De bovenstaande ontwerp patronen voor het model leren van scenario's met meerdere tenants in Azure Search uitgaan van een uniform bereik waarbij elke Tenant een geheel exemplaar van een toepassing is. Toepassingen kunnen soms veel kleinere bereiken verwerken.

Als service-per-Tenant-en index-per-Tenant modellen niet voldoende kleine bereiken zijn, is het mogelijk om een index te model leren om een nog nauw keurigere mate van granulatie te bereiken.

Als u een enkele index anders wilt gebruiken voor verschillende client eindpunten, kan een veld worden toegevoegd aan een index die een bepaalde waarde voor elke mogelijke client aanduidt. Telkens wanneer een client Azure Search aanroept om een index op te vragen of te wijzigen, geeft de code van de client toepassing de juiste waarde voor dat veld aan met behulp van de [filter](https://msdn.microsoft.com/library/azure/dn798921.aspx) mogelijkheid van Azure Search op de query tijd.

Deze methode kan worden gebruikt voor het bezorgen van de functionaliteit van afzonderlijke gebruikers accounts, afzonderlijke machtigings niveaus en zelfs volledig gescheiden toepassingen.

> [!NOTE]
> Met behulp van de hierboven beschreven methode kunt u een enkele index configureren voor meerdere tenants die van invloed is op de relevantie van de zoek resultaten. Zoek relevantie scores worden berekend op het bereik op index niveau, niet op Tenant niveau, zodat alle gegevens van de tenants worden opgenomen in de onderliggende statistieken van de relevantie scores zoals term frequentie.
> 
> 

## <a name="next-steps"></a>Volgende stappen
Azure Search is een fascinerende keuze voor veel toepassingen, [Lees meer over de krachtige mogelijkheden van de service](https://aka.ms/whatisazsearch). Bij het evalueren van de verschillende ontwerp patronen voor multi tenant-toepassingen, moet u rekening houden met de [verschillende prijs categorieën](https://azure.microsoft.com/pricing/details/search/) en de bijbehorende [service limieten](search-limits-quotas-capacity.md) om Azure Search te passen op werk belastingen van toepassingen en architecturen van elke omvang.

Vragen over Azure Search en multi tenant scenario's kunnen worden omgeleid naar azuresearch_contact@microsoft.com.

