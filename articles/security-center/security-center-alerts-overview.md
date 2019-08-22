---
title: Beveiligings waarschuwingen in Azure Security Center | Microsoft Docs
description: In dit onderwerp wordt uitgelegd wat beveiligings waarschuwingen zijn en welke verschillende typen er beschikbaar zijn in Azure Security Center.
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
ms.openlocfilehash: 25fb94afdfe68510c66cff130a3abc8b74d7311d
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647311"
---
# <a name="security-alerts-in-azure-security-center"></a>Beveiligings waarschuwingen in Azure Security Center

In dit artikel worden de verschillende soorten beveiligings waarschuwingen weer gegeven die beschikbaar zijn in Azure Security Center (ASC). Er zijn diverse waarschuwingen voor de vele verschillende resource typen. Bij ASC worden er waarschuwingen gegenereerd voor resources die zijn geïmplementeerd op Azure en over resources die zijn geïmplementeerd op on-premises en hybride Cloud omgevingen. 

## <a name="what-are-security-alerts"></a>Wat zijn beveiligingswaarschuwingen?

Waarschuwingen zijn de meldingen die Security Center gegenereerd wanneer er bedreigingen voor uw resources worden gedetecteerd. Er worden prioriteiten gegeven en de waarschuwingen worden vermeld samen met de informatie die u nodig hebt om snel het probleem te onderzoeken. Security Center biedt ook aanbevelingen voor het oplossen van een aanval.

## <a name="how-does-security-center-detect-threats"></a>Hoe detecteert Security Center bedreigingen?

Security Center verzamelt, analyseert en integreert de logboek gegevens van uw Azure-resources, het netwerk en verbonden partner oplossingen, zoals firewall-en eindpunt beveiligings oplossingen, om echte bedreigingen te detecteren en fout-positieven te verminderen. Security Center analyseert deze gegevens, vaak het correleren van informatie uit meerdere bronnen, om bedreigingen te identificeren.

ASC bewaakt de resources die zijn geïmplementeerd in azure of die zijn geïmplementeerd op andere on-premises en hybride Cloud omgevingen. Voor meer informatie over het detecteren en reageren op bedreigingen raadpleegt u [hoe Security Center bedreigingen detecteert en hierop reageert](security-center-detection-capabilities.md#asc-detects).

## <a name="security-alert-types"></a>Typen beveiligings waarschuwingen

In de volgende onderwerpen vindt u een overzicht van de verschillende ASC-waarschuwingen op basis van resource typen:

* [Waarschuwingen voor IaaS-Vm's &-servers](security-center-alerts-iaas.md)
* [Systeem eigen Compute-waarschuwingen](security-center-alerts-compute.md)
* [Data Services-waarschuwingen](security-center-alerts-data-services.md)

In de volgende onderwerpen wordt uitgelegd hoe Security Center de verschillende telemetrie gebruikt die het verzamelt van de integratie met de Azure-infra structuur om extra beveiligings lagen toe te passen voor resources die zijn geïmplementeerd op Azure:

* [Waarschuwingen voor service lagen](security-center-alerts-service-layer.md)
* [Integratie met Azure-beveiligings producten](security-center-alerts-integration.md)

## <a name="what-are-alert-incidents"></a>Wat zijn waarschuwings incidenten?

Een beveiligings incident is een verzameling van gerelateerde waarschuwingen, in plaats van elke waarschuwing afzonderlijk weer te geven. Security Center maakt gebruik van de correlatie van een [Smart-waarschuwing](security-center-alerts-cloud-smart.md) in de cloud om verschillende waarschuwingen en signalen met lage betrouw baarheid te correleren in beveiligings incidenten

Met behulp van incidenten Security Center biedt u één weer gave van een aanvals campagne en alle gerelateerde waarschuwingen. Met deze weer gave kunt u snel inzicht krijgen in welke acties de aanvaller heeft geduurd en welke resources worden beïnvloed. Zie [Cloud Smart alert correlatie](security-center-alerts-cloud-smart.md)(Engelstalig) voor meer informatie.

## <a name="get-started-with-alerts"></a>Aan de slag met waarschuwingen

Zie de volgende onderwerpen voor meer informatie over de resources die worden bewaakt door ASC en voor richt lijnen voor het beantwoorden van de waarschuwingen die door ASC worden gepresenteerd.

* Als u wilt zien welke platformen en functies worden beveiligd door ASC, raadpleegt u [platforms en functies die door Azure Security Center worden ondersteund](security-center-os-coverage.md).  
* Zie [beveiligings incidenten afhandelen in azure Security Center](security-center-incident.md)als u wilt weten wat beveiligings incidenten zijn en hoe ASC hierop reageert. 
* Zie [beveiligings waarschuwingen beheren en erop reageren in azure Security Center](security-center-managing-and-responding-alerts.md)voor meer informatie over het beheren van de waarschuwingen die u ontvangt.
* Zie [validatie van waarschuwingen in azure Security Center](security-center-alert-validation.md)voor informatie over het valideren van Security Center op de juiste wijze is geconfigureerd en om een test waarschuwing te stimuleren.  


## <a name="upgrade-to-standard-for-advanced-detections"></a>Upgrade naar Standard voor geavanceerde detecties

Als u geavanceerde detectie wilt instellen, voert u een upgrade uit naar Azure Security Center Standard. 

1. Selecteer in het menu Security Center de optie **beveiligings beleid**.
2. Voor de abonnementen die u wilt verplaatsen naar de laag standaard, klikt u op **Instellingen bewerken**. 
3. Selecteer op de pagina instellingen de optie **prijs categorie**. 
   Een gratis proef versie is per maand beschikbaar. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/security-center/)voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd wat beveiligings waarschuwingen zijn en welke verschillende typen waarschuwingen er beschikbaar zijn in Security Center. Zie de volgende onderwerpen voor meer informatie:

* [Plannings- en bedieningsgids voor Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure Security Center Veelgestelde vragen](https://docs.microsoft.com/azure/security-center/security-center-faq): Raadpleeg de veelgestelde vragen over het gebruik van de service.

