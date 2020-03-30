---
title: Inzicht in Azure CDN-facturering | Microsoft Documenten
description: In deze veelgestelde vraag wordt beschreven hoe Azure CDN-facturering werkt.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2019
ms.author: magattus
ms.openlocfilehash: e2827a11f4ec2a5c0467c3699cd9990aaf7ae97a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73495475"
---
# <a name="understanding-azure-cdn-billing"></a>Inzicht in facturering voor Azure CDN

In deze veelgestelde vraag wordt de factureringsstructuur beschreven voor inhoud die wordt gehost door het Azure Content Delivery Network (CDN).

## <a name="what-is-a-billing-region"></a>Wat is een factureringsgebied?
Een factureringsgebied is een geografisch gebied dat wordt gebruikt om te bepalen welk tarief in rekening wordt gebracht voor de levering van objecten uit Azure CDN. De huidige factureringszones en hun regio's zijn als volgt:

- Zone 1: Noord-Amerika, Europa, Midden-Oosten en Afrika

- Zone 2: Azië-Pacific (inclusief Japan)

- Zone 3: Zuid-Amerika

- Zone 4: Australië en Nieuw-Zeeland

- Zone 5: India

Zie [Azure CDN POP-locaties per regio](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)voor informatie over popgebieden (point-of-presence). Een POP in Mexico bevindt zich bijvoorbeeld in de regio Noord-Amerika en is daarom opgenomen in zone 1. 

