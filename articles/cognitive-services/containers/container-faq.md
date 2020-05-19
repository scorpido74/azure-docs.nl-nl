---
title: Veelgestelde vragen over Cognitive Services containers
titleSuffix: Azure Cognitive Services
description: Veelgestelde vragen en antwoorden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 0e4f9da3aec42df42cc43f73fdeefa7087de6db5
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592182"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Veelgestelde vragen over Azure Cognitive Services-containers (FAQ)

## <a name="general-questions"></a>Algemene vragen

**V: wat is er beschikbaar?**

**A:** Met Azure Cognitive Services-containers kunnen ontwikkel aars gebruikmaken van dezelfde intelligente Api's die beschikbaar zijn in azure, maar met de [voor delen](../cognitive-services-container-support.md#features-and-benefits) van container opslag. Sommige containers zijn beschikbaar als [geteste preview](../cognitive-services-gating-process.md), waarvoor mogelijk een toepassing voor toegang is vereist. Andere containers zijn openbaar beschikbaar als een niet-gegate preview-versie of zijn algemeen beschikbaar. U kunt een volledige lijst met containers en hun Beschik baarheid vinden in de [container ondersteuning in Azure Cognitive Services](../cognitive-services-container-support.md#container-availability-in-azure-cognitive-services) -artikel. 

**V: is er een verschil tussen de Cognitive Services Cloud en de containers?**

