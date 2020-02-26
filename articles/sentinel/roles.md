---
title: Machtigingen in azure Sentinel | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe Azure Sentinel gebruikmaakt van op rollen gebaseerd toegangs beheer om machtigingen toe te wijzen aan gebruikers en de toegestane acties voor elke rol te identificeren.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: yelevin
ms.openlocfilehash: e7629a53190433c6c331ce372476b0ed768fc5eb
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587172"
---
# <a name="permissions-in-azure-sentinel"></a>Machtigingen in azure Sentinel

Azure Sentinel maakt gebruik [van op rollen gebaseerde Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md)om [ingebouwde rollen](../role-based-access-control/built-in-roles.md) te bieden die kunnen worden toegewezen aan gebruikers, groepen en services in Azure.

Met RBAC kunt u rollen binnen uw beveiligings team gebruiken en maken om de juiste toegang tot Azure Sentinel te verlenen. Op basis van de rollen hebt u nauw keurige controle over wat gebruikers met toegang tot Azure Sentinel kunnen zien. U kunt RBAC-rollen rechtstreeks aan de Azure-Sentinel-werk ruimte toewijzen, of op een abonnement of resource groep waartoe de werk ruimte behoort.

Er zijn drie specifieke, ingebouwde Azure Sentinel-rollen.  
**Alle ingebouwde Azure Sentinel-rollen verlenen Lees toegang tot de gegevens in uw Azure Sentinel-werk ruimte.**
- [Azure Sentinel Reader](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel responder](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure Sentinel-bijdrager](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Naast Azure Sentinel dedicated RBAC-rollen zijn er Azure-en Log Analytics RBAC-rollen die een grotere set machtigingen kunnen verlenen die toegang hebben tot uw Azure Sentinel-werk ruimte en andere resources:

- **Azure-rollen:** [eigenaar](../role-based-access-control/built-in-roles.md#owner), [bijdrager](../role-based-access-control/built-in-roles.md#contributor)en [lezer](../role-based-access-control/built-in-roles.md#reader). Azure-rollen verlenen toegang tot alle Azure-resources, waaronder Log Analytics-werk ruimten en Azure-Sentinel-resources.

-   **Log Analytics rollen:** [Log Analytics Inzender](../role-based-access-control/built-in-roles.md#log-analytics-contributor), [log Analytics Reader](../role-based-access-control/built-in-roles.md#log-analytics-reader). Log Analytics rollen verlenen toegang tot alle Log Analytics-werk ruimten. 

> [!NOTE]
> Log Analytics rollen verlenen ook lees toegang voor alle Azure-resources, maar ze kunnen alleen schrijf machtigingen toewijzen aan Log Analytics-resources.


Bijvoorbeeld, een gebruiker die is toegewezen met rollen van **Azure Sentinel Reader** en **Azure contributor** (geen **Azure Sentinel contributor**), kan gegevens in azure Sentinel bewerken, hoewel ze alleen machtigingen voor **Sentinel Reader** hebben. Als u dus alleen machtigingen voor een Azure-Sentinel wilt verlenen, moet u de vorige machtigingen van deze gebruiker echter zorgvuldig verwijderen, zodat u zeker weet dat u de benodigde machtiging voor een andere resource niet hebt verbroken.

> [!NOTE]
>- Azure Sentinel maakt gebruik van playbooks voor automatische reactie op bedreigingen. Playbooks maakt gebruik van Azure Logic Apps en zijn een afzonderlijke Azure-resource. U kunt bepaalde leden van uw beveiligings team toewijzen met de optie om Logic Apps te gebruiken voor via-bewerkingen (Security Orchestration, Automation en Response). U kunt de rol [Inzender voor logische apps](../role-based-access-control/built-in-roles.md#logic-app-contributor) of de rol van [operator voor logische apps](../role-based-access-control/built-in-roles.md#logic-app-operator) gebruiken om expliciete machtigingen toe te wijzen voor het gebruik van playbooks.
>- Als u gegevens connectors wilt toevoegen, zijn de benodigde rollen voor elke connector per connector type en worden ze weer gegeven op de relevante connector-pagina. Daarnaast moet u voor de Azure-Sentinel-werk ruimte een schrijf machtiging hebben om verbinding te maken met een gegevens bron.



## <a name="roles-and-allowed-actions"></a>Rollen en toegestane acties

In de volgende tabel worden de rollen en toegestane acties in azure Sentinel weer gegeven. Een X geeft aan dat de actie is toegestaan voor die rol.

| Rol | Playbooks maken en uitvoeren| Dash boards, analytische regels en andere Azure Sentinel-resources maken en bewerken | Incidenten beheren (sluiten, toewijzen, enz.) | Gegevens, incidenten, Dash boards en andere Azure Sentinel-bronnen weer geven |
|--- |---|---|---|---|
| Azure Sentinel Reader | -- | -- | -- | X |
| Azure Sentinel responder|--|--| X | X |
| Azure Sentinel-bijdrager | -- | X | X | X |
| Inzender voor Azure Sentinel contributor + Logic apps | X | X | X | X |


> [!NOTE]
> - We raden u aan de rol toe te wijzen die gebruikers minimaal nodig hebben om hun taken uit te voeren. Wijs bijvoorbeeld de rol Azure Sentinel contributor alleen toe aan gebruikers die regels of Dash boards moeten maken.
> - U wordt aangeraden machtigingen voor Azure Sentinel in het bereik van de resource groep in te stellen, zodat de gebruiker toegang kan hebben tot alle Azure-Sentinel-werk ruimten in dezelfde resource groep.
>
## <a name="building-custom-rbac-roles"></a>Aangepaste RBAC-rollen bouwen

Naast of in plaats van het gebruik van ingebouwde RBAC-rollen, kunt u aangepaste RBAC-rollen maken voor Azure Sentinel. Aangepaste RBAC-rollen voor Azure Sentinel worden op dezelfde manier gemaakt als bij het maken van andere [aangepaste Azure RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) -rollen, op basis van [specifieke machtigingen voor Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) en [Azure log Analytics-resources](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights).

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>Geavanceerde RBAC voor de gegevens die u in azure-Sentinel opslaat
  
U kunt het Log Analytics geavanceerd toegangs beheer op basis van rollen gebruiken voor de gegevens in uw Azure Sentinel-werk ruimte. Dit geldt zowel voor op rollen gebaseerd toegangs beheer per gegevens type als voor resource gerichte toegangs beheer op basis van rollen. Zie [logboek gegevens en-werk ruimten beheren in azure monitor](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)voor meer informatie over log Analytics rollen.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u kunt werken met rollen voor Azure Sentinel-gebruikers en wat elke rol gebruikers kan doen.

* [Azure-verklikker blog](https://aka.ms/azuresentinelblog). Raadpleeg de blogberichten over beveiliging en naleving in Azure.
