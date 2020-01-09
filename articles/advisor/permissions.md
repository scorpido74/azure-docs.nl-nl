---
title: Machtigingen in Azure Advisor
description: Advisor-machtigingen en hoe ze de mogelijkheid kunnen blok keren om abonnementen te configureren of aanbevelingen uit te stellen of te negeren.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: c850d757044066d5c4a793e076436906d715833c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422323"
---
# <a name="permissions-in-azure-advisor"></a>Machtigingen in Azure Advisor

Azure Advisor biedt aanbevelingen op basis van het gebruik en de configuratie van uw Azure-resources en-abonnementen. Advisor maakt gebruik van de [ingebouwde rollen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) van op [rollen gebaseerde Access Control](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) om uw toegang tot aanbevelingen en Advisor-functies te beheren. 

## <a name="roles-and-their-access"></a>Rollen en hun toegang

De volgende tabel definieert de rollen en de toegang die ze hebben in Advisor:

| **Rol** | **Aanbevelingen weer geven** | **Regels bewerken** | **Abonnements configuratie bewerken** | **Configuratie van de resource groep bewerken**| **Aanbevelingen negeren en uitstellen**|
|---|:---:|:---:|:---:|:---:|:---:|
|Abonnements eigenaar|**X**|**X**|**X**|**X**|**X**|
|Mede werker van abonnement|**X**|**X**|**X**|**X**|**X**|
|Abonnements lezer|**X**|--|--|--|--|
|Eigenaar van resource groep|**X**|--|--|**X**|**X**|
|Inzender voor resource groep|**X**|--|--|**X**|**X**|
|Lezer van resource groep|**X**|--|--|--|--|
|Resource-eigenaar|**X**|--|--|--|**X**|
|Resource bijdrager|**X**|--|--|--|**X**|
|Resource Reader|**X**|--|--|--|--|

> [!NOTE]
> Toegang tot de weer gave van aanbevelingen is afhankelijk van uw toegang tot de betrokken resource van de aanbeveling.

## <a name="permissions-and-unavailable-actions"></a>Machtigingen en niet-beschik bare acties

Als u niet over de juiste machtigingen beschikt, kan de mogelijkheid voor het uitvoeren van acties in Advisor worden geblokkeerd. Hieronder volgen enkele veelvoorkomende problemen.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Kan geen abonnementen of resource groepen configureren

Wanneer u probeert abonnementen of resource groepen in Advisor te configureren, is het mogelijk dat de optie om op te nemen of uit te sluiten is uitgeschakeld. Deze status geeft aan dat u niet over voldoende machtigingen beschikt voor die resource groep of dit abonnement. U kunt dit probleem oplossen door te leren hoe u [een gebruiker toegang verleent](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Kan een aanbeveling niet uitstellen of negeren

Als er een fout optreedt bij het uitstellen of negeren van een aanbeveling, hebt u mogelijk niet de juiste machtigingen. Zorg ervoor dat u ten minste de Inzender toegang hebt tot de betrokken resource van de aanbeveling die u uitstelt of niet meer wilt gebruiken. U kunt dit probleem oplossen door te leren hoe u [een gebruiker toegang verleent](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt een overzicht gegeven van de manier waarop Advisor gebruikmaakt van RBAC voor het beheren van gebruikers machtigingen en het oplossen van veelvoorkomende problemen. Zie voor meer informatie over Advisor:

- [Wat is Azure Advisor?](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Aan de slag met Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-get-started)
