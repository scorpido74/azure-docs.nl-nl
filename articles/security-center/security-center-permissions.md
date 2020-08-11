---
title: Machtigingen in Azure Security Center | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe Azure Security Center toegangs beheer op basis van rollen gebruikt om machtigingen aan gebruikers toe te wijzen en de toegestane acties voor elke rol te identificeren.
services: security-center
cloud: na
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: memildin
ms.openlocfilehash: e5728b220f12c3305aa896e64010fc7e5828d6fb
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067219"
---
# <a name="permissions-in-azure-security-center"></a>Machtigingen in Azure Security Center

Azure Security Center maakt gebruik van [Azure RBAC (op rollen gebaseerd toegangs beheer)](../role-based-access-control/role-assignments-portal.md), dat [ingebouwde rollen](../role-based-access-control/built-in-roles.md) biedt die kunnen worden toegewezen aan gebruikers, groepen en services in Azure.

Security Center evalueert de configuratie van uw resources om beveiligings problemen en beveiligings lekken te identificeren. In Security Center ziet u alleen informatie die betrekking heeft op een resource als u de rol van eigenaar, bijdrager of lezer hebt toegewezen aan het abonnement of de resource groep waarvan een resource deel uitmaakt.

Naast deze rollen zijn er twee specifieke Security Center-rollen:

* **Beveiligings lezer**: een gebruiker die tot deze rol behoort, heeft weergave rechten voor Security Center. De gebruiker kan aanbevelingen, waarschuwingen, een beveiligings beleid en beveiligings status weer geven, maar kan geen wijzigingen aanbrengen.
* **Beveiligings beheerder**: een gebruiker die deel uitmaakt van deze rol heeft dezelfde rechten als de beveiligings lezer en kan het beveiligings beleid ook bijwerken en waarschuwingen en aanbevelingen negeren.

> [!NOTE]
> De beveiligings rollen, beveiligings lezer en beveiligings beheerder hebben alleen toegang tot Security Center. De beveiligings rollen hebben geen toegang tot andere service gebieden van Azure, zoals Storage, Web & Mobile of Internet of Things.
>

## <a name="roles-and-allowed-actions"></a>Rollen en toegestane acties

De volgende tabel bevat rollen en toegestane acties in Security Center.

|Bewerking|Beveiligings lezer/ <br> Lezer |Beveiligingsbeheerder  |Inzender voor resource groepen/ <br> Eigenaar van resource groep  |Mede werker van abonnement  |Abonnements eigenaar  |
|:--- |:---:|:---:|:---:|:---:|:---:|
|Beveiligings beleid bewerken|-|✔|-|-|✔|
|Initiatieven (waaronder) reglementaire nalevings standaarden toevoegen/toewijzen|-|-|-|-|✔|
|Prijs categorie voor abonnement wijzigen|-|✔|-|-|✔|
|Automatische inrichting in-of uitschakelen|-|✔|-|✔|✔|
|Aanbevelingen voor beveiliging Toep assen op een resource</br> (en gebruik een [snelle oplossing!](security-center-remediate-recommendations.md#quick-fix-remediation))|-|-|✔|✔|✔|
|Waarschuwingen negeren|-|✔|-|✔|✔|
|Waarschuwingen en aanbevelingen weer geven|✔|✔|✔|✔|✔|

> [!NOTE]
> We raden u aan de rol toe te wijzen die gebruikers minimaal nodig hebben om hun taken uit te voeren. Wijs bijvoorbeeld de rol Lezer toe aan gebruikers die alleen informatie over de beveiligings status van een resource hoeven te bekijken, maar geen actie ondernemen, zoals het Toep assen van aanbevelingen of het bewerken van beleid.
>
>

## <a name="next-steps"></a>Volgende stappen
In dit artikel wordt uitgelegd hoe Security Center RBAC gebruikt om machtigingen toe te wijzen aan gebruikers en de toegestane acties voor elke rol te identificeren. Nu u bekend bent met de roltoewijzingen die nodig zijn voor het bewaken van de beveiligings status van uw abonnement, het bewerken van beveiligings beleid en het Toep assen van aanbevelingen, leert u het volgende:

- [Beveiligingsbeleid instellen in Security Center](tutorial-security-policy.md)
- [Aanbevelingen voor beveiliging in Security Center beheren](security-center-recommendations.md)
- [De beveiligings status van uw Azure-resources controleren](security-center-monitoring.md)
- [Beveiligings waarschuwingen beheren en erop reageren in Security Center](security-center-managing-and-responding-alerts.md)
- [Beveiligings oplossingen van partners bewaken](security-center-partner-solutions.md)
