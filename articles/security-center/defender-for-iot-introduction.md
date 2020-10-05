---
title: Azure Defender voor IoT
description: Informatie over Azure Defender for IoT
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 2f41aac085fef62e0356a93a07823f21d7ba3667
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448346"
---
# <a name="introduction-to-azure-defender-for-iot"></a>Inleiding tot Azure Defender for IoT

U kunt het beveiligingsbeheer combineren en end-to-end detectie van bedreigingen en analyses inschakelen voor alle hybride cloudworkloads en uw Azure IoT-oplossing.

Azure Defender for IoT heeft het volgende te bieden:

- **Assetdetectie en netwerktoewijzing**, met inbegrip van gegevens zoals fabrikant van het apparaat, apparaattype en hoe apparaten communiceren in het netwerk
- **Beveiligingsbeheer**, met inbegrip van informatie over CVE's, open poorten en onbevoegde internetverbindingen
- **Doorlopende bewaking van bedreigingen**, met realtime waarschuwingen die afwijkende of niet-geautoriseerde activiteiten, zoals gerichte aanvallen of schadelijke software, aangeven

Volledige informatie is beschikbaar in [de specifieke documentatie](https://docs.microsoft.com/azure/asc-for-iot/overview).

## <a name="availability"></a>Beschikbaarheid
|Aspect|Details|
|----|:----|
|Releasestatus:|Algemeen verkrijgbaar (GA)|
|Prijzen:|[Azure Defender](security-center-pricing.md) is vereist|
|Vereiste rollen en machtigingen:|Schrijfmachtigingen op de NSG's van de computer|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) Nationaal/onafhankelijk (Overheid van de VS, China, andere overheden)|
|||

## <a name="what-devices-can-azure-defender-for-iot-secure"></a>Welke apparaten kan Azure Defender for IoT beveiligen?
Door de mogelijkheden van Security Center, Azure Defender en Cyberx Agentless-technologie te combineren, kunt u het volgende beveiligen:

- **Nieuw ontwikkelde apparaten** verbonden via IoT Hub. Hierdoor kunnen organisaties hun IoT-initiatieven versnellen door zowel moderne, interneteigen apparaten als traditionele ICS/SCADA-apparaten te beveiligen met één geïntegreerde oplossing.
    - Onboard nieuwe apparaten en pas beveiligingsbeleid toe voor uw workloads (Leaf-apparaten, Microsoft Edge-apparaten, IoT Hub) om te zorgen voor naleving van beveiligingsnormen en een betere beveiligingspostuur.

- **Niet-beheerde verouderde apparaten** gebruikt in omgevingen met operationele technologie (OT) zoals productie, BMS (Building Management Systems), life sciences, nutsbedrijven, olie- en gaswinning en logistiek. 
    - Voor het beveiligen van onbeheerde apparaten gebruikt Azure Defender for IoT een on-premises sensor voor passieve, niet-invasieve analyses van netwerkverkeer die geen enkele invloed heeft op de prestaties van OT-omgevingen. 
    - Azure Defender for IoT is bovendien voorzien van uitvoerige informatie over gespecialiseerde IoT/OT-protocollen, gecombineerd met gepatenteerde, IoT/OT-bewuste gedragsanalyses en machine learning. Daardoor hoeven er geen regels of handtekeningen te worden geconfigureerd, wat veelal resulteert in implementaties in enkele minuten of uren tijd in plaats van dagen of weken. 


## <a name="azure-defender-for-iot-integration-with-azure-sentinel"></a>Integratie van Azure Defender for IoT met Azure Sentinel
Azure Defender for IoT is systeemeigen geïntegreerd met [Azure Sentinel](../sentinel/overview.md) om geïntegreerde IT/OT-beveiligingsbewaking en -beheer mogelijk te maken.

SecOps-teams kunnen:

- Snel IoT/OT-bedreigingen opsporen en erop reageren via OT-specifieke SOAR-playbooks die zijn opgenomen in Sentinel
- Gebruikmaken van doorlopend bijgewerkte IoT/OT-specifieke bedreigingsinformatie die wordt verstrekt door Section 52, het interne IoT/OT-team van Microsoft
- Azure Defender for IoT integreren met bestaande SOC-werkstromen en beveiligingshulpprogramma's van derden, zoals Splunk, IBM QRadar en ServiceNow


## <a name="next-steps"></a>Volgende stappen

In dit artikel bent u meer te weten gekomen over Azure Defender for IoT in Azure Security Center. Zie voor meer informatie:

- [Kennismaking met Azure Security Center for IoT](../asc-for-iot/overview.md)
