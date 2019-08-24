---
title: Beveiligings waarschuwingen in Azure Security Center | Microsoft Docs
description: In dit onderwerp wordt uitgelegd welke beveiligings waarschuwingen er zijn en welke verschillende typen beschikbaar zijn in Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/20/2019
ms.author: v-mohabe
ms.openlocfilehash: d905096432c6e05e5386631480e5cc45b5c890c5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013297"
---
# <a name="security-alerts-in-azure-security-center"></a>Beveiligingswaarschuwingen in Azure Security Center

In Azure Security Center zijn er diverse waarschuwingen voor de vele verschillende resource typen. Security Center genereert waarschuwingen voor resources die zijn geïmplementeerd op Azure en ook voor resources die zijn geïmplementeerd op on-premises en hybride Cloud omgevingen. 

## <a name="what-are-security-alerts"></a>Wat zijn beveiligingswaarschuwingen?

Waarschuwingen zijn de meldingen die Security Center gegenereerd wanneer er bedreigingen voor uw resources worden gedetecteerd. Er worden prioriteiten gegeven en de waarschuwingen worden vermeld, samen met de informatie die u nodig hebt om snel het probleem te onderzoeken. Security Center biedt ook aanbevelingen voor het oplossen van een aanval.

## <a name="how-does-security-center-detect-threats"></a>Hoe detecteert Security Center bedreigingen?

Security Center verzamelt, analyseert en integreert de logboek gegevens van uw Azure-resources en het netwerk om echte bedreigingen te detecteren en fout-positieven te verminderen. Het werkt ook met verbonden partner oplossingen, zoals firewall-en eindpunt beveiligings oplossingen. Security Center analyseert deze gegevens, vaak het correleren van informatie uit meerdere bronnen, om bedreigingen te identificeren.

Security Center bewaakt de bronnen in welke omgeving ze mogelijk zijn. Voor meer informatie over het detecteren van en reageren op bedreigingen raadpleegt u [hoe Security Center bedreigingen detecteert en hierop reageert](security-center-detection-capabilities.md#asc-detects).

## <a name="security-alert-types"></a>Typen beveiligings waarschuwingen

De volgende onderwerpen helpen u bij het door lopen van de verschillende waarschuwingen, volgens de resource typen:

* [IaaS Vm's en server waarschuwingen](security-center-alerts-iaas.md)
* [Systeem eigen Compute-waarschuwingen](security-center-alerts-compute.md)
* [Data Services-waarschuwingen](security-center-alerts-data-services.md)

In de volgende onderwerpen wordt uitgelegd hoe Security Center de verschillende telemetrie gebruikt die wordt verzameld van integratie met de Azure-infra structuur, om extra beveiligings lagen toe te passen voor resources die in azure zijn geïmplementeerd:

* [Waarschuwingen voor service lagen](security-center-alerts-service-layer.md)
* [Integratie met Azure-beveiligings producten](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>Wat zijn beveiligings incidenten?

Een beveiligings incident is een verzameling van gerelateerde waarschuwingen, in plaats van elke waarschuwing afzonderlijk weer te geven. Security Center maakt gebruik van de correlatie van een [Smart-waarschuwing](security-center-alerts-cloud-smart.md) in de cloud om verschillende waarschuwingen en signalen met lage betrouw baarheid te correleren in beveiligings incidenten

Met behulp van incidenten Security Center biedt u één weer gave van een aanvals campagne en alle gerelateerde waarschuwingen. Met deze weer gave kunt u snel inzicht krijgen in welke acties de aanvaller heeft geduurd en welke resources zijn beïnvloed. Zie [Cloud Smart alert correlatie](security-center-alerts-cloud-smart.md)(Engelstalig) voor meer informatie.

## <a name="get-started-with-alerts"></a>Aan de slag met waarschuwingen

Hier vindt u artikelen die u meer inzicht geven in de bronnen die worden bewaakt door Security Center en richt lijnen bieden om te reageren op de gepresenteerde waarschuwingen.

* [Platforms en functies die worden ondersteund door Azure Security Center](security-center-os-coverage.md)  
* [Beveiligings incidenten afhandelen in Azure Security Center](security-center-incident.md) 
* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Validatie van waarschuwingen in Azure Security Center](security-center-alert-validation.md)  


## <a name="upgrade-to-standard-for-advanced-detections"></a>Upgrade naar Standard voor geavanceerde detecties

Als u geavanceerde detectie wilt instellen, voert u een upgrade uit naar Azure Security Center Standard. 

1. Selecteer in het menu **Security Center** de optie **beveiligings beleid**.
2. Voor de abonnementen die u wilt verplaatsen naar de laag standaard, selecteert u **Instellingen bewerken**. 
3. Selecteer op de pagina instellingen de optie **prijs categorie**. 
   Een gratis proef versie is per maand beschikbaar. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de verschillende soorten waarschuwingen die beschikbaar zijn in Security Center. Zie voor meer informatie:

* [Plannings- en bedieningsgids voor Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Veelgestelde vragen over Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-faq)

