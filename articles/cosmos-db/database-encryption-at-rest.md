---
title: Versleuteling-at-rest in Azure Cosmos DB
description: Meer informatie over hoe Azure Cosmos DB biedt versleuteling van data-at-rest en hoe deze is geïmplementeerd.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: f406f008e2c377b39deb8d151855ce7315616701
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616855"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Gegevens versleuteling in Azure Cosmos DB 

Versleuteling-at-rest is een woordgroep die vaak naar de versleuteling van gegevens op niet-vluchtig opslagapparaten, verwijst zoals Solid-State stations (SSD's) en harde schijven (HDD's). Cosmos DB slaat de bijbehorende primaire databases op SSD's. De mediabijlagen en back-ups worden opgeslagen in Azure Blob-opslag wordt gewoonlijk ondersteund door HDD. Met het uitbrengen van versleuteling-at-rest voor Cosmos DB, zijn alle databases, mediabijlagen en back-ups worden gecodeerd. Uw gegevens nu in transit versleuteld (via het netwerk) en at-rest (niet-vluchtig opslag), geeft u end-to-end versleuteling.

Als een PaaS-service, Cosmos DB heel eenvoudig te gebruiken is. Omdat alle gebruikersgegevens zijn opgeslagen in Cosmos DB is versleuteld in rust en transport, hebt u geen geen actie te ondernemen. Een andere manier om dit is dat versleuteling-at-rest 'op' standaard is. Er zijn geen besturingselementen om te schakelen in- of uitschakelen. Azure Cosmos DB maakt gebruik van AES-256-versleuteling voor alle regio's waar het account wordt uitgevoerd. We bieden deze functie terwijl we doorgaan om te voldoen aan onze [beschikbaarheid en prestaties van SLA's](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementatie van versleuteling-at-rest voor Azure Cosmos DB

Versleuteling-at-rest wordt geïmplementeerd met behulp van diverse beveiligingstechnologieën, met inbegrip van beveiligde opslag van clustersleutels systemen, versleutelde netwerken en cryptografische API's. Systemen die ontsleutelen en te verwerken gegevens heeft om te communiceren met systemen die sleutels beheren. Het diagram toont hoe de opslag van versleutelde gegevens en het beheer van de sleutels worden gescheiden. 

![Diagram ontwerpen](./media/database-encryption-at-rest/design-diagram.png)

De basisprincipes van een gebruikersaanvraag is als volgt:
- Het gebruikersaccount van de database gereed is gemaakt en storage-sleutels worden opgehaald via een aanvraag bij de Resourceprovider van de Management-Service.
- Een gebruiker maakt een verbinding met Cosmos DB via HTTPS/beveiligde transport. (Als de SDK's abstract de details.)
- De gebruiker verzendt een JSON-document via het eerder gemaakte beveiligde verbinding worden opgeslagen.
- Het JSON-document is geïndexeerd, tenzij de gebruiker heeft uitgeschakeld indexeren.
- Zowel de JSON-document en index de gegevens worden geschreven naar beveiligde opslag.
- Gegevens worden regelmatig lezen van de beveiligde opslag en back-ups op de Azure versleutelde Blob Store.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>V: Hoeveel meer heeft Azure Storage kosten als Storage Service Encryption is ingeschakeld?
A: Er zijn geen extra kosten.

### <a name="q-who-manages-the-encryption-keys"></a>V: Wie beheert de versleutelings sleutels?
A: De sleutels worden beheerd door micro soft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>V: Hoe vaak worden de versleutelings sleutels gedraaid?
A: Micro soft heeft een aantal interne richt lijnen voor de rotatie van de versleutelings sleutel, die Cosmos DB volgt. De specifieke richtlijnen zijn niet gepubliceerd. Microsoft publiceren de [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx), die wordt gezien als een subset van de interne richtlijnen en aanbevolen procedures voor nuttig is voor ontwikkelaars.

### <a name="q-can-i-use-my-own-encryption-keys"></a>V: Kan ik mijn eigen versleutelings sleutels gebruiken?
A: Cosmos DB is een PaaS-service en we hebben er hard aan gewerkt om de service eenvoudig te gebruiken. Ons opgevallen dat deze vraag is vaak gevraagd als een proxy-vraag voor het voldoen aan een nalevingsvereiste zoals PCI-DSS. We hebben als onderdeel van het bouwen van deze functie, met naleving auditors om ervoor te zorgen dat klanten die gebruikmaken van Cosmos DB aan hun eisen voldoen zonder de noodzaak om te sleutels zelf beheren.

### <a name="q-what-regions-have-encryption-turned-on"></a>V: In welke regio's is versleuteling ingeschakeld?
A: Voor alle Azure Cosmos DB regio's is versleuteling ingeschakeld voor alle gebruikers gegevens.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>V: Is versleuteling van invloed op de duur van de prestatie latentie en door Voer?
A: Er is geen invloed op of wijzigingen in de functie voor het uitvoeren van prestaties, nu de versleuteling bij rest is ingeschakeld voor alle bestaande en nieuwe accounts. U kunt meer lezen over de [SLA voor Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) pagina om te bekijken van de meest recente garanties.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>V: Ondersteunt de lokale emulator versleuteling op rest?
A: De emulator is een zelfstandig hulp programma voor ontwikkelen en testen en maakt geen gebruik van de services voor sleutel beheer die door de beheerde Cosmos DB-service worden gebruikt. Onze aanbeveling is het inschakelen van BitLocker op stations waar u gevoelige emulator testgegevens opslaat. De [emulator biedt ondersteuning voor het wijzigen van de standaardgegevensmap](local-emulator.md) of via een bekende locatie.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Cosmos data base Security](database-security.md)(Engelstalig) voor een overzicht van Cosmos DB beveiliging en de nieuwste verbeteringen.
Zie voor meer informatie over Microsoft-certificeringen, de [Azure Trust Center](https://azure.microsoft.com/support/trust-center/).
