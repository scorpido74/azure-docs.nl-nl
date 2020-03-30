---
title: Multitenancy en inhoudsisolatie
titleSuffix: Azure Cognitive Search
description: Meer informatie over algemene ontwerppatronen voor SaaS-toepassingen met meerdere tenants tijdens het gebruik van Azure Cognitive Search.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d37abd1b5d212c3d920cb68b6236029b2112ae24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113273"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-cognitive-search"></a>Ontwerppatronen voor Multitenant SaaS-toepassingen en Azure Cognitive Search
Een multitenant-toepassing is een toepassing die dezelfde services en mogelijkheden biedt aan een willekeurig aantal tenants die de gegevens van een andere tenant niet kunnen zien of delen. In dit document worden tenantisolatiestrategieën voor multitenant-toepassingen besproken die zijn gebouwd met Azure Cognitive Search.

## <a name="azure-cognitive-search-concepts"></a>Azure Cognitive Search-concepten
Als zoek-as-a-service-oplossing stelt Azure Cognitive Search ontwikkelaars in staat om uitgebreide zoekervaringen toe te voegen aan toepassingen zonder infrastructuur te beheren of een expert te worden in het ophalen van informatie. Gegevens worden geüpload naar de service en vervolgens opgeslagen in de cloud. Met behulp van eenvoudige aanvragen voor de Azure Cognitive Search API kunnen de gegevens vervolgens worden gewijzigd en doorzocht. Een overzicht van de dienst is te vinden in [dit artikel](https://aka.ms/whatisazsearch). Voordat u ontwerppatronen bespreekt, is het belangrijk om bepaalde concepten in Azure Cognitive Search te begrijpen.

### <a name="search-services-indexes-fields-and-documents"></a>Zoekservices, indexen, velden en documenten
Bij het gebruik van Azure Cognitive Search wordt één geabonneerd op een *zoekservice.* Als gegevens worden geüpload naar Azure Cognitive Search, wordt deze opgeslagen in een *index* binnen de zoekservice. Er kunnen een aantal indexen binnen één service zijn. Als u de vertrouwde concepten van databases wilt gebruiken, kan de zoekservice worden vergeleken met een database, terwijl de indexen binnen een service kunnen worden vergeleken met tabellen in een database.

Elke index binnen een zoekservice heeft een eigen schema, dat wordt gedefinieerd door een aantal aanpasbare *velden.* Gegevens worden toegevoegd aan een Azure Cognitive Search-index in de vorm van afzonderlijke *documenten.* Elk document moet worden geüpload naar een bepaalde index en moet passen in het schema van die index. Bij het zoeken naar gegevens met Azure Cognitive Search worden de zoekopdrachten met volledige tekst uitgegeven tegen een bepaalde index.  Als u deze concepten wilt vergelijken met die van een database, kunnen velden worden vergeleken met kolommen in een tabel en kunnen documenten worden vergeleken met rijen.

### <a name="scalability"></a>Schaalbaarheid
Elke Azure Cognitive Search-service in de [standaardprijslaag](https://azure.microsoft.com/pricing/details/search/) kan worden geschaald in twee dimensies: opslag en beschikbaarheid.

* *Partities* kunnen worden toegevoegd om de opslag van een zoekservice te vergroten.
* *Replica's* kunnen worden toegevoegd aan een service om de doorvoer van aanvragen die een zoekservice kan verwerken te verhogen.

Het toevoegen en verwijderen van partities en replica's op zal de capaciteit van de zoekservice om te groeien met de hoeveelheid gegevens en het verkeer van de toepassing eisen. Om een zoekservice te bereiken om een gelezen [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)te bereiken, vereist dit twee replica's. Om een service te bereiken een read-write [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), het vereist drie replica's.

### <a name="service-and-index-limits-in-azure-cognitive-search"></a>Service- en indexlimieten in Azure Cognitive Search
Er zijn een paar verschillende [prijsniveaus](https://azure.microsoft.com/pricing/details/search/) in Azure Cognitive Search, elk van de lagen heeft verschillende [limieten en quota.](search-limits-quotas-capacity.md) Sommige van deze limieten zijn op serviceniveau, sommige zijn op indexniveau en sommige bevinden zich op partitieniveau.

|  | Basic | Standaard1 | Standaard2 | Standaard3 | Standaard3 HD |
| --- | --- | --- | --- | --- | --- |
| Maximale replica's per service |3 |12 |12 |12 |12 |
| Maximale partities per service |1 |12 |12 |12 |3 |
| Maximale zoekeenheden (replica's*partities) per service |3 |36 |36 |36 |36 (maximaal 3 partities) |
| Maximale opslag per service |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| Maximale opslag per partitie |2 GB |25 GB |100 GB |200 GB |200 GB |
| Maximale indexen per service |5 |50 |200 |200 |3000 (max 1000 indexen/partitie) |

#### <a name="s3-high-density"></a>S3 Hoge Dichtheid'
In de S3-prijscategorie Van Azure Cognitive Search is er een optie voor de HD-modus (High Density) die speciaal is ontworpen voor multitenant-scenario's. In veel gevallen is het noodzakelijk om een groot aantal kleinere huurders onder één service te ondersteunen om de voordelen van eenvoud en kostenefficiëntie te bereiken.

S3 HD zorgt ervoor dat de vele kleine indexen worden verpakt onder het beheer van een enkele zoekservice door de mogelijkheid te verhandelen om indexen uit te schalen met behulp van partities voor de mogelijkheid om meer indexen in één service te hosten.

Concreet kan een S3-service tussen de 1 en 200 indexen hebben die samen tot 1,4 miljard documenten kunnen hosten. Een S3 HD aan de andere kant zou individuele indexen in staat stellen om slechts tot 1 miljoen documenten te gaan, maar het kan tot 1000 indexen per partitie verwerken (tot 3000 per service) met een totaal aantal documenten van 200 miljoen per partitie (tot 600 miljoen per service).

## <a name="considerations-for-multitenant-applications"></a>Overwegingen voor multitenant-toepassingen
Multitenant-toepassingen moeten effectief middelen verdelen onder de huurders met behoud van een zekere mate van privacy tussen de verschillende tenants. Er zijn een paar overwegingen bij het ontwerpen van de architectuur voor een dergelijke toepassing:

* *Huurder isolatie:* Ontwikkelaars van toepassingen moeten passende maatregelen nemen om ervoor te zorgen dat geen enkele huurders ongeautoriseerde of ongewenste toegang hebben tot de gegevens van andere tenants. Afgezien van het perspectief van gegevensprivacy, tenant isolatie strategieën vereisen effectief beheer van gedeelde middelen en bescherming tegen luidruchtige buren.
* *Kosten voor cloudbronnen:* Zoals bij elke andere toepassing moeten softwareoplossingen kostenconcurrerend blijven als onderdeel van een multitenant-toepassing.
* *Bedieningsgemak:* Bij het ontwikkelen van een multitenant-architectuur is de impact op de werking en complexiteit van de toepassing een belangrijke overweging. Azure Cognitive Search heeft een [SLA van 99,9%.](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
* *Wereldwijde voetafdruk:* Multitenant-toepassingen moeten mogelijk huurders die over de hele wereld worden gedistribueerd, effectief bedienen.
* *Schaalbaarheid:* Toepassingsontwikkelaars moeten overwegen hoe ze in overeenstemming zijn tussen het handhaven van een voldoende laag niveau van toepassingscomplexiteit en het ontwerpen van de toepassing om te schalen met het aantal tenants en de grootte van de gegevens en werkbelasting van tenants.

Azure Cognitive Search biedt een aantal grenzen die kunnen worden gebruikt om de gegevens en werkbelasting van tenants te isoleren.

## <a name="modeling-multitenancy-with-azure-cognitive-search"></a>Multitenancy modelleren met Azure Cognitive Search
In het geval van een multitenant-scenario verbruikt de toepassingsontwikkelaar een of meer zoekservices en verdeelt hij zijn huurders over services, indexen of beide. Azure Cognitive Search heeft een paar veelvoorkomende patronen bij het modelleren van een multitenant-scenario:

1. *Index per tenant:* Elke tenant heeft zijn eigen index binnen een zoekservice die wordt gedeeld met andere tenants.
2. *Service per tenant:* Elke tenant heeft zijn eigen speciale Azure Cognitive Search-service, die het hoogste niveau van gegevens- en werkbelastingscheiding biedt.
3. *Mix van beide:* Grotere, actievere tenants krijgen toegewezen speciale services, terwijl kleinere tenants afzonderlijke indexen toegewezen krijgen binnen gedeelde services.

## <a name="1-index-per-tenant"></a>1. Index per tenant
![Een afbeelding van het index-per-tenant model](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

In een index-per-tenantmodel bezetten meerdere tenants één Azure Cognitive Search-service waarbij elke tenant zijn eigen index heeft.

Tenants bereiken gegevensisolatie omdat alle zoekaanvragen en documentbewerkingen worden uitgegeven op indexniveau in Azure Cognitive Search. In de toepassingslaag is er de noodzaak om het verkeer van de verschillende tenants naar de juiste indexen te leiden en tegelijkertijd resources op serviceniveau over alle tenants te beheren.

Een belangrijk kenmerk van het index-per-tenantmodel is de mogelijkheid voor de toepassingsontwikkelaar om de capaciteit van een zoekservice onder de tenants van de toepassing te overschrijven. Als de tenants een ongelijke verdeling van de werklast hebben, kan de optimale combinatie van tenants worden verdeeld over de indexen van een zoekservice om plaats te bieden aan een aantal zeer actieve, resource-intensieve huurders, terwijl tegelijkertijd een lange staart van minder actieve huurders. De afweging is het onvermogen van het model om situaties te behandelen waarin elke tenant gelijktijdig zeer actief is.

Het index-per-tenant-model biedt de basis voor een variabel kostenmodel, waarbij een volledige Azure Cognitive Search-service vooraf wordt opgekocht en vervolgens wordt gevuld met tenants. Hierdoor kan ongebruikte capaciteit worden aangewezen voor proeven en gratis accounts.

Voor toepassingen met een globale voetafdruk is het index-per-tenantmodel mogelijk niet het meest efficiënt. Als de tenants van een toepassing over de hele wereld worden verdeeld, kan een afzonderlijke service nodig zijn voor elke regio die de kosten voor elk van deze regio's kan dupliceren.

Azure Cognitive Search zorgt voor de schaal van zowel de afzonderlijke indexen als het totale aantal indexen om te groeien. Als een geschikte prijscategorie wordt gekozen, kunnen partities en replica's aan de gehele zoekservice worden toegevoegd wanneer een afzonderlijke index binnen de service te groot wordt in termen van opslag of verkeer.

Als het totale aantal indexen te groot wordt voor één service, moet er een andere service worden ingericht om de nieuwe tenants te kunnen huisvesten. Als indexen tussen zoekservices moeten worden verplaatst als nieuwe services worden toegevoegd, moeten de gegevens uit de index handmatig van de ene index naar de andere worden gekopieerd, omdat Azure Cognitive Search het niet mogelijk maakt dat een index wordt verplaatst.

## <a name="2-service-per-tenant"></a>2. Service per tenant
![Een weergave van het service-per-tenant model](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

In een service-per-tenant-architectuur heeft elke tenant zijn eigen zoekservice.

In dit model bereikt de toepassing het maximale niveau van isolatie voor zijn huurders. Elke service heeft speciale opslag en doorvoer voor het verwerken van zoekaanvraag en afzonderlijke API-sleutels.

Voor toepassingen waarbij elke tenant een grote voetafdruk heeft of de werkbelasting weinig variabiliteit heeft van tenant tot tenant, is het service-per-tenantmodel een effectieve keuze omdat resources niet worden gedeeld over de workloads van verschillende tenants.

Een service per tenant model biedt ook het voordeel van een voorspelbaar, vast kostenmodel. Er is geen investering vooraf in een volledige zoekservice totdat er een tenant is om deze te vullen, maar de kosten per tenant zijn hoger dan een index-per-tenant-model.

Het service-per-tenant model is een efficiënte keuze voor toepassingen met een wereldwijde voetafdruk. Met geografisch verdeelde huurders is het eenvoudig om de service van elke huurder in de juiste regio te hebben.

De uitdagingen bij het schalen van dit patroon ontstaan wanneer individuele huurders hun service ontgroeien. Azure Cognitive Search biedt momenteel geen ondersteuning voor het upgraden van de prijscategorie van een zoekservice, dus alle gegevens moeten handmatig worden gekopieerd naar een nieuwe service.

## <a name="3-mixing-both-models"></a>3. Het mengen van beide modellen
Een ander patroon voor het modelleren van multitenancy is het mengen van zowel index-per-tenant en service-per-tenant strategieën.

Door de twee patronen te mengen, kunnen de grootste tenants van een toepassing speciale services bezetten, terwijl de lange staart van minder actieve, kleinere tenants indexen in een gedeelde service kan bezetten. Dit model zorgt ervoor dat de grootste huurders consequent hoge prestaties van de service hebben, terwijl het helpen om de kleinere huurders te beschermen tegen luidruchtige buren.

De implementatie van deze strategie is echter afhankelijk van de vooruitziende blik bij het voorspellen van welke tenants een specifieke service nodig hebben ten opzichte van een index in een gedeelde service. De complexiteit van toepassingen neemt toe met de noodzaak om beide multitenancy-modellen te beheren.

## <a name="achieving-even-finer-granularity"></a>Het bereiken van nog fijnere granulariteit
De bovenstaande ontwerppatronen voor het modelleren van multitenant-scenario's in Azure Cognitive Search gaan uit van een uniforme scope waarbij elke tenant een hele instantie van een toepassing is. Toepassingen kunnen echter soms veel kleinere scopes verwerken.

Als service-per-tenant- en index-per-tenant-modellen niet voldoende kleine scopes zijn, is het mogelijk om een index te modelleren om een nog fijnere mate van granulariteit te bereiken.

Om een enkele index zich anders te laten gedragen voor verschillende clienteindpunten, kan een veld worden toegevoegd aan een index die een bepaalde waarde aangeeft voor elke mogelijke client. Elke keer dat een client Azure Cognitive Search aanroept om een index op te vragen of te wijzigen, geeft de code van de clienttoepassing de juiste waarde voor dat veld op met behulp van de [filtermogelijkheden](https://msdn.microsoft.com/library/azure/dn798921.aspx) van Azure Cognitive Search tijdens querytijd.

Deze methode kan worden gebruikt om functionaliteit van afzonderlijke gebruikersaccounts, afzonderlijke machtigingsniveaus en zelfs volledig afzonderlijke toepassingen te bereiken.

> [!NOTE]
> Het gebruik van de hierboven beschreven aanpak om één index te configureren voor meerdere tenants is van invloed op de relevantie van zoekresultaten. Zoekrelevantiescores worden berekend op een bereik op indexniveau, niet op een bereik op tenantniveau, zodat alle gegevens van huurders worden opgenomen in de onderliggende statistieken van de relevantiescores, zoals termfrequentie.
> 
> 

## <a name="next-steps"></a>Volgende stappen
Azure Cognitive Search is een aantrekkelijke keuze voor veel toepassingen. Houd bij het evalueren van de verschillende ontwerppatronen voor multitenant-toepassingen rekening met de [verschillende prijsniveaus](https://azure.microsoft.com/pricing/details/search/) en de respectieve [servicelimieten](search-limits-quotas-capacity.md) om Azure Cognitive Search het beste aan te passen aan toepassingsworkloads en architecturen van elke omvang.

Eventuele vragen over Azure Cognitive Search en azuresearch_contact@microsoft.commultitenant-scenario's kunnen worden gericht op .

