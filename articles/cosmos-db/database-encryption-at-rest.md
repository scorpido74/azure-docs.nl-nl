---
title: Versleuteling in rust in Azure Cosmos DB
description: Ontdek hoe Azure Cosmos DB versleuteling biedt van gegevens in rust en hoe deze wordt geïmplementeerd.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.custom: seodec18
ms.openlocfilehash: db20388b5277e000ffe7055e9840742d6af7788e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062605"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Gegevensversleuteling in Azure Cosmos DB 

Encryptie in rust is een zin die vaak verwijst naar de versleuteling van gegevens op niet-vluchtige opslagapparaten, zoals solid state drives (SSD's) en harde schijven (HDD's). Cosmos DB slaat zijn primaire databases op ssd's. De mediabijlagen en back-ups worden opgeslagen in Azure Blob-opslag, waarvan meestal wordt geback-upt door HDD's. Met de release van encryptie in rust voor Cosmos DB, al uw databases, media bijlagen, en back-ups zijn versleuteld. Uw gegevens worden nu versleuteld tijdens het transport (via het netwerk) en in rust (niet-vluchtige opslag), waardoor u end-to-end encryptie krijgt.

Als PaaS-service is Cosmos DB zeer eenvoudig te gebruiken. Omdat alle gebruikersgegevens die zijn opgeslagen in Cosmos DB in rust en in transport worden versleuteld, hoeft u geen actie te ondernemen. Een andere manier om dit te zetten is dat encryptie in rust is "op" standaard. Er zijn geen besturingselementen om deze uit of aan te zetten. Azure Cosmos DB maakt gebruik van AES-256-versleuteling in alle regio's waar het account wordt uitgevoerd. We bieden deze functie terwijl we blijven voldoen aan onze [beschikbaarheid en prestaties SLA's](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementatie van versleuteling in rust voor Azure Cosmos DB

Encryptie in rust wordt geïmplementeerd met behulp van een aantal beveiligingstechnologieën, waaronder beveiligde sleutelopslagsystemen, versleutelde netwerken en cryptografische API's. Systemen die gegevens decoderen en verwerken, moeten communiceren met systemen die sleutels beheren. Het diagram laat zien hoe de opslag van versleutelde gegevens en het beheer van sleutels wordt gescheiden. 

![Ontwerpdiagram](./media/database-encryption-at-rest/design-diagram.png)

De basisstroom van een gebruikersverzoek is als volgt:
- Het gebruikersdatabaseaccount wordt gereed gemaakt en opslagsleutels worden opgehaald via een verzoek aan de Management Service Resource Provider.
- Een gebruiker maakt een verbinding met Cosmos DB via HTTPS/secure transport. (De SDKs abstraheert de details.)
- De gebruiker stuurt een JSON-document dat via de eerder gemaakte beveiligde verbinding moet worden opgeslagen.
- Het JSON-document wordt geïndexeerd, tenzij de gebruiker indexering heeft uitgeschakeld.
- Zowel het JSON-document als de indexgegevens worden geschreven om de opslag te beveiligen.
- Regelmatig worden gegevens uit de beveiligde opslag gelezen en wordt een back-up naar de Azure Encrypted Blob Store uitgevoerd.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>V: Hoeveel kosten Azure Storage nog als Storage Service Encryption is ingeschakeld?
A: Er zijn geen extra kosten.

### <a name="q-who-manages-the-encryption-keys"></a>V: Wie beheert de encryptiesleutels?
A: De sleutels worden beheerd door Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>V: Hoe vaak worden encryptiesleutels gedraaid?
A: Microsoft heeft een reeks interne richtlijnen voor het draaien van de encryptiesleutel, die Cosmos DB volgt. De specifieke richtlijnen worden niet gepubliceerd. Microsoft publiceert wel de [Security Development Lifecycle (SDL),](https://www.microsoft.com/sdl/default.aspx)die wordt gezien als een subset van interne richtlijnen en nuttige best practices heeft voor ontwikkelaars.

### <a name="q-can-i-use-my-own-encryption-keys"></a>V: Kan ik mijn eigen encryptiesleutels gebruiken?
A: Ja nu deze functie beschikbaar is voor de nieuwe cosmos accounts en dit moet worden gedaan op het moment van het maken van een account. Ga door het document [Van de klant beheerde sleutels](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk) voor meer informatie.

### <a name="q-what-regions-have-encryption-turned-on"></a>V: Welke regio's hebben versleuteling ingeschakeld?
A: Alle Azure Cosmos DB-regio's hebben versleuteling ingeschakeld voor alle gebruikersgegevens.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>V: Heeft versleuteling invloed op de prestatielatentie en doorvoerSLA's?
A: Er is geen impact of wijzigingen in de prestaties SLA's nu encryptie in rust is ingeschakeld voor alle bestaande en nieuwe accounts. U meer lezen op de [SLA voor Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) pagina om de nieuwste garanties te zien.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>V: Ondersteunt de lokale emulator encryptie in rust?
A: De emulator is een standalone dev/testtool en maakt geen gebruik van de belangrijkste beheerservices die de beheerde Cosmos DB-service gebruikt. Onze aanbeveling is om BitLocker in te schakelen op stations waar u gevoelige emulatortestgegevens opslaat. De [emulator ondersteunt het wijzigen van de standaard gegevensmap](local-emulator.md) en het gebruik van een bekende locatie.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Cosmos-databasebeveiliging](database-security.md)voor een overzicht van cosmos DB-beveiliging en de nieuwste verbeteringen.
Zie het [Azure Trust Center](https://azure.microsoft.com/support/trust-center/)voor meer informatie over Microsoft-certificeringen.