**A:** Cognitive Services containers zijn een alternatief voor de Cognitive Services Cloud. Containers bieden dezelfde mogelijkheden als de bijbehorende Cloud Services. Klanten kunnen de containers on-premises of in azure implementeren. De kern-AI-technologie, de prijs categorieën, de API-sleutels en de API-hand tekening zijn hetzelfde als die van de container en de bijbehorende Cloud Services. Hier vindt u de [functies en voor delen](../cognitive-services-container-support.md#features-and-benefits) voor het kiezen van containers ten opzichte van hun vergelijk bare Cloud service.

**V: er zijn containers beschikbaar voor alle Cognitive Services en wat zijn de volgende sets containers die we moeten verwachten?**

**A:** We willen meer Cognitive Services beschikbaar maken als container aanbod. Neem contact op met uw lokale Microsoft-account manager om updates te ontvangen over nieuwe container releases en andere Cognitive Services aankondigingen.

**V: wat is de Service Level Agreement (SLA) voor Cognitive Services containers?**

**A:** Cognitive Services containers hebben geen SLA.

Cognitive Services container configuraties van bronnen worden beheerd door klanten, zodat micro soft geen SLA biedt voor algemene Beschik baarheid (GA). Klanten kunnen on-premises containers implementeren en daarom kunnen ze de host-omgevingen definiëren.

> [!IMPORTANT]
> [Bezoek onze Sla-pagina](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)voor meer informatie over Cognitive Services Service Level Agreements.

**V: zijn deze containers beschikbaar in soevereine Clouds?**

**A:** Niet iedereen is bekend met de term ' soevereine Cloud ', dus laten we beginnen met definitie:

> De "soevereine Cloud" bestaat uit de [Azure Government](../../azure-government/documentation-government-welcome.md), [Azure Duitsland](../../germany/germany-welcome.md)en [Azure China 21vianet](https://docs.microsoft.com/azure/china/overview-operations) -Clouds.

Helaas worden de Cognitive Services containers *niet* systeem eigen ondersteund in de soevereine Clouds. De containers kunnen worden uitgevoerd in deze Clouds, maar ze worden opgehaald uit de open bare Cloud en er moeten gebruiks gegevens naar het open bare eind punt worden verzonden.

### <a name="versioning"></a>Versiebeheer

**V: hoe worden containers bijgewerkt naar de nieuwste versie?**

**A:** Klanten kunnen kiezen wanneer ze de containers moeten bijwerken die ze hebben geïmplementeerd. Containers worden gemarkeerd met standaard- [docker-Tags](https://docs.docker.com/engine/reference/commandline/tag/) , zoals `latest` om de meest recente versie aan te geven. We moedigen klanten aan om de meest recente versie van containers te halen wanneer ze worden uitgebracht, [Azure container Registry webhooks](../../container-registry/container-registry-webhook.md) uit te checken voor meer informatie over hoe ze een melding krijgen wanneer een installatie kopie wordt bijgewerkt.
 
**V: welke versies worden ondersteund?**

**A:** De huidige en laatste primaire versie van de container worden ondersteund. We raden klanten echter aan om actueel te blijven om de nieuwste technologie te krijgen.
 
**V: hoe worden updates in versie nummer?**

**A:** Wijzigingen in de primaire versie geven aan dat er een wijziging in de API-hand tekening is opgesplitst. We verwachten dat dit doorgaans samen vallen met belang rijke versie wijzigingen in de bijbehorende Cloud aanbieding van de cognitieve service. Kleine versie wijzigingen geven aan dat fout oplossingen, model updates of nieuwe functies zijn die geen belang rijke wijziging aanbrengen in de API-hand tekening.

## <a name="technical-questions"></a>Technische vragen

**V: hoe moet ik de Cognitive Services-containers op IoT-apparaten uitvoeren?**

**A:** Of u geen betrouw bare Internet verbinding hebt of op kosten per band breedte wilt besparen. Of als er sprake is van vereisten met een lage latentie of als u wilt omgaan met gevoelige gegevens die on-site moeten worden geanalyseerd, kunt [Azure IOT Edge met de Cognitive Services containers](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) consistentie met de Cloud.

**V: zijn deze containers compatibel met openshift?** 

Containers worden niet getest met openshift, maar in het algemeen moeten Cognitive Services containers worden uitgevoerd op elk platform dat ondersteuning biedt voor docker-installatie kopieën. Als u openshift gebruikt, raden we u aan om de containers uit te voeren als `root-user` .

**V: Hoe kan ik de aanbevelingen voor product feedback en functies?**

**A:** Klanten worden geadviseerd om [hun bezorgdheid](https://cognitive.uservoice.com/) openbaar te maken en andere personen die hetzelfde doen hebben gedaan, wanneer potentiële problemen elkaar overlappen. Het hulp programma voor gebruikers spraak kan worden gebruikt voor de aanbevelingen voor de product feedback en functies.

**V: met wie moet ik contact opnemen voor ondersteuning?**

**A:** De ondersteunings kanalen van de klant zijn hetzelfde als de Cognitive Services Cloud-aanbieding. Alle Cognitive Services-containers bevatten logboek functies waarmee wij en de Community klanten kunnen ondersteunen. Zie de volgende opties voor aanvullende ondersteuning.

### <a name="customer-support-plan"></a>Abonnement voor klant ondersteuning

Klanten moeten hun [ondersteunings plan voor Azure](https://azure.microsoft.com/support/plans/) raadplegen om te zien wie er contact moet opnemen voor ondersteuning.

### <a name="azure-knowledge-center"></a>Azure-kennis centrum

Klanten kunnen het [Azure-kennis centrum](https://azure.microsoft.com/resources/knowledge-center/) verkennen voor het beantwoorden van vragen en ondersteunings problemen.

### <a name="stack-overflow"></a>Stackoverloop

> [Stack overflow](https://en.wikipedia.org/wiki/Stack_Overflow) is een vraag-en-antwoord site voor professionele en liefhebbers-programmeurs.

Bekijk de volgende tags voor mogelijke vragen en antwoorden die zijn afgestemd op uw behoeften.

* [Azure Cognitive Services](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Micro soft cognitieve](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**V: Hoe werkt de facturering?**

**A:** Klanten worden in rekening gebracht op basis van verbruik, vergelijkbaar met de Cognitive Services Cloud. De containers moeten worden geconfigureerd om meet gegevens naar Azure te verzenden en trans acties worden dienovereenkomstig gefactureerd. Resources die worden gebruikt voor de gehoste en on-premises Services, kunnen worden toegevoegd aan één quotum met prijzen per laag en worden geteld bij beide gebruik. Raadpleeg de pagina met prijzen van de bijbehorende aanbieding voor meer informatie.

* [Anomaly Detector][ad-containers-billing]
* [Computer Vision][cv-containers-billing]
* [Face][fa-containers-billing]
* [Form Recognizer][fr-containers-billing]
* [Language Understanding (LUIS)][lu-containers-billing]
* [Speech Service-API][sp-containers-billing]
* [Text Analytics][ta-containers-billing]

> [!IMPORTANT]
> Cognitive Services-containers mogen niet worden uitgevoerd zonder te zijn verbonden met Azure voor meting. Klanten moeten de containers in staat stellen om de facturerings gegevens te allen tijde met de meet service te communiceren. Cognitive Services containers verzenden geen klant gegevens naar micro soft.
 
**V: wat is de huidige ondersteunings garantie voor containers?**

**A:** Er is geen garantie voor previews. De standaard garantie voor zakelijke software van micro soft is van toepassing wanneer containers formeel worden aangekondigd als algemene Beschik baarheid (GA).
 
**V: wat gebeurt er met Cognitive Services containers wanneer de Internet verbinding is verbroken?**

**A:** Cognitive Services-containers mogen *niet* worden uitgevoerd zonder te zijn verbonden met Azure voor meting. Klanten moeten ervoor zorgen dat de containers te allen tijde met de meet service kunnen communiceren.

**V: hoe lang kan de container worden gebruikt zonder te zijn verbonden met Azure?**

**A:** Cognitive Services-containers mogen *niet* worden uitgevoerd zonder te zijn verbonden met Azure voor meting. Klanten moeten ervoor zorgen dat de containers te allen tijde met de meet service kunnen communiceren.
 
**V: wat is de huidige hardware die nodig is om deze containers uit te voeren?**

**A:** Cognitive Services containers zijn op x64 gebaseerde containers die elk compatibel Linux-knoop punt, een VM en een edge-apparaat kunnen uitvoeren dat ondersteuning biedt voor x64 Linux docker-containers. Alle vereisen CPU-processors. De minimale en aanbevolen configuraties voor elke container aanbieding zijn hieronder beschikbaar:

* [Anomaly Detector][ad-containers-recommendations]
* [Computer Vision][cv-containers-recommendations]
* [Face][fa-containers-recommendations]
* [Form Recognizer][fr-containers-recommendations]
* [Language Understanding (LUIS)][lu-containers-recommendations]
* [Speech Service-API][sp-containers-recommendations]
* [Text Analytics][ta-containers-recommendations]
 
**V: worden deze containers momenteel ondersteund in Windows?**

**A:** De Cognitive Services containers zijn Linux-containers, maar er is wel enige ondersteuning voor Linux-containers in Windows. Zie [docker-documentatie](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/)voor meer informatie over Linux-containers in Windows.
 
**V: Hoe kan ik de containers ontdekken?**

**A:** Cognitive Services containers zijn beschikbaar op verschillende locaties, zoals de Azure Portal, docker hub en Azure container registers. Raadpleeg [container opslagplaatsen en installatie kopieën](../cognitive-services-container-support.md#container-repositories-and-images)voor de meest recente container locaties.

**V: hoe worden Cognitive Services containers vergeleken met AWS-en Google-aanbiedingen?**

**A:** Micro soft is de eerste cloud provider om hun vooraf getrainde AI-modellen in containers te verplaatsen met een eenvoudige facturering per trans actie, alsof klanten een Cloud service gebruiken. Micro soft is van mening dat klanten een hybride Cloud meer keuze bieden.

**V: welke nalevings certificeringen hebben containers?**

**A:** Cognitieve Services-containers hebben geen compatibiliteits certificeringen

**V: in welke regio's zijn Cognitive Services containers beschikbaar?**

**A:** Containers kunnen overal in elke regio worden uitgevoerd, maar ze moeten een sleutel hebben en terug naar Azure voor meting. Alle ondersteunde regio's voor de Cloud service worden ondersteund voor het aanroepen van containers-licentie controle.

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations
