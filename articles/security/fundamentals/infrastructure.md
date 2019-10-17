---
title: Azure-infrastructuur beveiliging | Microsoft Docs
description: In dit artikel wordt beschreven hoe micro soft werkt om onze Azure-data centers te beveiligen.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: a17d98d49d2c653e2498a663829d26e8a171fd74
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72433506"
---
# <a name="azure-infrastructure-security"></a>Azure-infrastructuur beveiliging
Microsoft Azure wordt uitgevoerd in data centers die worden beheerd en beheerd door micro soft. Deze geografisch verspreide data centers voldoen aan de belangrijkste industrie normen, zoals ISO/IEC 27001:2013 en NIST SP 800-53, voor beveiliging en betrouw baarheid. De data centers worden beheerd, bewaakt en beheerd door micro soft-werk personeel. De werk nemers hebben jaren ervaring met het leveren van de grootste onlineservices ter wereld met een continuïteit van 24 x 7.

In deze reeks artikelen vindt u informatie over wat micro soft doet bij het beveiligen van de Azure-infra structuur. Het artikel adres:

- [Fysieke beveiliging](physical-security.md)
- [Beschikbaarheid](infrastructure-availability.md)
- [Onderdelen en grenzen](infrastructure-components.md)
- [Netwerkarchitectuur](infrastructure-network.md)
- [Productie netwerk](production-network.md)
- [SQL Database](infrastructure-sql.md)
- [Bewerkingen](infrastructure-operations.md)
- [Controle](infrastructure-monitoring.md)
- [Volledig](infrastructure-integrity.md)
- [Gegevensbeveiliging](protection-customer-data.md)

## <a name="shared-responsibility-model"></a>Model van gedeelde verantwoordelijkheid
Het is belang rijk om inzicht te krijgen in de verantwoordelijkheid tussen u en micro soft. U bent eigenaar van de hele stack, maar wanneer u overstapt naar de Cloud, worden sommige verantwoordelijkheden overgezet naar micro soft. In de volgende afbeelding ziet u de verantwoordelijkheids gebieden, op basis van het type implementatie van uw stack (Software as a service [SaaS], platform as a service [PaaS], Infrastructure as a service [IaaS] en on-premises).

![Grafische weer gave van verantwoordelijkheden](./media/infrastructure/responsibility-zones.png)

U bent altijd verantwoordelijk voor het volgende, ongeacht het type implementatie:

- Gegevens
- Eindpunten
- Account
- Toegangsbeheer

Zorg ervoor dat u bekend bent met de verantwoordelijkheid van de verantwoordelijkheden tussen u en micro soft in een SaaS-, PaaS-en IaaS-implementatie. Zie [gedeelde verantwoordelijkheden voor Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225237/1/Shared%20Responsibilities%20for%20Cloud%20Computing%20(2017-04-03).pdf)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over wat micro soft doet bij het beveiligen van de Azure-infra structuur:

- [Azure-faciliteiten,-locaties en fysieke beveiliging](physical-security.md)
- [Beschik baarheid van Azure-infra structuur](infrastructure-availability.md)
- [Azure Information System-onderdelen en-grenzen](infrastructure-components.md)
- [Azure-netwerk architectuur](infrastructure-network.md)
- [Productie netwerk van Azure](production-network.md)
- [Azure SQL Database beveiligings functies](infrastructure-sql.md)
- [Azure-productie bewerkingen en-beheer](infrastructure-operations.md)
- [Bewaking van Azure-infra structuur](infrastructure-monitoring.md)
- [Integriteit van Azure-infra structuur](infrastructure-integrity.md)
- [Azure-klant gegevens beveiliging](protection-customer-data.md)


