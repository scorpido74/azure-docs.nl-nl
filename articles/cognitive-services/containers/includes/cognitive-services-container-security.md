---
title: Beveiliging van de container
titleSuffix: Azure Cognitive Services
description: Meer informatie over het beveiligen van uw container
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/30/2019
ms.author: dapine
ms.openlocfilehash: 20f78d9269d4b2270293c8746157ba495c694562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272943"
---
## <a name="azure-cognitive-services-container-security"></a>Azure Cognitive Services-containerbeveiliging

Beveiliging moet een primaire focus zijn wanneer u toepassingen ontwikkelt. Het belang van veiligheid is een maatstaf voor succes. Wanneer u een softwareoplossing aanhet ontwerpen bent die containers voor Cognitive Services bevat, is het essentieel om de beperkingen en mogelijkheden te begrijpen die voor u beschikbaar zijn. Zie Virtuele netwerken van [Azure Cognitive Services configureren][az-security]voor meer informatie over netwerkbeveiliging.

> [!IMPORTANT]
> Standaard is er *geen beveiliging* op de Container API voor Cognitive Services. De reden hiervoor is dat de container meestal zal draaien als onderdeel van een pod die wordt beschermd van buitenaf door een netwerkbrug. Het is echter mogelijk om verificatie in te schakelen die identiek werkt aan de verificatie die wordt gebruikt bij de toegang tot de [cloudgebaseerde Cognitive Services.][request-authentication]

Het onderstaande diagram illustreert de standaard en **niet-veilige** benadering:

![Beveiliging van de container](../media/container-security.svg)

Als een alternatieve en *veilige* benadering kunnen consumenten van Cognitive Services-containers een container uitbreiden met een front-facing component, waardoor het containereindpunt privé blijft. Laten we eens kijken naar een scenario waarin we [Istio][istio] gebruiken als een ingress gateway. Istio ondersteunt HTTPS/TLS en client-certificate authenticatie. In dit scenario stelt de Istio frontend de containertoegang bloot en presenteert het clientcertificaat dat vooraf op de witte lijst staat met Istio.

[Nginx][nginx] is een andere populaire keuze in dezelfde categorie. Zowel Istio als Nginx fungeren als een servicemesh en bieden extra functies, waaronder load-balancing, routing en rate-control.

### <a name="container-networking"></a>Containernetwerken

De containers Cognitive Services zijn verplicht om meetgegevens in te dienen voor factureringsdoeleinden. De enige uitzondering is *Offline containers* omdat ze een andere factureringsmethode volgen. Als u de lijst niet toestaat, worden verschillende netwerkkanalen waarop de Cognitive Services-containers vertrouwen, voorkomen dat de container werkt.

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>Domeinen en poorten van lijst Cognitive Services toestaan

De host moet **lijstpoort 443** en de volgende domeinen toestaan:

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>Inspectie van deep packet uitschakelen

> [Deep packet inspection](https://en.wikipedia.org/wiki/Deep_packet_inspection) (DPI) is een type gegevensverwerking dat de gegevens die via een computernetwerk worden verzonden in detail inspecteert en meestal actie onderneemt door deze te blokkeren, opnieuw te routeren of dienovereenkomstig te registreren.

Schakel DPI uit op de beveiligde kanalen die de cognitive services-containers maken naar Microsoft-servers. Als u dit niet doet, wordt voorkomen dat de container goed functioneert.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md
