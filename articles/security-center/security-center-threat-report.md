---
title: Bedreigingsinformatierapport Azure Security Center | Microsoft Docs
description: Deze pagina helpt u bij het gebruik van Azure Security Center Threat Intelligence-rapporten tijdens een onderzoek naar meer informatie over beveiligings waarschuwingen
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: a4fdbab4a69fac1376779f37d5fa69fef587bf52
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84888233"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Azure Security Center Threat Intelligence-rapport

Op deze pagina wordt uitgelegd hoe Azure Security Center Threat Intelligence-rapporten u kunnen helpen om meer te weten te komen over een bedreiging waarmee een beveiligings waarschuwing wordt geactiveerd.


## <a name="what-is-a-threat-intelligence-report"></a>Wat is een bedreigingsinformatierapport?

Security Center bedreigings beveiliging werkt door beveiligings gegevens van uw Azure-resources, het netwerk en verbonden partner oplossingen te bewaken. Deze informatie wordt door Security Center geanalyseerd, waarbij vaak informatie uit meerdere bronnen wordt samengebracht om bedreigingen te analyseren. Zie [How Azure Security Center detecteert en reageert op bedreigingen](security-center-alerts-overview.md#detect-threats)voor meer informatie.

Wanneer Security Center een bedreiging identificeert, wordt er een [beveiligings waarschuwing](security-center-managing-and-responding-alerts.md)geactiveerd, die gedetailleerde informatie bevat over de gebeurtenis, inclusief suggesties voor herstel. Security Center biedt bedreigingen Intelligence-rapporten die informatie over gedetecteerde bedreigingen bevatten om te helpen incidenten om te voor komen dat antwoord teams problemen onderzoeken en oplossen. Het rapport bevat informatie zoals:

* De identiteit of associaties van de aanvaller (indien beschikbaar)
* De doelstellingen van de aanvaller
* Huidige en eerdere aanvalscampagnes (indien beschikbaar)
* Tactieken, hulpprogram ma's en procedures van aanvallers
* Gekoppelde indicators of compromise (IoC) zoals URL's en bestands-hashes
* Victimologie, de prevalentie qua branche en geografische locatie, om u te helpen bepalen of uw Azure-resources risico lopen
* Informatie over risicobeperking en herstel

> [!NOTE]
> De hoeveelheid informatie in het rapport kan variëren; de gedetailleerdheid van de informatie is afhankelijk van de activiteit en de gangbaarheid van de malware.

Security Center heeft drie soorten bedreigingsrapporten, afhankelijk van het soort aanval. De beschikbare rapporten zijn:

* **Rapport activiteiten groep**: biedt uitgebreide Dives in aanvallers, hun doel stellingen en tactieken.
* **Campagnerapport**: gericht op details van een specifieke aanvalscampagne.
* **Bedreigingsoverzichtsrapport**: dekt alle onderwerpen in de voorgaande twee rapporten.

Dit type informatie is nuttig tijdens het respons proces van een incident, waarbij een voortdurend onderzoek wordt gedaan om de bron van de aanval, de motivatie van de aanvaller en wat te doen om het probleem in de toekomst te verhelpen.



## <a name="how-to-access-the-threat-intelligence-report"></a>Hoe open ik het bedreigingsinformatierapport?

1. Open de pagina **beveiligings waarschuwingen** vanuit de zijbalk van Security Center.
1. Selecteer een waarschuwing. 
    De pagina waarschuwings Details wordt geopend met meer informatie over de waarschuwing. Hieronder vindt u de informatie pagina met **indica toren** voor waarschuwingen gedetecteerd.

    [![Pagina met gedetecteerde waarschuwings Details van Ransomware-indica toren](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png)](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png#lightbox)

1. Selecteer de koppeling naar het rapport en een PDF-bestand wordt geopend in de standaard browser.

    [![Pagina waarschuwing Details mogelijk onveilige actie](media/security-center-threat-report/threat-intelligence-report.png)](media/security-center-threat-report/threat-intelligence-report.png#lightbox)

    U kunt het PDF-rapport eventueel downloaden. 

    >[!TIP]
    > De hoeveelheid beschikbare informatie voor een beveiligingswaarschuwing is afhankelijk van het type waarschuwing.



## <a name="next-steps"></a>Volgende stappen

Op deze pagina wordt uitgelegd hoe u bedreigings informatie rapporten opent wanneer u beveiligings waarschuwingen onderzoekt. Zie de volgende pagina's voor verwante informatie:

* [Beveiligings waarschuwingen beheren en erop reageren in azure Security Center](security-center-managing-and-responding-alerts.md). Informatie over het beheren van en reageren op beveiligingswaarschuwingen.
* [Beveiligings incidenten afhandelen in Azure Security Center](security-center-incident.md)