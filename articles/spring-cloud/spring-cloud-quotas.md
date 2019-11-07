---
title: Service plannen en quota's voor Azure lente-Cloud
description: Meer informatie over service quota's en service plannen voor Azure lente Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 41a2b1a7d9aa5089ba2ee73cd3c5c5c5e31f5225
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607674"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Quota's en service plannen voor Azure lente-Cloud

Alle Azure-Services hebben standaard limieten en-quota ingesteld voor resources en functies.  Tijdens de preview-periode biedt Azure lente-Cloud slechts één service plan.

In dit artikel vindt u informatie over de service quota's die tijdens de huidige preview-periode beschikbaar worden gesteld.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure lente-Cloud service lagen en-quota's

Tijdens de preview-periode biedt Azure lente-Cloud slechts één servicelaag.

Resource | Aantal
------- | -------
vCPU | 4
Geheugen | 8 GBytes
Azure lente-Cloud abonnement | 1
Azure veer Cloud service-instanties per regio per abonnement | 2
Totaal aantal app-exemplaren per Azure veer Cloud service-exemplaar | 50
Totaal aantal app-exemplaren per lente toepassing | 20
Permanente volumes | 10 x 50 GBytes

Wanneer u een quotum bereikt, ontvangt u een 400-fout die lees bewerkingen: ' quotum overschrijdt de limiet voor het abonnement op *uw abonnement* in regio regio *waar uw Azure lente-Cloud service wordt gemaakt*.

## <a name="next-steps"></a>Volgende stappen

Bepaalde standaardlimieten en -quota kunnen worden verhoogd. Als voor uw resource een verhoging is vereist, [maakt u een ondersteunings aanvraag](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).
