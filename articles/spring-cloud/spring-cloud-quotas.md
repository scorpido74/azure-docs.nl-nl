---
title: Service plannen en quota's voor Azure lente-Cloud
description: Meer informatie over service quota's en service plannen voor Azure lente Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: cda22c63a70c5121e6a6972c66bdc0a4bb5158fc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089461"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Quota's en service plannen voor Azure lente-Cloud

Alle Azure-Services hebben standaard limieten en-quota ingesteld voor resources en functies.   Azure lente-Cloud biedt twee prijs Categorieën: Basic en Standard. In dit artikel worden de limieten voor beide lagen weer gegeven.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Azure lente-Cloud service lagen en-limieten

| Resource | Basic | Standard
------- | ------- | -------
vCPU | 1 per service-exemplaar | 4 per service-exemplaar
Geheugen | 2 GB per service-exemplaar | 8 GB per service-exemplaar
Azure veer Cloud service-instanties per regio per abonnement | 10 | 10
Totaal aantal app-exemplaren per Azure veer Cloud service-exemplaar | 25 | 500
Permanente volumes | 1 GB/app x 10 apps | 50 GB/app x 10-apps


Tijdens de preview-periode biedt Azure lente-Cloud slechts één servicelaag. Wanneer u een limiet bereikt, ontvangt u een 400-fout die lees bewerkingen: ' quotum overschrijdt de limiet voor het abonnement op *uw abonnement* in regio regio *waar uw Azure lente-Cloud service wordt gemaakt*.

## <a name="next-steps"></a>Volgende stappen

Enkele standaard limieten kunnen worden verhoogd. Als uw installatie een verhoging vereist, [maakt u een ondersteunings aanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
