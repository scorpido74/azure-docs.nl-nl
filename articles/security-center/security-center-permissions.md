---
title: Machtigingen in Azure Security Center | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe Azure Security Center op rollen gebaseerd toegangsbeheer gebruikt om machtigingen toe te wijzen aan gebruikers en de toegestane acties voor elke rol te identificeren.
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
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 0e60e782fa65cd5868bebe081673f9a158e07799
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921314"
---
# <a name="permissions-in-azure-security-center"></a>Machtigingen in Azure Security Center

Het Azure Beveiligingscentrum gebruikt [op rollen gebaseerd toegangsbeheer (RBAC)](../role-based-access-control/role-assignments-portal.md), dat [ingebouwde rollen](../role-based-access-control/built-in-roles.md) biedt die kunnen worden toegewezen aan gebruikers, groepen en services in Azure.

Security Center beoordeelt de configuratie van uw resources om beveiligingsproblemen en kwetsbaarheden te identificeren. In Security Center ziet u alleen informatie met betrekking tot een resource wanneer u de rol van eigenaar, bijdrager of lezer krijgt toegewezen voor het abonnement of de resourcegroep waartoe een resource behoort.

Naast deze rollen zijn er twee specifieke Security Center-rollen:

* **Beveiligingslezer:** een gebruiker die tot deze rol behoort, heeft weergaverechten op Security Center. De gebruiker kan aanbevelingen, waarschuwingen, een beveiligingsbeleid en beveiligingsstatussen bekijken, maar kan geen wijzigingen aanbrengen.
* **Beveiligingsbeheerder**: een gebruiker die tot deze rol behoort, heeft dezelfde rechten als de beveiligingslezer en kan ook het beveiligingsbeleid bijwerken en waarschuwingen en aanbevelingen verwijderen.

> [!NOTE]
> De beveiligingsrollen, Security Reader en Security Administrator, hebben alleen toegang in Security Center. De beveiligingsrollen hebben geen toegang tot andere servicegebieden van Azure, zoals Opslag, Web & Mobile of Internet of Things.
>
>

## <a name="roles-and-allowed-actions"></a>Rollen en toegestane acties

In de volgende tabel worden rollen en toegestane acties weergegeven in Security Center.

| Rol | Beveiligingsbeleid bewerken | Beveiligingsaanbevelingen toepassen voor een resource</br> (ook met 'Quick Fix!') | Waarschuwingen en aanbevelingen afwijzen | Waarschuwingen en aanbevelingen weergeven |
|:--- |:---:|:---:|:---:|:---:|
| Eigenaar van een abonnement | ✔ | ✔ | ✔ | ✔ |
| Inzender van een abonnement | -- | ✔ | ✔ | ✔ |
| Eigenaar resourcegroep | -- | ✔ | -- | ✔ |
| Bijdrage van resourcegroep | -- | ✔ | -- | ✔ |
| Lezer | -- | -- | -- | ✔ |
| Beveiligingsbeheer | ✔ | -- | ✔ | ✔ |
| Beveiligingslezer | -- | -- | -- | ✔ |

> [!NOTE]
> We raden u aan de rol toe te wijzen die gebruikers minimaal nodig hebben om hun taken uit te voeren. Wijs de leesrol bijvoorbeeld toe aan gebruikers die alleen informatie over de beveiligingsstatus van een resource hoeven te bekijken, maar geen actie hoeven te ondernemen, zoals het toepassen van aanbevelingen of het bewerken van beleid.
>
>

## <a name="next-steps"></a>Volgende stappen
In dit artikel wordt uitgelegd hoe Het Beveiligingscentrum RBAC gebruikt om machtigingen toe te wijzen aan gebruikers en de toegestane acties voor elke rol te identificeren. Nu u bekend bent met de roltoewijzingen die nodig zijn om de beveiligingsstatus van uw abonnement te controleren, beveiligingsbeleid te bewerken en aanbevelingen toe te passen, leest u hoe u:

- [Beveiligingsbeleid instellen in Security Center](tutorial-security-policy.md)
- [Beveiligingsaanbevelingen beheren in Security Center](security-center-recommendations.md)
- [De beveiligingsstatus van uw Azure-bronnen bewaken](security-center-monitoring.md)
- [Beveiligingswaarschuwingen beheren en erop reageren in beveiligingscentrum](security-center-managing-and-responding-alerts.md)
- [Beveiligingsoplossingen voor partners bewaken](security-center-partner-solutions.md)
