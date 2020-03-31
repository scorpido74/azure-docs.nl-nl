---
title: Toepassingsgegevens opslaan, beheren en blijven bestaan in de cloud met Visual Studio App Center en Azure-services
description: Meer informatie over services zoals Visual Studio App Center waarmee u mobiele toepassingsgegevens in de cloud opslaan, beheren en voortbestaan.
author: codemillmatt
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 166847325fa9094136f1c2a143f1751420f05f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240917"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Mobiele toepassingsgegevens opslaan, synchroniseren en opvragen vanuit de cloud
Het maakt niet uit wat voor soort applicatie je bouwt, zult u waarschijnlijk genereren en verwerken van gegevens. De gebruikers van uw toepassing hebben hoge verwachtingen. Ze willen dat de applicatie snel en naadloos werkt, onder alle omstandigheden. De meeste toepassingen werken ook op meerdere apparaten. Mogelijk hebt u toegang tot uw toepassing vanaf een desktop of mobiel apparaat. Meerdere gebruikers kunnen de toepassing tegelijkertijd gebruiken en gegevens delen met de verwachting dat ze direct en realtime toegang krijgen tot gegevens.

Uw toepassingsgebruikers hebben niet altijd een internetverbinding. Toepassingen zijn ontworpen en naar verwachting werken met of zonder een internetverbinding. Ontwikkelaars moeten de juiste oplossing kiezen voor het opslaan en synchroniseren van hun gegevens naar de cloud om een geweldige klantervaring voor hun toepassing te bieden.

Microsoft biedt een verscheidenheid aan services die het niet nodig hebben om servers op te draaien, uw database te kiezen of zich zorgen te maken over schaal of beveiliging om een zo rijke ervaring mogelijk te bieden. Deze services bieden een geweldige ontwikkelaarservaring waarmee u toepassingsgegevens in de cloud opslaan met SQL- of NoSQL-API's. U ook gegevens synchroniseren op alle apparaten en de toepassing in staat stellen om te werken met of zonder netwerkverbinding om schaalbare en robuuste toepassingen te bouwen.

Gebruik de volgende services voor het beheren en opslaan van mobiele toepassingsgegevens in de cloud.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Data](/appcenter/data/) is een datamanagementservice. Het stelt toepassingen in staat om toepassingsgegevens in de cloud te beheren, te blijven bestaan en te synchroniseren op verschillende apparaten en platforms in online en offline scenario's. App Center Data is gebouwd bovenop Azure Cosmos DB en schaalt naarmate uw gebruikersbasis en het aantal toepassingen toenemen. Het zorgt voor een lage latentie, hoge beschikbaarheid en hoge schaalbaarheid voor al uw gegevens.

**Belangrijke functies**
- Eenvoudige inrichten op een nieuwe Azure Cosmos DB-database of een bestaande Azure Cosmos DB-database vanuit de Visual Studio App Center-portal.
- NoSQL-databaseondersteuning voor het eenvoudig opslaan, synchroniseren en querytoepassingsgegevens.
- Deze service is gebouwd bovenop Azure Cosmos DB en erft de meeste belangrijke functies die azure cosmos DB bieden en kan wereldwijd worden geschaald om aan uw bedrijfsbehoeften te voldoen.
- Online en offline synchronisatiemogelijkheden om gegevens op verschillende apparaten te synchroniseren.
- SDK's van mobiele klanten die u gebruiken om eenvoudig privétoepassingsgegevens te beheren.
- Platformondersteuning voor iOS, Android, Xamarin en React Native.

**Verwijzingen**
- [Meld u aan bij Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Aan de slag met App Center Data](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) is een wereldwijd gedistribueerde databaseservice met meerdere modellen. U het gebruiken om toepassingen op planeetschaal te bouwen. Met Azure Cosmos DB u de doorvoer en opslag in elk aantal Azure-regio's wereldwijd elastisch en onafhankelijk schalen. U profiteren van snelle gegevenstoegang met één cijfer met behulp van uw favoriete API-oppervlakken. Deze oppervlakken omvatten SQL, MongoDB, Cassandra, Tabellen of Gremlin. Azure Cosmos DB biedt op unieke wijze uitgebreide servicelevelovereenkomsten (SLA's) voor doorvoer, latentie, beschikbaarheid en consistentie.

