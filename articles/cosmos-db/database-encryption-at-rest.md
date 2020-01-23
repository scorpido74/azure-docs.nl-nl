---
title: Versleuteling-at-rest in Azure Cosmos DB
description: Meer informatie over hoe Azure Cosmos DB biedt versleuteling van data-at-rest en hoe deze is geïmplementeerd.
author: markjbrown
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.custom: seodec18
ms.openlocfilehash: 366a8cc2d8b08c9508053eaeb8bf70622fd870cf
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548098"
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

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>V: hoe veel meer kost Azure Storage als de Storage-Serviceversleuteling is ingeschakeld?
Antwoord: Er is geen extra kosten.

### <a name="q-who-manages-the-encryption-keys"></a>V: die de versleutelingssleutels worden beheerd?
A: de sleutels worden beheerd door Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>V: hoe vaak worden versleutelingssleutels gedraaid?
A: Microsoft heeft een set van interne richtlijnen voor codering sleutelroulatie, waarbij Cosmos DB. De specifieke richtlijnen zijn niet gepubliceerd. Microsoft publiceren de [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx), die wordt gezien als een subset van de interne richtlijnen en aanbevolen procedures voor nuttig is voor ontwikkelaars.

### <a name="q-can-i-use-my-own-encryption-keys"></a>V: kan ik mijn eigen versleutelingssleutels gebruiken?
A: Ja, deze functie is nu beschikbaar voor de nieuwe Cosmos-accounts en moet worden uitgevoerd op het moment dat het account wordt gemaakt. Ga naar door de [klant beheerde sleutels](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk) document voor meer informatie.

### <a name="q-what-regions-have-encryption-turned-on"></a>V: wat regio's hebt u versleuteling is ingeschakeld?
A: alle regio's van Azure Cosmos DB hebt versleuteling is ingeschakeld voor alle gebruikersgegevens.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>V: versleuteling beïnvloedt de prestaties van latentie en doorvoer Sla's?
Antwoord: Er is geen impact of de wijzigingen op de prestaties van SLA's nu dat versleuteling-at-rest wordt ingeschakeld voor alle bestaande en nieuwe accounts. U kunt meer lezen over de [SLA voor Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) pagina om te bekijken van de meest recente garanties.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>V: de lokale emulator ondersteunt versleuteling-at-rest?
A: de emulator is een hulpprogramma voor het ontwikkelen en testen van zelfstandige en gebruikt niet de key management-services die de beheerde Cosmos DB-service gebruikt. Onze aanbeveling is het inschakelen van BitLocker op stations waar u gevoelige emulator testgegevens opslaat. De [emulator biedt ondersteuning voor het wijzigen van de standaardgegevensmap](local-emulator.md) of via een bekende locatie.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Cosmos data base Security](database-security.md)(Engelstalig) voor een overzicht van Cosmos DB beveiliging en de nieuwste verbeteringen.
Zie voor meer informatie over Microsoft-certificeringen, de [Azure Trust Center](https://azure.microsoft.com/support/trust-center/).