Zie [Content Delivery Network-prijzen](https://azure.microsoft.com/pricing/details/cdn/)voor informatie over azure CDN-prijzen.

## <a name="how-are-delivery-charges-calculated-by-region"></a>Hoe worden de leveringskosten berekend per regio?
Het factureringsgebied Azure CDN is gebaseerd op de locatie van de bronserver die de inhoud aan de eindgebruiker levert. De bestemming (fysieke locatie) van de klant wordt niet beschouwd als het factureringsgebied.

Als een gebruiker in Mexico bijvoorbeeld een aanvraag uitgeeft en dit verzoek wordt onderhouden door een server in een POP in de Verenigde Staten vanwege peering- of verkeersomstandigheden, zijn de factureringsregio de Verenigde Staten.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Wat is een factureerbare Azure CDN-transactie?
Elk HTTP(S)-verzoek dat eindigt op het CDN is een factable event, die alle antwoordtypen omvat: succes, mislukking of andere. Verschillende reacties kunnen echter verschillende verkeersbedragen genereren. *304 Niet-gewijzigde* en andere header-only reacties genereren bijvoorbeeld weinig verkeer omdat ze een kleine header-respons zijn; ook foutreacties (bijvoorbeeld *404 Not Found)* zijn factureerbaar, maar maken kleine kosten met zich mee vanwege de kleine responspayload.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Welke andere Azure-kosten zijn gekoppeld aan het gebruik van Azure CDN?
Het gebruik van Azure CDN brengt ook enkele gebruikskosten met zich mee voor de services die worden gebruikt als de oorsprong voor uw objecten. Deze kosten zijn meestal een klein deel van de totale CDN-gebruikskosten.

Als u Azure Blob-opslag gebruikt als oorsprong voor uw inhoud, worden er ook de volgende opslagkosten in rekening gebracht voor cachevullingen:

- Werkelijke GB gebruikt: de werkelijke opslag van uw bronobjecten.

- Transacties: Indien nodig om de cache te vullen.

- Overdrachten in GB: de hoeveelheid gegevens die wordt overgedragen om de CDN-caches te vullen.

> [!NOTE]
> Vanaf oktober 2019 zijn de kosten van gegevensoverdracht van Origins die in Azure worden gehost naar CDN-pop's gratis als u Azure CDN van Microsoft gebruikt. Azure CDN van Verizon en Azure CDN van Akamai zijn onderworpen aan de onderstaande tarieven.

Zie [Azure Storage Billing – Bandbreedte, Transacties en capaciteit begrijpen](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/)voor meer informatie over facturering met Azure Storage.

Als u de levering van *de gehoste service*gebruikt, brengt u als volgt kosten in rekening:

- Azure-rekentijd: de rekeninstanties die als oorsprong fungeren.

- Azure-compute transfer: de gegevensoverdrachten van de compute-instanties om de Azure CDN-caches te vullen.

Als uw klant aanvragen voor bytebereik gebruikt (ongeacht de oorsprongsservice), zijn de volgende overwegingen van toepassing:

- Een *byte-range aanvraag* is een factureerbare transactie bij het CDN. Wanneer een client een byte-range-aanvraag uitgeeft, is deze aanvraag voor een subset (bereik) van het object. Het CDN reageert met slechts een gedeeltelijk deel van de gevraagde inhoud. Deze gedeeltelijke reactie is een factureerbare transactie en het overdrachtsbedrag is beperkt tot de grootte van de bereikrespons (plus kopteksten).

- Wanneer een aanvraag slechts voor een deel van een object binnenkomt (door een byte-range-header op te geven), kan het CDN het hele object in de cache ophalen. Als gevolg hiervan, hoewel de factureerbare transactie van het CDN is voor een gedeeltelijke reactie, de factureerbare transactie van de oorsprong kan betrekking hebben op de volledige grootte van het object.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Hoeveel overdrachtsactiviteit vindt er plaats om de cache te ondersteunen?
Elke keer dat een CDN POP zijn cache moet vullen, wordt een verzoek ingediend bij de oorsprong van het object dat in de cache wordt opgeslagen. Als gevolg hiervan, de oorsprong maakt een factureerbare transactie op elke cache missen. Het aantal cachemissers is afhankelijk van een aantal factoren:

- Hoe cacheable de inhoud is: Als de inhoud hoge TTL (time-to-live)/expiratiewaarden heeft en vaak wordt geopend, zodat deze populair blijft in de cache, wordt het overgrote deel van de belasting afgehandeld door het CDN. Een typische goede cache-hit ratio is ruim 90%, wat betekent dat minder dan 10% van de clientaanvragen terug moeten keren naar de oorsprong, hetzij voor een cache miss of object refresh.

- Hoeveel knooppunten het object moeten laden: elke keer dat een knooppunt een object van de oorsprong laadt, wordt een factureerbare transactie gemaakt. Als gevolg hiervan resulteert meer globale inhoud (toegankelijk vanaf meer knooppunten) in meer factureerbare transacties.

- TTL-invloed: Een hogere TTL voor een object betekent dat het minder vaak van de oorsprong moet worden gehaald. Het betekent ook dat clients, zoals browsers, het object langer in de cache kunnen opslaan, waardoor de transacties naar het CDN kunnen worden verminderd.

## <a name="which-origin-services-are-eligible-for-free-data-transfer-with-azure-cdn-from-microsoft"></a>Welke origin-services komen in aanmerking voor gratis gegevensoverdracht met Azure CDN van Microsoft? 
Als u een van de volgende Azure-services als cdn-oorsprong gebruikt, worden er geen kosten in rekening gebracht van Gegevensoverdracht van Origin naar de CDN-pop's. 

- Azure Storage
- Azure Media Services
- Azure Virtual Machines
- Virtual Network
- Load balancer
- Application Gateway
- Azure DNS
- ExpressRoute
- VPN Gateway
- Traffic Manager
- Network Watcher
- Azure Firewall
- Azure Front Door Service
- Azure Bastion
- Azure App-service
- Azure Functions
- Azure Data Factory
- Azure API Management
- Azure Batch 
- Azure Data Explorer
- HDInsight
- Azure Cosmos DB
- Azure Data Lake Store
- Azure Machine Learning 
- Azure SQL-database
- Azure Cache voor Redis

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Hoe kan ik mijn kosten het meest effectief beheren?
Stel de langste TTL mogelijk op uw inhoud. 
