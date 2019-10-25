---
title: Toepassings gegevens in de Cloud opslaan, beheren en persistent maken met Visual Studio App Center en Azure-Services
description: Meer informatie over de services, zoals App Center waarmee u mobiele toepassings gegevens in de cloud kunt opslaan, beheren en behouden.
author: elamalani
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b7043c7e9bd944bfb3633397edfa3c35609bd8ec
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795573"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Mobiele toepassings gegevens vanuit de Cloud opslaan, synchroniseren en doorzoeken
Ongeacht het soort toepassing dat u wilt maken, is het waarschijnlijk dat u gegevens wilt genereren en verwerken. Gebruikers verwachtingen zijn hoog en ze willen dat de toepassing in alle omstandigheden snel en probleemloos werkt. De meeste toepassingen werken op meerdere apparaten, dus wanneer u toegang tot uw toepassing hebt, is het mogelijk dat u deze opent via een bureau blad of een mobiel apparaat. Het is mogelijk dat meerdere gebruikers tegelijkertijd gebruikmaken van de toepassing en gegevens delen met de verwachting van het verkrijgen van directe toegang tot gegevens en realtime.

Op dat moment hebben uw toepassings gebruikers niet altijd een Internet verbinding. toepassingen zijn ontworpen en verwacht om te werken met of zonder Internet verbinding. Met al deze groeiende complexen is het een enorme taak voor ontwikkel aars om de juiste oplossing voor het opslaan en synchroniseren van hun gegevens naar de cloud te kiezen om een goede klant ervaring te bieden voor de toepassing.

Micro soft biedt diverse services waarmee u geen servers hoeft in te zetten, uw data base te kiezen of u zorgen te maken over schalen of beveiliging om zo veel mogelijk ervaring te bieden. Deze services bieden een fantastische ontwikkel ervaring waarmee u toepassings gegevens kunt opslaan in de Cloud met behulp van SQL of NoSQL Api's, gegevens op alle apparaten synchroniseert en de toepassing kunt gebruiken om te werken met of zonder een netwerk verbinding om schaal bare en robuuste functies te bouwen. toepassingen.

Gebruik de volgende services om mobiele toepassings gegevens in de cloud te beheren en op te slaan.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center gegevens](/appcenter/data/) is een service voor gegevens beheer waarmee toepassingen toepassings gegevens in de Cloud kunnen beheren, persistent maken en synchroniseren op verschillende apparaten en platformen in zowel online-als offline scenario's. Deze service is **gebaseerd op Cosmos DB** -service die kan worden geschaald als uw gebruikers basis en het aantal toepassingen groeit, en zorgt voor een lage latentie, hoge Beschik baarheid en hoge schaal baarheid voor al uw gegevens.

**Belangrijkste functies**
- U kunt eenvoudig **een nieuwe Cosmos DB Data Base inrichten** of **verbinding maken met een bestaande Cosmos DB-data base** vanuit de app Center Portal.
- **NoSQL data base-ondersteuning** om toepassings gegevens eenvoudig op te slaan, te synchroniseren en op te vragen.
- Deze service neemt **boven op Cosmos DB**de meeste van de belangrijkste functies van Azure Cosmos DB over en kan **wereld wijd schalen** om te voldoen aan de behoeften van uw bedrijf.
- Mogelijkheden voor **online-en offline synchronisatie** om gegevens op verschillende apparaten te synchroniseren.
- Mobiele **client-sdk's** waarmee u eenvoudig zowel persoonlijke toepassings gegevens kunt beheren.
- **Platform ondersteuning** : IOS, Android, Xamarin, reageren systeem eigen.

**Referentie**
- [Meld u aan met App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Aan de slag met App Center gegevens](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) is de wereld wijd gedistribueerde, multi-model database service van micro soft waarmee u op planeet schaal toepassingen kunt bouwen. Met een klik op een knop kunt Cosmos DB u op een flexibele en onafhankelijke manier door Voer en opslag over elk wille keurig aantal Azure-regio's wereld wijd schalen. U kunt de door Voer en opslag elastisch schalen en profiteren van snelle, single-milliseconden-data toegang met behulp van uw favoriete API-Opper vlakken, waaronder SQL, MongoDB, Cassandra, Tables of Gremlin. Cosmos DB biedt unieke service overeenkomsten (Sla's) voor door Voer, latentie, Beschik baarheid en consistentie.

