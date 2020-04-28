---
title: Service plannen en quota's voor Azure lente-Cloud
description: Meer informatie over service quota's en service plannen voor Azure lente Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76278892"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Quota's en service plannen voor Azure lente-Cloud

Alle Azure-Services hebben standaard limieten en-quota ingesteld voor resources en functies.  Tijdens de preview-periode biedt Azure lente-Cloud slechts één service plan.

In dit artikel vindt u informatie over de service quota's die tijdens de huidige preview-periode beschikbaar worden gesteld.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure lente-Cloud service lagen en-quota's

Tijdens de preview-periode biedt Azure lente-Cloud slechts één servicelaag.

Resource | Aantal
------- | -------
vCPU | 4 per service-exemplaar
Geheugen | 8 GBytes per service-exemplaar
Azure veer Cloud service-instanties per regio per abonnement | 10
Totaal aantal app-exemplaren per Azure veer Cloud service-exemplaar | 500
Totaal aantal app-exemplaren per lente toepassing | 20
Permanente volumes | 10 x 50 GBytes

Wanneer u een quotum bereikt, ontvangt u een 400-fout die lees bewerkingen: ' quotum overschrijdt de limiet voor het abonnement op *uw abonnement* in regio regio *waar uw Azure lente-Cloud service wordt gemaakt*.

## <a name="next-steps"></a>Volgende stappen

Bepaalde standaardlimieten en -quota kunnen worden verhoogd. Als voor uw resource een verhoging is vereist, [maakt u een ondersteunings aanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
