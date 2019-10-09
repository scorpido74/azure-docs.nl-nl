---
title: Service plannen en quota's voor Azure lente-Cloud
description: Meer informatie over service quota's en service plannen voor Azure lente Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 9/27/2019
ms.author: jeconnoc
ms.openlocfilehash: 89934ee0ab0c901a904a1a5ac2fb620185571a23
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038780"
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

Wanneer u een quotum bereikt, ontvangt u een 400-fout die het volgende leest: "Quotum overschrijdt de limiet voor *het abonnement op uw abonnement* in regio *regio waar uw Azure lente-Cloud service wordt gemaakt*.

## <a name="next-steps"></a>Volgende stappen

Bepaalde standaardlimieten en -quota kunnen worden verhoogd. Als uw resource een verhoging vereist, stuurt u ons uw aanvraag: azure-spring-cloud@service.microsoft.com.
