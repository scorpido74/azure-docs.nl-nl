---
title: Besturingselementen voor Naleving van Azure Policy-regelgeving voor Azure Cognitive Search
description: Geeft beschikbare besturingselementen voor Naleving van Azure Policy-regelgeving voor Azure Cognitive Search weer. Deze ingebouwde beleidsdefinities bieden algemene benaderingen voor het beheren van de naleving van uw Azure-resources.
ms.date: 07/30/2020
ms.topic: sample
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: b039073810b7b429a560485bcc55df3be569be17
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494873"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-cognitive-search"></a>Besturingselementen voor Naleving van Azure Policy-regelgeving voor Azure Cognitive Search

Als u [Azure Policy](../governance/policy/overview.md) gebruikt om de aanbevelingen in de [Azure Security Benchmark](../security/benchmarks/introduction.md) af te dwingen, weet u waarschijnlijk al dat u beleidsregels kunt opstellen voor het identificeren van niet-compatibele services en het in orde maken van deze services. Deze beleidsregels kunnen aangepast zijn of ze kunnen zijn gebaseerd op ingebouwde definities die voldoen aan de criteria en geschikte oplossingen voor gangbare best practices.

Voor Azure Cognitive Search is er momenteel één ingebouwde definitie, die hieronder wordt vermeld, en die u kunt gebruiken in een beleidstoewijzing. Deze definitie is bedoeld voor logboekregistratie en controle. Door deze ingebouwde definitie te gebruiken in een [beleid dat u maakt](../governance/policy/assign-policy-portal.md), zal het systeem scannen op services waarvoor geen [diagnostische logboekregistratie](search-monitor-logs.md) is ingeschakeld en dit dan alsnog doen.

[Naleving van regelgeving in Azure Policy](../governance/policy/concepts/regulatory-compliance.md) biedt door Microsoft gemaakte en beheerde initiatiefdefinities, bekend als _ingebouwde modules_, voor de **nalevingsdomeinen** en **beveiligingsmaatregelen** met betrekking tot verschillende nalevingsstandaarden. Op deze pagina worden de **nalevingsdomeinen** en **beveiligingsmaatregelen** voor Azure Cognitive Search weergegeven. U kunt de ingebouwde modules voor een **beveiligingsmaatregel** afzonderlijk toewijzen om uw Azure-resources de specifieke standaard te laten naleven.

[!INCLUDE [azure-policy-compliancecontrols-introwarning](../../includes/policy/standards/intro-warning.md)]

[!INCLUDE [azure-policy-compliancecontrols-search](../../includes/policy/standards/byrp/microsoft.search.md)]

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Naleving van Azure Policy-regelgeving](../governance/policy/concepts/regulatory-compliance.md).
- Bekijk de inbouwingen op de [Azure Policy GitHub-opslagplaats](https://github.com/Azure/azure-policy).