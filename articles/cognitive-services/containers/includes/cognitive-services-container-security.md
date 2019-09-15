---
title: Beveiliging van de container
titleSuffix: Azure Cognitive Services
description: Meer informatie over het beveiligen van uw container
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: dapine
ms.openlocfilehash: d8d069dddbce6ab6ddb541db460634ad3f6fa067
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70994961"
---
## <a name="azure-cognitive-services-container-security"></a>Azure Cognitive Services-container beveiliging

De beveiliging moet een primaire focus hebben wanneer u toepassingen ontwikkelt. Het belang van beveiliging is een metrische waarde voor geslaagde pogingen. Wanneer u een software oplossing met Cognitive Services containers ontwikkelt, is het belang rijk om inzicht te krijgen in de beperkingen en mogelijkheden die voor u beschikbaar zijn. Zie [Azure Security][az-security]voor meer informatie.

> [!IMPORTANT]
> Standaard is er *geen beveiliging* op de Cognitive Services container-API. De reden hiervoor is dat de container meestal wordt uitgevoerd als onderdeel van een pod die wordt beveiligd door een netwerk brug. Het is echter mogelijk om verificatie in te scha kelen die identiek werkt met de authenticatie die wordt gebruikt bij het openen [van de cloud Cognitive Services][request-authentication].

In het onderstaande diagram ziet u de standaard-en **beveiligings** methode:

![Beveiliging van de container](../media/container-security.svg)

Gebruikers van Cognitive Services containers kunnen een andere en *veilige* benadering van een container met een front-facing component uitbreiden, zodat het container eindpunt persoonlijk blijft. Laten we eens kijken naar een scenario waarin [Istio][istio] wordt gebruikt als een ingangs gateway. Istio ondersteunt HTTPS/SSL en verificatie van client certificaten. In dit scenario wordt de toegang tot de container door de Istio-front-front-front-end weer gegeven, waarbij het client certificaat wordt gepresenteerd dat vooraf is white list met Istio.

[Nginx][nginx] is een andere populaire keuze in dezelfde categorie. Zowel Istio als nginx fungeren als een service-net en bieden extra functies, zoals taak verdeling, route ring en frequentie beheer.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../../security/fundamentals/overview.md
