---
title: Cognitive Services containers veelgestelde vragen (FAQ)
titleSuffix: Azure Cognitive Services
description: Veelgestelde vragen en antwoorden.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: dapine
ms.openlocfilehash: 33b99d50db4384c7de818a7dd0bb8492c86bef97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73961881"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Veelgestelde vragen over Azure Cognitive Services-containers (FAQ)

## <a name="general-questions"></a>Algemene vragen

**V: Wat is er beschikbaar?**

**A: Met** [containerondersteuning in Azure Cognitive Services](../cognitive-services-container-support.md) kunnen ontwikkelaars dezelfde intelligente API's gebruiken die beschikbaar zijn in Azure, maar met de [voordelen](../cognitive-services-container-support.md#features-and-benefits) van containerisatie. Containerondersteuning is momenteel beschikbaar in preview voor een subset van Azure Cognitive Services, inclusief delen van:

> [!div class="checklist"]
> * [Anomaly Detector][ad-containers]
> * [Computer Visie][cv-containers]
> * [Face][fa-containers]
> * [Form Recognizer][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Speech Service-API][sp-containers]
> * [Tekstanalyse][ta-containers]

**V: Is er een verschil tussen de Cognitive Services cloud en de containers?**

**A:** Cognitive Services-containers zijn een alternatief voor de Cognitive Services-cloud. Containers bieden dezelfde mogelijkheden als de bijbehorende cloudservices. Klanten kunnen de containers on-premises of in Azure implementeren. De belangrijkste AI-technologie, prijsniveaus, API-sleutels en API-handtekening zijn hetzelfde tussen de container en de bijbehorende cloudservices. Hier volgen de [functies en voordelen](../cognitive-services-container-support.md#features-and-benefits) voor het kiezen van containers boven hun cloudservice-equivalent.

**V: Zijn containers beschikbaar voor alle Cognitive Services en wat zijn de volgende set containers die we mogen verwachten?**

**A:** We willen graag meer Cognitive Services beschikbaar stellen als containeraanbod. Neem contact op met uw lokale Microsoft-accountmanager om updates te ontvangen over nieuwe containerreleases en andere aankondigingen van Cognitive Services.

**V: Wat wordt de SERVICE-Level Agreement (SLA) voor containers op cognitief services?**

**A:** Cognitive Services-containers hebben geen SLA.

De containerconfiguraties van cognitive services worden gecontroleerd door klanten, dus Microsoft biedt geen SLA voor algemene beschikbaarheid (GA). Klanten zijn vrij om containers on-premises te implementeren, waardoor ze de hostomgevingen definiëren.

> [!IMPORTANT]
> Ga voor meer informatie over overeenkomsten op serviceniveau van Cognitive Services [naar onze SLA-pagina.](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)

**V: Zijn deze containers beschikbaar in soevereine wolken?**

**A:** Niet iedereen is bekend met de term "soevereine wolk", dus laten we beginnen met de definitie:

> De "soevereine cloud" bestaat uit de [Azure Government](../../azure-government/documentation-government-welcome.md), [Azure Germany](../../germany/germany-welcome.md)en Azure [China 21Vianet](https://docs.microsoft.com/azure/china/overview-operations) clouds.

Helaas worden de Cognitive Services containers *niet* native ondersteund in de soevereine wolken. De containers kunnen in deze clouds worden uitgevoerd, maar ze worden uit de openbare cloud gehaald en moeten gebruiksgegevens naar het openbare eindpunt verzenden.

### <a name="versioning"></a>Versiebeheer

**V: Hoe worden containers bijgewerkt naar de nieuwste versie?**

**A:** Klanten kunnen kiezen wanneer ze de containers die ze hebben geïmplementeerd, willen bijwerken. Containers worden gemarkeerd met standaard [Docker-tags](https://docs.docker.com/engine/reference/commandline/tag/) `latest` die de meest recente versie kunnen aangeven. We raden klanten aan om de nieuwste versie van containers te trekken zodra ze worden uitgebracht, met het afrekenen van [Azure Container Registry-webhooks](../../container-registry/container-registry-webhook.md) voor meer informatie over hoe ze een melding kunnen ontvangen wanneer een afbeelding wordt bijgewerkt.
 
**V: Welke versies worden ondersteund?**

**A:** De huidige en laatste grote versie van de container wordt ondersteund. We moedigen klanten echter aan om op de hoogte te blijven om de nieuwste technologie te krijgen.
 
**V: Hoe worden updates versies?**

**A:** Belangrijke versiewijzigingen geven aan dat er een wijziging is in de API-handtekening. We verwachten dat dit over het algemeen zal samenvallen met belangrijke versiewijzigingen in het bijbehorende Cognitive Service-cloudaanbod. Kleine versiewijzigingen geven bugfixes, modelupdates of nieuwe functies aan die geen baanbrekende wijziging aanbrengen in de API-handtekening.

## <a name="technical-questions"></a>Technische vragen

**V: Hoe moet ik de cognitive services-containers uitvoeren op IoT-apparaten?**

Of u nu geen betrouwbare internetverbinding hebt of wilt besparen op bandbreedtekosten. Of als u vereisten met een lage latentie hebt of te maken heeft met gevoelige gegevens die ter plaatse moeten worden geanalyseerd, biedt [Azure IoT Edge met de containers Cognitive Services](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) u consistentie met de cloud.

**V: Hoe geef ik productfeedback en aanbevelingen voor functies?**

**A:** Klanten worden aangemoedigd om hun zorgen in het openbaar te [uiten](https://cognitive.uservoice.com/) en anderen te verbeteren die hetzelfde hebben gedaan wanneer potentiële problemen elkaar overlappen. De user voice tool kan worden gebruikt voor zowel productfeedback als functieaanbevelingen.

**V: Met wie kan ik contact opnemen voor ondersteuning?**

**A:** Klantenondersteuningskanalen zijn hetzelfde als het cloudaanbod van Cognitive Services. Alle Cognitive Services-containers bevatten logging-functies die ons en de community zullen helpen klanten te ondersteunen. Zie de volgende opties voor extra ondersteuning.

### <a name="customer-support-plan"></a>Customer support plan

Klanten moeten verwijzen naar hun [Azure-ondersteuningsplan](https://azure.microsoft.com/support/plans/) om te zien met wie ze contact kunnen opnemen voor ondersteuning.

### <a name="azure-knowledge-center"></a>Azure-kenniscentrum

Klanten zijn vrij om het [Azure-kenniscentrum](https://azure.microsoft.com/resources/knowledge-center/) te verkennen om vragen en ondersteuningsproblemen te beantwoorden.

### <a name="stack-overflow"></a>Stack Overflow

> [Stack Overflow](https://en.wikipedia.org/wiki/Stack_Overflow) is een vraag en antwoord site voor professionele en enthousiaste programmeurs.

Bekijk de volgende tags voor mogelijke vragen en antwoorden die aansluiten bij uw behoeften.

* [Azure Cognitive Services](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft Cognitief](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**V: Hoe werkt facturering?**

**A:** Klanten worden in rekening gebracht op basis van het verbruik, vergelijkbaar met de Cognitive Services-cloud. De containers moeten worden geconfigureerd om meetgegevens naar Azure te verzenden en transacties worden dienovereenkomstig gefactureerd. Resources die worden gebruikt voor de gehoste en on-premises services, worden toegevoegd aan één quotum met gedifferentieerde prijzen, waarbij rekening wordt gehouden met beide toepassingen. Voor meer informatie verwijzen wij u naar de prijspagina van het bijbehorende aanbod.

* [Anomaly Detector][ad-containers-billing]
* [Computer Visie][cv-containers-billing]
* [Face][fa-containers-billing]
* [Form Recognizer][fr-containers-billing]
* [Language Understanding (LUIS)][lu-containers-billing]
* [Speech Service-API][sp-containers-billing]
* [Tekstanalyse][ta-containers-billing]

> [!IMPORTANT]
> Cognitive Services-containers hebben geen licentie om uit te voeren zonder dat deze is verbonden met Azure voor meting. Klanten moeten de containers te allen tijde in staat stellen factureringsgegevens met de meetservice te communiceren. Cognitive Services-containers verzenden geen klantgegevens naar Microsoft.
 
**V: Wat is de huidige ondersteuningsgarantie voor containers?**

**A:** Er is geen garantie voor previews. De standaardgarantie van Microsoft voor bedrijfssoftware is van toepassing wanneer containers formeel worden aangekondigd als algemene beschikbaarheid (GA).
 
**V: Wat gebeurt er met Cognitive Services-containers wanneer de internetverbinding verloren gaat?**

**A:** Cognitive Services-containers hebben *geen licentie* om uit te voeren zonder dat deze is verbonden met Azure voor meting. Klanten moeten de containers te allen tijde laten communiceren met de meetservice.

**V: Hoe lang kan de container werken zonder verbonden te zijn met Azure?**

**A:** Cognitive Services-containers hebben *geen licentie* om uit te voeren zonder dat deze is verbonden met Azure voor meting. Klanten moeten de containers te allen tijde laten communiceren met de meetservice.
 
**V: Wat is de huidige hardware die nodig is om deze containers uit te voeren?**

**A:** Cognitive Services-containers zijn op x64 gebaseerde containers waarmee elk compatibel Linux-knooppunt, VM en edge-apparaat dat x64 Linux Docker-containers ondersteunt, kan worden uitgevoerd. Ze vereisen allemaal CPU-processors. De minimale en aanbevolen configuraties voor elk containeraanbod zijn hieronder beschikbaar:

* [Anomaly Detector][ad-containers-recommendations]
* [Computer Visie][cv-containers-recommendations]
* [Face][fa-containers-recommendations]
* [Form Recognizer][fr-containers-recommendations]
* [Language Understanding (LUIS)][lu-containers-recommendations]
* [Speech Service-API][sp-containers-recommendations]
* [Tekstanalyse][ta-containers-recommendations]
 
**V: Worden deze containers momenteel ondersteund op Windows?**

**A:** De Cognitive Services containers zijn Linux containers, maar er is enige ondersteuning voor Linux containers op Windows. Zie [Docker-documentatie](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/)voor meer informatie over Linux-containers op Windows.
 
**V: Hoe ontdek ik de containers?**

**A:** Cognitive Services-containers zijn beschikbaar op verschillende locaties, zoals de Azure-portal, dockerhub en Azure-containerregisters. Raadpleeg voor de meest recente containerlocaties [containeropslagplaatsen en -afbeeldingen](../cognitive-services-container-support.md#container-repositories-and-images).

**V: Hoe verhoudt Cognitive Services containers zich tot AWS en Google-aanbiedingen?**

**A:** Microsoft is de eerste cloudprovider die zijn vooraf getrainde AI-modellen verplaatst in containers met eenvoudige facturering per transactie alsof klanten een cloudservice gebruiken. Microsoft gelooft dat een hybride cloud klanten meer keuze geeft.

**V: Welke nalevingscertificeringen hebben containers?**

**A:** Cognitive services containers hebben geen compliance certificeringen

**V: In welke regio's zijn cognitive services-containers beschikbaar?**

**A:** Containers kunnen overal in elke regio worden uitgevoerd, maar ze hebben een sleutel nodig en om terug te bellen naar Azure voor meting. Alle ondersteunde regio's voor de Cloud Service worden ondersteund voor de metingvan containers.

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
