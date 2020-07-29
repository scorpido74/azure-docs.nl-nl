---
title: Probleem met aanvraag header oplossen te groot of 400 ongeldige aanvraag in Azure Cosmos DB
description: De aanvraag header diagnosticeren en verhelpen te grote uitzonde ring
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 942af52c43a1d4526a42f20df40abf3f510e0ee6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294428"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Fout bij het vaststellen en oplossen van de aanvraag header van Azure Cosmos DB te groot bericht
De aanvraag header te groot bericht wordt gegenereerd met een HTTP-fout code 400. Deze fout treedt op als de grootte van de aanvraag header te groot is en de Maxi maal toegestane grootte overschrijdt. U wordt aangeraden de nieuwste versie van de SDK te gebruiken. Zorg ervoor dat u ten minste versie 3. x of 2. x gebruikt, omdat deze versies het traceren van de header grootte aan het uitzonderings bericht toevoegen.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
De aanvraag header te groot bericht treedt op als de sessie of het vervolg token te groot is. In de volgende secties worden de oorzaak en oplossing voor het probleem in elke categorie beschreven.

### <a name="1-session-token-too-large"></a>1. sessie token te groot

#### <a name="cause"></a>Oorzaak:
een ongeldige aanvraag van 400 is hoogstwaarschijnlijk veroorzaakt door een grote sessie token. Als de volgende instructies zijn ingesteld op True, wordt bevestigd dat het sessie token te groot is.

* De fout treedt op bij een punt bewerking zoals maken, lezen, bijwerken en etc. Wanneer er geen vervolg token is.
* De uitzonde ring is gestart zonder dat er wijzigingen in de toepassing zijn aangebracht. Het sessie token groeit naarmate het aantal partities toeneemt in de container. De aantallen van de partitie toename naarmate de hoeveelheid gegevens toeneemt of als de door Voer is verhoogd.

#### <a name="temporary-mitigation"></a>Tijdelijke beperking: 
Start uw client toepassing opnieuw op om alle sessie tokens opnieuw in te stellen. Het sessie token zal uiteindelijk worden teruggebracht naar de vorige grootte waardoor het probleem is veroorzaakt. Gebruik daarom de oplossing in de volgende sectie om dit probleem volledig te voor komen.

#### <a name="solution"></a>Oplossing:
1. Volg de instructies in het artikel over prestatie tips voor [.net v3](performance-tips-dotnet-sdk-v3-sql.md) of [.NET v2](performance-tips.md) en converteer de toepassing voor gebruik van de modus directe verbinding met het TCP-protocol. De directe modus met het TCP-protocol heeft geen limiet voor de header grootte, zoals het HTTP-protocol, waardoor dit probleem wordt voor komen. Zorg ervoor dat u de nieuwste versie van SDK gebruikt. Deze bevat een correctie voor query bewerkingen wanneer de service Interop niet beschikbaar is.
2. Als de modus directe verbinding met het TCP-protocol geen optie is voor uw workload, vermindert u deze door het [consistentie niveau](how-to-manage-consistency.md)van de client te wijzigen. Het sessie token wordt alleen gebruikt voor sessie consistentie, het standaard consistentie niveau voor Azure Cosmos DB. Andere consistentie niveaus gebruiken niet het sessie token.

### <a name="2-continuation-token-too-large"></a>2. vervolg token te groot

#### <a name="cause"></a>Oorzaak:
De 400-ongeldige aanvraag gebeurt op query bewerkingen waarbij het vervolg token wordt gebruikt. Als het voortzettings token te groot is geworden of als verschillende query's verschillende voortzettings token groottes hebben.
    
#### <a name="solution"></a>Oplossing:
1. Volg de instructies in het artikel over prestatie tips voor [.net v3](performance-tips-dotnet-sdk-v3-sql.md) of [.NET v2](performance-tips.md) en converteer de toepassing voor gebruik van de modus directe verbinding met het TCP-protocol. De directe modus met het TCP-protocol heeft geen limiet voor de header grootte, zoals het HTTP-protocol, waardoor dit probleem wordt voor komen. 
3. Als de modus directe verbinding met het TCP-protocol geen optie is voor uw workload, kunt u de `ResponseContinuationTokenLimitInKb` optie instellen. U vindt deze optie in de `FeedOptions` for v2-versie of `QueryRequestOptions` in v3.

## <a name="next-steps"></a>Volgende stappen
* Problemen [vaststellen en oplossen](troubleshoot-dot-net-sdk.md) bij het gebruik van Azure Cosmos db .NET SDK
* Meer informatie over prestatie richtlijnen voor [.net v3](performance-tips-dotnet-sdk-v3-sql.md) en [.NET v2](performance-tips.md)
