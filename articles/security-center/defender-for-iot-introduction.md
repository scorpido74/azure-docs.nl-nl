---
title: Azure Defender voor IoT
description: Meer informatie over Azure Defender voor IoT
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 67bb01092cb6171ae56b8ab8b308e0c2ea3bece0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936413"
---
# <a name="introduction-to-azure-defender-for-iot"></a>Inleiding tot Azure Defender voor IoT

U kunt het beveiligings beheer combi neren en end-to-end detectie van bedreigingen en analyses voor de hybride Cloud werkbelastingen en uw Azure IoT-oplossing inschakelen.

Azure Defender voor IoT biedt het volgende:

- **Asset Discovery en netwerk toewijzing**, inclusief details zoals apparaatfabrikant, apparaattype en hoe apparaten communiceren op het netwerk
- **Beveiligings beheer**, met inbegrip van informatie over CVEs, open poorten en onbevoegde Internet verbindingen
- **Voortdurende bewaking van bedreigingen**, met realtime waarschuwingen die afwijkende of niet-geautoriseerde activiteiten aangeven, zoals gerichte aanvallen of schadelijke software

Volledige informatie is beschikbaar in [de specifieke documentatie](https://docs.microsoft.com/azure/asc-for-iot/overview).

## <a name="availability"></a>Beschikbaarheid
|Aspect|Details|
|----|:----|
|Release status:|Algemeen beschikbaar (GA)|
|Koers|Vereist [Azure Defender](security-center-pricing.md)|
|Vereiste rollen en machtigingen:|Schrijf machtigingen op de Nsg's van de computer|
|Clouds|![Yes](./media/icons/yes-icon.png) Commerciële Clouds<br>![Yes](./media/icons/yes-icon.png) National/soeverein (US Gov, China gov, andere gov)|
|||

## <a name="what-devices-can-azure-defender-for-iot-secure"></a>Welke apparaten kunnen Azure Defender voor IoT veilig zijn?
Door de mogelijkheden van Security Center, Azure Defender en Cyberx Agentloze technologie te combi neren, kunt u het volgende beveiligen:

- **Ontwikkel-apparaten** die zijn verbonden via IOT hub. Hierdoor kunnen organisaties hun IoT-initiatieven versnellen door zowel moderne, Internet-eigen apparaten als traditionele ICS/SCADA-apparaten te beveiligen met één geïntegreerde oplossing.
    - Nieuwe apparaten onboarden en beveiligings beleid Toep assen voor uw werk belastingen (Leaf-apparaten, micro soft edge-apparaten, IoT Hub) om te zorgen voor naleving van beveiligings normen en verbeterde beveiligings postuur.

- Niet- **beheerde Brownfield-apparaten** die worden gebruikt in omgevingen met operationele technologie (OT) zoals productie, bouw beheer systemen (BMS), levens wetenschappen, energie-en water hulpprogramma's, olie & gas en logistiek. 
    - Voor het beveiligen van onbeheerde apparaten gebruikt Azure Defender voor IoT een on-premises sensor voor passieve, niet-invasieve netwerk verkeer analyse (NTA) die geen invloed heeft op de prestaties van de omgeving. 
    - Het bevat ook een diep gaande kennis van gespecialiseerde IoT/OT-protocollen, gecombineerd met gepatenteerde, IoT/niet-bewuste gedrags analyse en machine learning-om te voor komen dat er regels of hand tekeningen worden geconfigureerd, wat resulteert in typische implementaties in minuten of uren in plaats van dagen of weken. 


## <a name="azure-defender-for-iot-integration-with-azure-sentinel"></a>Azure Defender voor IoT-integratie met Azure Sentinel
Azure Defender voor IoT is systeem eigen geïntegreerd met [Azure Sentinel](../sentinel/overview.md)om geïntegreerde IT/niet-beveiligings bewaking en beheer mogelijk te maken.

SecOps teams kunnen:

- Spoor en reageer snel op IoT/OT-bedreigingen via via playbooks die zijn opgenomen in Sentinel
- Profiteer van voortdurend bijgewerkte IoT/OT-specifieke bedreigings informatie die is verstrekt door para graaf 52, het interne IoT/OT-team van micro soft.
- Integreer Azure Defender voor IoT met bestaande SOC-werk stromen en beveiligings hulpprogramma's van derden, zoals Splunk, IBM QRadar en ServiceNow


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over Azure Defender voor IoT in Azure Security Center. Zie voor meer informatie:

- [Kennismaking met Azure Security Center for IoT](../asc-for-iot/overview.md)