**Belangrijke functies**
- Ondersteunt een breed scala aan API's, waaronder de SQL (Core) API, Cassandra API, MongoDB API, Gremlin API en Table API.
- Turnkey global distribution repliceert uw gegevens waar uw gebruikers zich ook bevinden. Uw gebruikers kunnen communiceren met een replica van de gegevens die het dichtst bij hen staan.
- Geen schema- of indexbeheer omdat de databaseengine volledig schemaagnostisch is.
- Alomtegenwoordige regionale aanwezigheid omdat Azure Cosmos DB beschikbaar is in alle Azure-regio's wereldwijd, waaronder 54+ regio's in de openbare cloud.
- Precies gedefinieerde, meerdere consistentiekeuzes omdat het multimasterreplicatieprotocol van Azure Cosmos DB zorgvuldig is ontworpen om vijf goed gedefinieerde consistentiekeuzes te bieden. Deze vijf keuzes zijn sterk, begrensd staleness, sessie, consistente voorvoegsel, en uiteindelijk.
- 99.999% beschikbaarheid voor zowel leest als schrijft.
- Programmatisch (of via de Azure-portal) beroept u op de regionale failover van uw Azure Cosmos DB-account om ervoor te zorgen dat uw toepassing is ontworpen om een regionale ramp te weerstaan.
- Gegarandeerde lage latency bij het 99e percentiel wereldwijd.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com) 
- [Azure Cosmos DB-documentatie](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Azure SQL Database
 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) is een beheerde service voor algemene doeleinden relationele database. U het gebruiken om een zeer beschikbare en krachtige gegevensopslaglaag te maken voor toepassingen en oplossingen in de Azure-cloud.

**Belangrijke functies**
- **Elastische databasemodellen en -hulpprogramma's:** Met een elastische database kunnen ontwikkelaars resources bundelen tussen een groep databases voor schalen. Als u deze resources administratief wilt beheren, verzendt u een script als taak. De SQL-database voert vervolgens het script uit in de databases.
- **Hoge prestaties:** Toepassingen met een hoge doorvoer kunnen profiteren van de nieuwste versie. Het levert 25% meer premium database vermogen.
- **Back-ups, replicatie en hoge beschikbaarheid:** Ingebouwde replicatie en een door Microsoft gesteunde SLA op databaseniveau bieden de continuïteit en bescherming van toepassingen tegen catastrofale gebeurtenissen. Met actieve georeplicatie u failover- en selfserviceherstel configureren, die volledige controle bieden over 'oeps-herstel'. Gegevensherstel is beschikbaar via back-ups van gegevens tot 35 dagen.
- **Bijna nul onderhoud:** Automatische software maakt deel uit van de service. Ingebouwde systeemreplica's helpen om inherente gegevensbescherming, databaseuptime en systeemstabiliteit te bieden. Systeemreplica's worden automatisch verplaatst naar nieuwe computers. Ze zijn on the fly ingericht als oude falen.
- **Beveiliging:** Azure SQL Database biedt een portfolio van beveiligingsfuncties om te voldoen aan het nalevingsbeleid voor organisaties of in de branche:
    - Auditing biedt ontwikkelaars de mogelijkheid om compliance-gerelateerde taken uit te voeren en kennis op te doen over activiteiten.
    - Ontwikkelaars en IT kunnen beleid op databaseniveau implementeren om de toegang tot gevoelige gegevens te beperken met beveiliging op rijniveau, dynamische gegevensmaskering en transparante gegevensversleuteling voor Azure SQL Database.
    - Azure SQL Database wordt geverifieerd door belangrijke cloudauditors als onderdeel van het bereik van belangrijke Azure-nalevingscertificeringen en -goedkeuringen, zoals HIPAA BAA, ISO/IEC 27001:2005, FedRAMP en EU-modelclausules.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com) 
- [Documentatie voor Azure SQL Database](/azure/sql-database/) 
