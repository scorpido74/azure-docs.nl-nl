---
title: Toepassings gegevens in de Cloud opslaan, beheren en persistent maken met Visual Studio App Center en Azure-Services
description: Meer informatie over services zoals Visual Studio App Center waarmee u mobiele toepassings gegevens in de cloud kunt opslaan, beheren en behouden.
author: codemillmatt
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: cb76151a3f952f932cc533b771283b5d45382366
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873455"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Mobiele toepassings gegevens vanuit de Cloud opslaan, synchroniseren en doorzoeken
Ongeacht het soort toepassing dat u bouwt, zult u waarschijnlijk gegevens genereren en verwerken. De gebruikers van uw toepassing hebben hoge verwachtingen. Ze willen dat de toepassing in alle omstandigheden snel en probleemloos werkt. De meeste toepassingen werken ook op meerdere apparaten. U kunt toegang krijgen tot uw toepassing vanaf een bureau blad of een mobiel apparaat. Meerdere gebruikers kunnen de toepassing tegelijkertijd gebruiken en gegevens delen met de verwachting van de toegang tot gegevens en real-time.

Uw toepassings gebruikers hebben niet altijd een Internet verbinding. Toepassingen zijn ontworpen en verwacht om te werken met of zonder een Internet verbinding. Ontwikkel aars moeten de juiste oplossing kiezen om hun gegevens in de Cloud op te slaan en te synchroniseren om een goede klant ervaring te bieden voor de toepassing.

Micro soft biedt diverse services waarmee u geen servers hoeft in te zetten, uw data base te kiezen of u zorgen te maken over schalen of beveiliging om zo veel mogelijk ervaring te bieden. Deze services bieden een fantastische ervaring voor ontwikkel aars waarmee u toepassings gegevens kunt opslaan in de Cloud met behulp van SQL-of NoSQL-Api's. U kunt ook gegevens op alle apparaten synchroniseren en de toepassing inschakelen voor gebruik met of zonder een netwerk verbinding om schaal bare en robuuste toepassingen te bouwen.

Gebruik de volgende services om mobiele toepassings gegevens in de cloud te beheren en op te slaan.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) is een wereld wijd gedistribueerde, multi-model database service. U kunt deze gebruiken om toepassingen op basis van planeet te schalen. Met Azure Cosmos DB kunt u de door Voer en opslag op een flexibele en onafhankelijke manier schalen voor elk wille keurig aantal Azure-regio's wereld wijd. U kunt profiteren van snelle toegang tot gegevens met één cijfer en data-milliseconden met behulp van uw favoriete API-Opper vlakken. Deze Opper vlakken bevatten SQL, MongoDB, Cassandra, Tables of Gremlin. Azure Cosmos DB biedt unieke service overeenkomsten (Sla's) voor door Voer, latentie, Beschik baarheid en consistentie.

**Belangrijke functies**
- Ondersteunt een breed scala aan Api's, waaronder de SQL-API (core), Cassandra-API, MongoDB API, Gremlin API en Table-API.
- Kant-en-klare wereld wijde distributie repliceert uw gegevens overal waar uw gebruikers zich bevinden. Uw gebruikers kunnen communiceren met een replica van de gegevens die het dichtst bij hen ligt.
- Er is geen schema-of index beheer, omdat de data base-engine volledig schema-neutraal is.
- Alomtegenwoordige regionale aanwezigheid omdat Azure Cosmos DB beschikbaar is in alle Azure-regio's over de hele wereld, waaronder 54 en regio's in de open bare Cloud.
- Nauw keurig gedefinieerd, meerdere consistentie mogelijkheden omdat het multi-master replicatie Protocol van Azure Cosmos DB nauw keurig is ontworpen om vijf goed gedefinieerde consistentie mogelijkheden te bieden. Deze vijf opties zijn Strong, gebonden veroudering, sessie, consistent voor voegsel en uiteindelijk.
- 99,999% Beschik baarheid voor zowel lees-als schrijf bewerkingen.
- Via een programma (of via de Azure Portal) wordt de regionale failover van uw Azure Cosmos DB-account aangeroepen om ervoor te zorgen dat uw toepassing is ontworpen voor een regionale nood geval.
- Gegarandeerde lage latentie bij het 99e percentiel over de hele wereld.

**Verwijzingen**
- [Azure Portal](https://portal.azure.com) 
- [Documentatie over Azure Cosmos DB](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Azure SQL Database
 [Azure SQL database](https://azure.microsoft.com/services/sql-database/) is een service voor algemeen gebruik van relationele data bases. U kunt deze gebruiken om een Maxi maal beschik bare gegevenslaag met hoge prestaties te maken voor toepassingen en oplossingen in de Azure-Cloud.

**Belangrijke functies**
- **Modellen en hulpprogram ma's voor elastische data bases:** Met een elastische data base kunnen ontwikkel aars resources groeperen in een groep data bases voor schalen. Als u deze resources wilt beheren, dient u een script als een taak in te dienen. Het SQL database voert vervolgens het script uit op de data bases.
- **Hoge prestaties:** Toepassingen met een hoge door Voer kunnen profiteren van de nieuwste versie. Het biedt 25% meer Premium-database kracht.
- **Back-ups, replicatie en hoge Beschik baarheid:** Ingebouwde replicatie en een door micro soft ondersteunde SLA op database niveau bieden de continuïteit van toepassingen en bescherming tegen onherstelbare gebeurtenissen. Met actieve geo-replicatie kunt u failover en self-service herstel configureren, waarmee u volledige controle over ' huidige herstel ' krijgt. Het terugzetten van gegevens is beschikbaar via gegevens back-ups van Maxi maal 35 dagen.
- **Bijna nul onderhoud:** Automatische software maakt deel uit van de service. Ingebouwde systeem replica's helpen u bij het leveren van inherente gegevens beveiliging, Beschik baarheid van de data base en systeem stabiliteit. Systeem replica's worden automatisch verplaatst naar nieuwe computers. Deze worden op de vlucht ingericht als oude.
- **Beveiliging:** Azure SQL Database biedt een port Folio met beveiligings functies die voldoen aan het nalevings beleid van de organisatie of de branche:
    - Auditing biedt ontwikkel aars de mogelijkheid om aan naleving gerelateerde taken uit te voeren en kennis te krijgen over activiteiten.
    - Ontwikkel aars en IT kunnen beleid op database niveau implementeren om de toegang tot gevoelige gegevens te helpen beperken met beveiliging op rijniveau, dynamische gegevens maskering en transparante gegevens versleuteling voor Azure SQL Database.
    - Azure SQL Database wordt gecontroleerd door belang rijke Cloud controleurs als onderdeel van de belangrijkste Azure-nalevings certificeringen en-goed keuringen, zoals HIPAA BAA, ISO/IEC 27001:2005, FedRAMP en EU-model clausules.

**Verwijzingen**
- [Azure Portal](https://portal.azure.com) 
- [Documentatie voor Azure SQL Database](/azure/sql-database/) 
