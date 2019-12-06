---
title: Service plannen en quota's voor Azure lente-Cloud
description: Meer informatie over service quota's en service plannen voor Azure lente Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 0518b13ea228b4834a095a9bf126b131e70a5f45
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851550"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Quota's en service plannen voor Azure lente-Cloud

Alle Azure-Services hebben standaard limieten en-quota ingesteld voor resources en functies.  Tijdens de preview-periode biedt Azure lente-Cloud slechts één service plan.

In dit artikel vindt u informatie over de service quota's die tijdens de huidige preview-periode beschikbaar worden gesteld.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure lente-Cloud service lagen en-quota's

Tijdens de preview-periode biedt Azure lente-Cloud slechts één servicelaag.

Bron | Bedrag
------- | -------
vCPU | 4 per service-exemplaar
Geheugen | 8 GBytes per service-exemplaar
Azure veer Cloud service-instanties per regio per abonnement | 10
Totaal aantal app-exemplaren per Azure veer Cloud service-exemplaar | 500
Totaal aantal app-exemplaren per lente toepassing | 20
Permanente volumes | 10 x 50 GBytes

Wanneer u een quotum bereikt, ontvangt u een 400-fout die lees bewerkingen: ' quotum overschrijdt de limiet voor het abonnement op *uw abonnement* in regio regio *waar uw Azure lente-Cloud service wordt gemaakt*.

## <a name="next-steps"></a>Volgende stappen

Bepaalde standaardlimieten en -quota kunnen worden verhoogd. Als voor uw resource een verhoging is vereist, [maakt u een ondersteunings aanvraag](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).
