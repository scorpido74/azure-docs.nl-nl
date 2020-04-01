---
title: Veelgestelde vragen over Azure Security Center - vragen over bestaande Log Analytics-agents
description: Deze veelgestelde vragen beantwoorden vragen voor klanten die de Log Analytics-agent al gebruiken en overweegt Azure Security Center, een product dat u helpt bedreigingen te voorkomen, detecteren en erop te reageren.
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
ms.openlocfilehash: f6384c1e9e14e38b4c44c5ac79a674839b43b4ca
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436153"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>Veelgestelde vragen voor klanten die azure monitorlogboeken al gebruiken<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Overschrijft Security Center bestaande verbindingen tussen VM's en werkruimten?

Als de agent Log Analytics al is geïnstalleerd als Azure-extensie, overschrijft Beveiligingscentrum de bestaande werkruimteverbinding niet. In plaats daarvan gebruikt Security Center de bestaande werkruimte. De VM wordt beveiligd op voorwaarde dat de "Security" of "SecurityCenterFree" oplossing is geïnstalleerd op de werkruimte waaraan het rapporteert. 

Er is een oplossing voor beveiligingscentrum geïnstalleerd op de werkruimte die is geselecteerd in het scherm Gegevensverzameling als deze nog niet aanwezig is, en de oplossing wordt alleen toegepast op de relevante VM's. Wanneer u een oplossing toevoegt, wordt deze standaard automatisch geïmplementeerd op alle Windows- en Linux-agents die zijn aangesloten op uw Log Analytics-werkruimte. [Met Solution Targeting](../operations-management-suite/operations-management-suite-solution-targeting.md) u een scope toepassen op uw oplossingen.

> [!TIP]
> Als de Log Analytics-agent rechtstreeks op de VM is geïnstalleerd (niet als Azure-extensie), installeert Beveiligingscentrum de loganalyse-agent niet en is de beveiligingscontrole beperkt.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Installeert Security Center oplossingen op mijn bestaande Log Analytics-werkruimten? Wat zijn de implicaties voor facturering?
Wanneer beveiligingscentrum vaststelt dat een vm al is verbonden met een werkruimte die u hebt gemaakt, maakt Security Center oplossingen op deze werkruimte mogelijk op basis van uw prijscategorie. De oplossingen worden alleen toegepast op de relevante Azure VM's, via [oplossingstargeting,](../operations-management-suite/operations-management-suite-solution-targeting.md)zodat de facturering hetzelfde blijft.

- **Gratis laag** – Security Center installeert de 'SecurityCenterFree'-oplossing op de werkruimte. U wordt niet gefactureerd voor de gratis laag.
- **Standaardlaag** – Security Center installeert de 'Security'-oplossing op de werkruimte.

   ![Oplossingen op standaardwerkruimte](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Ik heb al werkruimten in mijn omgeving, kan ik ze gebruiken om beveiligingsgegevens te verzamelen?
Als de agent Log Analytics al is geïnstalleerd als Azure-extensie, gebruikt Beveiligingscentrum de bestaande verbonden werkruimte. Een Security Center-oplossing is geïnstalleerd op de werkruimte als deze nog niet aanwezig is en de oplossing wordt alleen toegepast op de relevante VM's via [oplossingstargeting.](../operations-management-suite/operations-management-suite-solution-targeting.md)

Wanneer Security Center de Log Analytics-agent op VM's installeert, wordt de standaardwerkruimte(s) gebruikt die zijn gemaakt door Security Center.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Ik heb al een beveiligingsoplossing op mijn werkplekken. Wat zijn de implicaties voor facturering?
De oplossing Security & Audit wordt gebruikt om standaardstandaardfuncties voor Azure VM's in te schakelen. Als de oplossing Voor & beveiliging al op een werkruimte is geïnstalleerd, gebruikt Security Center de bestaande oplossing. Er is geen wijziging in de facturering.