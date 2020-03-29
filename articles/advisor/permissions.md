---
title: Machtigingen in Azure Advisor
description: Advisor-machtigingen en hoe ze uw mogelijkheid om abonnementen te configureren kunnen blokkeren of aanbevelingen kunnen uitstellen of afwijzen.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: c850d757044066d5c4a793e076436906d715833c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422323"
---
# <a name="permissions-in-azure-advisor"></a>Machtigingen in Azure Advisor

Azure Advisor biedt aanbevelingen op basis van het gebruik en de configuratie van uw Azure-bronnen en -abonnementen. Advisor gebruikt de [ingebouwde rollen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) van [RBAC (Role-Based Access Control)](https://docs.microsoft.com/azure/role-based-access-control/overview) om uw toegang tot aanbevelingen en Advisor-functies te beheren. 

## <a name="roles-and-their-access"></a>Rollen en hun toegang

In de volgende tabel worden de rollen en de toegang die ze hebben binnen Advisor gedefinieerd:

| **Rol** | **Aanbevelingen bekijken** | **Regels bewerken** | **Abonnementsconfiguratie bewerken** | **Configuratie van resourcegroep bewerken**| **Aanbevelingen afwijzen en uitstellen**|
|---|:---:|:---:|:---:|:---:|:---:|
|Eigenaar van een abonnement|**X**|**X**|**X**|**X**|**X**|
|Inzender van een abonnement|**X**|**X**|**X**|**X**|**X**|
|Abonnementlezer|**X**|--|--|--|--|
|Eigenaar van resourcegroep|**X**|--|--|**X**|**X**|
|Bijdrage groep resourcegroep|**X**|--|--|**X**|**X**|
|Brongroeplezer|**X**|--|--|--|--|
|Broneigenaar|**X**|--|--|--|**X**|
|Resourcebijdrager|**X**|--|--|--|**X**|
|Resourcelezer|**X**|--|--|--|--|

> [!NOTE]
> De toegang tot aanbevelingen voor weergave is afhankelijk van uw toegang tot de door de aanbeveling getroffen bron.

## <a name="permissions-and-unavailable-actions"></a>Machtigingen en niet-beschikbare acties

Het ontbreken van de juiste machtigingen kan uw vermogen om acties uit te voeren in Advisor blokkeren. Hieronder volgen enkele veelvoorkomende problemen.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Kan geen abonnementen of brongroepen configureren

Wanneer u abonnementen of resourcegroepen probeert te configureren in Advisor, u zien dat de optie om op te nemen of uit te sluiten is uitgeschakeld. Deze status geeft aan dat u niet over voldoende toestemming beschikt voor die resourcegroep of -abonnement. Als u dit probleem wilt oplossen, leest u hoe [u een gebruiker toegang verleent.](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Kan een aanbeveling niet uitstellen of afwijzen

Als u een fout ontvangt wanneer u een aanbeveling probeert uit te stellen of te verwijderen, hebt u mogelijk niet voldoende machtigingen. Zorg ervoor dat u ten minste toegang hebt tot de door de betreffende bron van de aanbeveling die u uitstelt of afwijst. Als u dit probleem wilt oplossen, leest u hoe [u een gebruiker toegang verleent.](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

## <a name="next-steps"></a>Volgende stappen

In dit artikel vindt u een overzicht van hoe Advisor RBAC gebruikt om gebruikersmachtigingen te beheren en veelvoorkomende problemen op te lossen. Zie voor meer informatie over Advisor:

- [Wat is Azure Advisor?](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Aan de slag met Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-get-started)
