---
title: Serviceplannen en quota voor Azure Spring Cloud
description: Meer informatie over servicequota en serviceplannen voor Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278892"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Quota en serviceplannen voor Azure Spring Cloud

Alle Azure-services stellen standaardlimieten en quota in voor resources en functies.  Tijdens de previewperiode biedt Azure Spring Cloud slechts één serviceplan.

In dit artikel worden de servicequota beschreven die tijdens de huidige voorbeeldperiode worden aangeboden.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure Spring Cloud-servicelagen en -quota

Tijdens de previewperiode biedt Azure Spring Cloud slechts één servicelaag.

Resource | Aantal
------- | -------
vCPU | 4 per service-exemplaar
Geheugen | 8 GBytes per service-instantie
Azure Spring Cloud-service-exemplaren per regio per abonnement | 10
Totaal aantal app-exemplaren per Azure Spring Cloud-serviceinstantie | 500
Totaal aantal app-exemplaren per voorjaarstoepassing | 20
Permanente volumes | 10 x 50 GBytes

Wanneer u een quotum bereikt, ontvangt u een fout van 400 met de tekst: 'Quotum overschrijdt limiet voor abonnement *uw abonnement* in *regioregio's waar uw Azure Spring Cloud-service is gemaakt.*

## <a name="next-steps"></a>Volgende stappen

Bepaalde standaardlimieten en -quota kunnen worden verhoogd. Als uw resource een verhoging vereist, [maakt u een ondersteuningsverzoek](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