**Belangrijkste functies**
- Biedt ondersteuning **voor een breed scala aan api's** , waaronder SQL (core) API, Cassandra-API, MongoDb API, Gremlin api en Table-API.
- Kant-en- **klare wereld wijde distributie** repliceert uw gegevens overal waar uw gebruikers zich bevinden, zodat uw gebruikers kunnen communiceren met een replica van de gegevens die het dichtst bij hen ligt.
- **Geen schema-of index beheer** als de data base-engine is volledig schema-neutraal.
- **Alomtegenwoordige regionale aanwezigheid** als Cosmos DB is beschikbaar in alle Azure-regio's over de hele wereld, met inbegrip van 54 + regio's in de open bare Cloud.
- **Nauw keurig gedefinieerd, meerdere consistentie opties** als Cosmos DB het multi-master replicatie Protocol van het model zijn zorgvuldig ontworpen om vijf goed gedefinieerde consistentie mogelijkheden te bieden: Strong, gebonden veroudering, sessie, consistent voor voegsel en uiteindelijk.
- **99,999% Beschik baarheid** voor zowel lees-als schrijf bewerkingen.
- **Programmatisch (of via Portal) roept de regionale failover** van uw Cosmos-account aan om ervoor te zorgen dat uw toepassing is ontworpen voor een regionale nood geval.
- **Gegarandeerde lage latentie bij 99e percentiel**, wereld wijd.

**Referentie**
- [Azure-portal](https://portal.azure.com) 
- [Documentatie](/azure/cosmos-db/introduction)   

## <a name="azure-sql-database"></a>Azure SQL Database
 [Azure SQL database](https://azure.microsoft.com/services/sql-database/) is een service voor algemeen gebruik van relationele data bases waarmee u een Maxi maal beschik bare gegevenslaag met hoge prestaties kunt maken voor toepassingen en oplossingen in de Azure-Cloud.

**Belangrijkste functies**
- **Elastic data base-modellen en-hulpprogram ma's** : een elastische Data Base biedt ontwikkel aars de mogelijkheid om resources te poolen voor gebruik in een groep data bases voor schalen, die vervolgens door de beheerder kunnen worden beheerd door een script als een taak te verzenden en de SQL database voert de script in de data bases.
- **Hoge prestaties** : toepassingen met een hoge door Voer kunnen profiteren van de nieuwste versie, die 25% meer Premium-database kracht biedt.
- **Back-ups, replicatie en hoge Beschik baarheid** -ingebouwde replicatie en een sla met micro soft-ondersteuning op database niveau bieden continuïteit van toepassingen en bescherming tegen onherstelbare gebeurtenissen. Met actieve geo-replicatie kunt u failover en self-service terugzetten configureren, waarmee u volledige controle hebt over ' huidige herstel ' (gegevens herstel van beschik bare gegevens back-ups van Maxi maal 35 dagen).
- **Bijna nul onderhoud** : automatische software maakt deel uit van de service en ingebouwde systeem replica's helpen inherente gegevens beveiliging, Beschik baarheid van de data base en systeem stabiliteit te leveren. Systeemreplica's worden automatisch verplaatst naar nieuwe computers, die in een oogwenk worden ingericht wanneer oudere machines het begeven.
- **Security** -SQL database biedt een port Folio met beveiligings functies die voldoen aan het nalevings beleid van de organisatie of de branche
    - Auditing biedt ontwikkel aars de mogelijkheid om aan naleving gerelateerde taken uit te voeren en kennis te krijgen over activiteiten.
    - Ontwikkel aars en IT kunnen beleid op database niveau implementeren om de toegang tot gevoelige gegevens te helpen beperken met beveiliging op rijniveau, dynamische gegevens maskering en transparante gegevens versleuteling voor Azure SQL Database.
    - SQL Database wordt gecontroleerd door de belangrijkste Cloud controleurs als onderdeel van het bereik van belang rijke Azure-nalevings certificeringen en-goed keuringen, zoals HIPAA BAA, ISO/IEC 27001:2005, FedRAMP en EU-model clausules).

**Referentie**
- [Azure-portal](https://portal.azure.com) 
- [Documentatie](/azure/sql-database/)
   