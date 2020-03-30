---
title: Gedeelde verantwoordelijkheid in de cloud - Microsoft Azure
description: Inzicht krijgen in het gedeelde verantwoordelijkheidsmodel en welke beveiligingstaken worden uitgevoerd door de cloudprovider en welke taken door u worden uitgevoerd.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: na
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2019
ms.author: terrylan
ms.openlocfilehash: 8f16105d6bda1798828bc423ec8a158d49e0cf2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518367"
---
# <a name="shared-responsibility-in-the-cloud"></a>Gedeelde verantwoordelijkheid in de cloud

Wanneer u openbare cloudservices overweegt en evalueert, is het van cruciaal belang om inzicht te krijgen in het gedeelde verantwoordelijkheidsmodel en welke beveiligingstaken door de cloudprovider worden afgehandeld en welke taken door u worden uitgevoerd. De verantwoordelijkheden voor werkbelasting variëren afhankelijk van of de werkbelasting wordt gehost op Software as a Service (SaaS), Platform as a Service (PaaS), Infrastructure as a Service (IaaS) of in een on-premises datacenter

## <a name="division-of-responsibility"></a>Verdeling van de verantwoordelijkheid
In een on-premises datacenter bent u eigenaar van de hele stack. Als u naar de cloud gaat, worden sommige verantwoordelijkheden overgedragen aan Microsoft. In het volgende diagram worden de verantwoordelijkheden tussen u en Microsoft geïllustreerd, afhankelijk van het type implementatie van uw stack.

![Verantwoordelijkheidszones](./media/shared-responsibility/shared-responsibility.png)

Voor elk type cloudimplementatie bent u eigenaar van uw gegevens en identiteiten. U bent verantwoordelijk voor de bescherming van de beveiliging van uw gegevens en identiteiten, on-premises resources en de cloudcomponenten die u beheert (die per servicetype verschilt).

Ongeacht het type implementatie, worden de volgende verantwoordelijkheden altijd door u behouden:

- Gegevens
- Eindpunten
- Account
- Toegangsbeheer

## <a name="cloud-security-advantages"></a>Voordelen voor cloudbeveiliging
De cloud biedt aanzienlijke voordelen voor het oplossen van langdurige uitdagingen op het gebied van informatiebeveiliging. In een on-premises omgeving hebben organisaties waarschijnlijk onvervulde verantwoordelijkheden en beperkte middelen beschikbaar om te investeren in beveiliging, waardoor een omgeving ontstaat waarin aanvallers kwetsbaarheden in alle lagen kunnen misbruiken.

Het volgende diagram toont een traditionele benadering waarbij veel beveiligingsverantwoordelijkheden niet worden nageleefd vanwege beperkte resources. In de cloud-enabled aanpak, bent u in staat om dag tot dag beveiligingsverantwoordelijkheden verschuiven naar uw cloud provider en herverdelen van uw middelen.

![Beveiligingsvoordelen van cloudtijdperk](./media/shared-responsibility/cloud-enabled-security.png)

In de cloud-enabled aanpak, bent u ook in staat om cloud-gebaseerde beveiligingsmogelijkheden te benutten voor meer effectiviteit en gebruik maken van cloud intelligence om uw bedreigingdetectie en responstijd te verbeteren. Door verantwoordelijkheden te verschuiven naar de cloudprovider, kunnen organisaties meer beveiligingsdekking krijgen, waardoor ze beveiligingsresources en budget kunnen herverdelen naar andere zakelijke prioriteiten.

## <a name="next-steps"></a>Volgende stappen
Zie [Gedeelde verantwoordelijkheden voor cloud computing voor](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)meer informatie over de verdeling van de verantwoordelijkheid tussen u en Microsoft in een SaaS-, PaaS- en IaaS-implementatie.
