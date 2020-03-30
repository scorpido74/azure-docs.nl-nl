---
title: Peering Service - Veelgestelde vragen
titleSuffix: Azure
description: Peering Service - Veelgestelde vragen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775470"
---
# <a name="peering-service---faqs"></a>Peering Service - Veelgestelde vragen

U onderstaande informatie bekijken voor algemene vragen.

**Kan een provider hun bestaande Direct-peering met Microsoft gebruiken om Peering Service te ondersteunen?**

Ja, een provider kan zijn bestaande PNI gebruiken om Peering Service te ondersteunen. Een Peering Service PNI vereist diversiteit om HA te ondersteunen. Als bestaande PNI al diversiteit heeft, is er geen nieuwe infrastructuur nodig. Als bestaande PNI diversiteit nodig heeft, kan het worden uitgebreid.

**Kan een provider nieuwe Direct-peering met Microsoft gebruiken om Peering Service te ondersteunen?**

Ja, dat is ook mogelijk. Microsoft werkt samen met Carrier om nieuwe Direct-peering te maken ter ondersteuning van Peering Service.  

**Waarom is Direct peering een vereiste om Peering Service te ondersteunen?**

Een van de belangrijkste drijfveren achter Peering Service is het bieden van connectiviteit met online services van Microsoft via een goed verbonden SP. PNI zijn altijd in Gbps bereik en dus een fundamentele bouwsteen voor hoge doorvoer connectiviteit tussen vervoerder en Microsoft.

**Wat zijn de diversiteitsvereisten voor een Direct-peering ter ondersteuning van Peering Service?**

Een PNI moet lokaal redundantie en georedundantie ondersteunen. Lokale redundantie wordt gedefinieerd als twee verschillende paden in een bepaalde peeringsite. Geo-redundantie vereist dat Carrier extra connectiviteit heeft op een andere Microsoft edge-site voor het geval de primaire site uitvalt. Voor de korte storingsduur kan de vervoerder het verkeer door de back-upsite leiden.

**De vervoerder biedt al SLA en enterprise grade Internet, hoe is dit aanbod anders?**

Sommige providers bieden SLA en enterprise grade Internet op hun deel van het netwerk. In Peering Service biedt Microsoft SLA-aanbiedingsverkeer op Microsoft-onderdeel van het netwerk. Door peering service te selecteren krijgt de klant end-to-end SLA. SLA van hun site naar Microsoft edge op ISP-netwerk kan worden gedekt door de ISP. SLA in Microsoft Global Network van Microsoft edge naar end users applicatie wordt nu gedekt door Microsoft.

**Als een serviceprovider al peers met Microsoft met behulp van PNI dan wat voor soort wijzigingen nodig zijn om Peering Service te ondersteunen?**

* Software wijzigingen om een Peering Service gebruiker en het verkeer te identificeren. Mogelijk moeten wijzigingen in het routeringsbeleid worden gewijzigd om het verkeer van een gebruiker op de dichtstbijzijnde Microsoft-rand uit te wisselen via peeringserviceverbinding.
* Zorg ervoor dat de connectiviteit lokaal redundantie en georedundantie heeft.
