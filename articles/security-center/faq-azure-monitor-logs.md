---
title: 'Azure Security Center Veelgestelde vragen: vragen over bestaande MMAs'
description: Deze veelgestelde vragen beantwoordt vragen aan klanten die al gebruikmaken van de micro soft Monitoring Agent en overwegen Azure Security Center, een product dat u helpt bij het voor komen, detecteren en reageren op bedreigingen.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 528ff47be2b18cb7d9b938e988383a9e81be67fb
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599507"
---
# Veelgestelde vragen voor klanten die Azure Monitor-logboeken al gebruiken<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Security Center heeft voorrang op bestaande verbindingen tussen virtuele machines en werkruimten?

Als een virtuele machine al de Microsoft Monitoring Agent is geïnstalleerd als een uitbreiding van Azure is, wordt de bestaande verbinding in de werkruimte niet overschreven door Security Center. Security Center gebruikt in plaats daarvan de bestaande werkruimte. De virtuele machine wordt beveiligd, op voor waarde dat de oplossing ' Security ' of ' SecurityCenterFree ' is geïnstalleerd op de werk ruimte waarmee deze wordt gerapporteerd. 

Er wordt een Security Center oplossing geïnstalleerd op de werk ruimte die is geselecteerd in het scherm voor het verzamelen van gegevens, als deze nog niet aanwezig is, en de oplossing wordt alleen toegepast op de relevante Vm's. Wanneer u een oplossing toevoegt, wordt deze automatisch geïmplementeerd standaard voor alle Windows en Linux-agents die zijn verbonden met uw Log Analytics-werkruimte. Met [doel items van oplossingen](../operations-management-suite/operations-management-suite-solution-targeting.md) kunt u een bereik Toep assen op uw oplossingen.

Als de Microsoft Monitoring Agent rechtstreeks op de virtuele machine (en niet als een Azure-extensie) is geïnstalleerd, de Microsoft Monitoring Agent wordt niet geïnstalleerd door Security Center en beveiligingsbewaking is beperkt.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Security Center oplossingen installeren op mijn bestaande Log Analytics-werkruimten Wat zijn de gevolgen voor de facturering?
Als Security Center identificeert dat een virtuele machine al is verbonden met een werkruimte die u hebt gemaakt, kunt u Security Center-oplossingen voor deze werkruimte op basis van uw prijscategorie. De oplossingen worden alleen toegepast op de relevante Azure-Vm's, via [oplossings doelen](../operations-management-suite/operations-management-suite-solution-targeting.md), zodat de facturering op dezelfde manier blijft.

- **Gratis laag** – Security Center installeert de oplossing ' SecurityCenterFree ' in de werk ruimte. Er worden geen kosten in rekening gebracht voor de gratis laag.
- **Standard-laag** – Security Center installeert de oplossing beveiliging in de werk ruimte.

   ![Oplossingen op standaardwerkruimte][1]

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Ik heb al werkruimten in Mijn omgeving, kan ik deze niet gebruiken voor het verzamelen van beveiligingsgegevens?
Als een virtuele machine al de Microsoft Monitoring Agent is geïnstalleerd als een uitbreiding van Azure is, wordt in Security Center maakt gebruik van de bestaande gekoppelde werkruimte. Er wordt een Security Center oplossing geïnstalleerd op de werk ruimte als deze nog niet aanwezig is, en de oplossing wordt alleen toegepast op de relevante Vm's via de [doel stellingen](../operations-management-suite/operations-management-suite-solution-targeting.md)van de oplossing.

Wanneer Security Center de Microsoft Monitoring Agent is geïnstalleerd op virtuele machines, gebruikt de standaard werkruimten die zijn gemaakt door Security Center.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Ik heb al beveiligingsoplossing op mijn werkruimten. Wat zijn de gevolgen voor de facturering?
De oplossing beveiliging en Audit wordt Security Center Standard-laag-functies inschakelen voor virtuele Azure-machines gebruikt. Als de oplossing beveiliging en Audit is al geïnstalleerd op een werkruimte, wordt in Security Center maakt gebruik van de bestaande oplossing. Er is geen wijziging in facturering.


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/solutions.png