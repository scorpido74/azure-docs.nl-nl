---
title: Veelgestelde vragen over Azure peering-service
description: Veelgestelde vragen over het Microsoft Azure van peering-service
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 55c5e6c5b718dc2de295b9b4418ddc8607a69f8f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "84872048"
---
# <a name="peering-service-faq"></a>Veelgestelde vragen over peering-service

In dit artikel worden de veelgestelde vragen over Azure peering service-verbindingen beschreven.


**V. wie zijn de doel klanten?**

A. Doel klanten zijn ondernemingen die verbinding maken met micro soft Cloud via internet als Trans Port.

**V. kunnen klanten zich aanmelden voor een peering-service met meerdere providers?** 

A. Ja, klanten kunnen zich aanmelden voor de peering-service met meerdere providers in dezelfde regio of in verschillende regio's, maar niet voor hetzelfde voor voegsel.

**V. kunnen klanten een unieke Internet provider selecteren voor hun sites per geografische regio?**

A. Ja, klanten kunnen dit doen. Selecteer de partner-ISP per regio die tegemoetkomt aan uw bedrijf en operationele behoeften.

**V. Wat is een micro soft Edge-PoP?**

A. Het is een fysieke locatie waar micro soft verbinding maakt met andere netwerken. In de micro soft Edge PoP-locatie worden services, zoals Azure front deur en Azure CDN, gehost. Zie [Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-features)voor meer informatie.

## <a name="peering-service-unique-characteristics"></a>Peering-service: unieke kenmerken

**V. Hoe wijkt de peering-service af van de normale Internet toegang?**

A. Partners die zijn geregistreerd bij micro soft-peering service, werken samen met micro soft om geoptimaliseerde route ring en betrouw bare connectiviteit met micro soft-services te bieden.  

**V. Hoe wijkt de peering-service af van ExpressRoute?**

A. Azure ExpressRoute is een privé, exclusieve verbinding van een of meer klant locaties. Hoewel peering-service geoptimaliseerde open bare connectiviteit biedt en geen persoonlijke connectiviteit ondersteunt, biedt het ook een geoptimaliseerde connectiviteit voor lokale Internet-breakouts.

## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van peering service](about.md)voor meer informatie over peering-service.
- Zie [peering service-partners en-locaties](location-partners.md)om een service provider te vinden.
- Zie voor het onboarden van een peering-service verbinding [onboarding-peering-service](onboarding-model.md).
- Zie [een peering service-verbinding registreren-Azure Portal](azure-portal.md)als u een peering service-verbinding wilt registreren.
- Zie [verbindings-telemetrie meten](measure-connection-telemetry.md)om telemetrie te meten.