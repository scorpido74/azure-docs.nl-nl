---
title: Beheer van meerdere tenant's in Azure Security Center | Microsoft Documenten
description: Meer informatie over het instellen van cross-tenantbeheer om de beveiligingshouding van meerdere tenants in Security Center te beheren met azure gedelegeerd bronbeheer.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: 2aeb2ab4cfb4ed5e8652638aaced320cc7119d3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919478"
---
# <a name="cross-tenant-management-in-security-center"></a>Beheer van meerdere huurders in Security Center

Met beheer van meerdere tenants u de beveiligingshouding van meerdere tenants in security center bekijken en beheren door gebruik te maken van [Azure gedelegeerd resourcebeheer.](../lighthouse/concepts/azure-delegated-resource-management.md) Beheer meerdere tenants efficiënt, vanuit één weergave, zonder dat u zich hoeft aan te melden bij de directory van elke tenant.

- Serviceproviders kunnen de beveiligingshouding van resources beheren, voor meerdere klanten, vanuit hun eigen tenant.

- Beveiligingsteams van organisaties met meerdere tenants kunnen hun beveiligingshouding vanaf één locatie bekijken en beheren.

## <a name="set-up-cross-tenant-management"></a>Beheer van meerdere huurders instellen

Stel beheer van meerdere tenants in door de toegang tot resources van beheerde tenants te delegeren aan uw eigen tenant met [Azure delegated resource management.](../lighthouse/concepts/azure-delegated-resource-management.md)

> [!NOTE]
> Azure delegated resource management is een van de belangrijkste onderdelen van Azure Lighthouse.

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Hoe werkt cross-tenant management in Security Center

U abonnementen voor meerdere tenants controleren en beheren op dezelfde manier als u meerdere abonnementen in één tenant beheert.

Klik op de bovenste menubalk op het filterpictogram en selecteer de abonnementen die u in de map van elke tenant wilt weergeven.

  ![Tenants filteren](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

De standpunten en acties zijn in principe hetzelfde. Hier volgen enkele voorbeelden:

- **Beveiligingsbeleid beheren:** beheer vanuit één weergave de beveiligingshouding van veel resources met [beleid,](tutorial-security-policy.md)onderneem acties met beveiligingsaanbevelingen en verzamel en beheer beveiligingsgerelateerde gegevens.
- **Verbeter de veilige score en nalevingshouding:** Dankzij de zichtbaarheid van meerdere tenants u de algehele beveiligingshouding van al uw huurders bekijken en waar en hoe u de [veilige score](security-center-secure-score.md) en [nalevingshouding](security-center-compliance-dashboard.md) voor elk van hen het beste verbeteren.
- **Aanbevelingen herstellen**: Monitor en herstel een [aanbeveling](security-center-recommendations.md) voor veel middelen van verschillende huurders tegelijk. U dan onmiddellijk de kwetsbaarheden aanpakken die het hoogste risico vormen voor alle tenants.
- **Waarschuwingen beheren:** [detecteer waarschuwingen](security-center-alerts-overview.md) in de verschillende tenants. Onderneem actie op resources die niet voldoen aan actiebeperkende [herstelstappen](security-center-managing-and-responding-alerts.md).

- **Geavanceerde clouddefensefuncties beheren en meer:** Beheer de verschillende services voor bedreigingsbeveiliging, zoals [just-in-time (JIT) VM-toegang,](security-center-just-in-time.md) [Adaptive Network Hardening,](security-center-adaptive-network-hardening.md) [adaptieve toepassingsbesturingselementen](security-center-adaptive-application.md)en meer.
 
## <a name="next-steps"></a>Volgende stappen
In dit artikel wordt uitgelegd hoe cross-tenant beheer werkt in Security Center. Zie de volgende onderwerpen voor meer informatie over Security Center:

* [Versterk uw beveiligingshouding met Azure Security Center](security-center-monitoring.md) - Lees hoe u de status van uw Azure-bronnen controleren.
* [Veelgestelde vragen over Azure Security Center](faq-general.md) - Vind veelgestelde vragen over het gebruik van de service.
* [Meer informatie over Azure Lighthouse in bedrijfsscenario's](https://docs.microsoft.com/azure/lighthouse/concepts/enterprise) : ontdek hoe Azure Lighthouse het beheer van meerdere tenants binnen een onderneming die meerdere Azure AD-tenants gebruikt, kan vereenvoudigen.