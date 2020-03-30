---
title: Machtigingen in Azure Sentinel | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe Azure Sentinel op rollen gebaseerd toegangsbeheer gebruikt om machtigingen toe te wijzen aan gebruikers en de toegestane acties voor elke rol identificeert.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587172"
---
# <a name="permissions-in-azure-sentinel"></a>Machtigingen in Azure Sentinel

Azure Sentinel gebruikt [RBAC(Role-Based Access Control)](../role-based-access-control/role-assignments-portal.md)om [ingebouwde rollen](../role-based-access-control/built-in-roles.md) te bieden die kunnen worden toegewezen aan gebruikers, groepen en services in Azure.

Met RBAC u rollen binnen uw beveiligingsteam gebruiken en maken om de juiste toegang tot Azure Sentinel te verlenen. Op basis van de rollen hebt u een fijnmazige controle over wat gebruikers met toegang tot Azure Sentinel kunnen zien. U RBAC-rollen rechtstreeks toewijzen in de Azure Sentinel-werkruimte of aan een abonnement of brongroep waartoe de werkruimte behoort.

Er zijn drie specifieke ingebouwde Azure Sentinel-rollen.  
**Alle ingebouwde azure sentinel-rollen verlenen leestoegang tot de gegevens in uw Azure Sentinel-werkruimte.**
- [Azure Sentinel-lezer](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel-responder](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure Sentinel-bijdrager](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Naast azure Sentinel-specifieke RBAC-rollen zijn er Azure- en Log Analytics RBAC-rollen die een bredere set machtigingen kunnen verlenen die toegang bevatten tot uw Azure Sentinel-werkruimte en andere bronnen:

- **Azure-rollen:** [eigenaar,](../role-based-access-control/built-in-roles.md#owner) [bijdrager](../role-based-access-control/built-in-roles.md#contributor)en [lezer](../role-based-access-control/built-in-roles.md#reader). Azure-rollen bieden toegang tot al uw Azure-bronnen, waaronder Logboekanalyse-werkruimten en Azure Sentinel-bronnen.

-   **Log Analytics-rollen:** [bijdrager Log Analytics](../role-based-access-control/built-in-roles.md#log-analytics-contributor), [Logboekanalyselezer](../role-based-access-control/built-in-roles.md#log-analytics-reader). Log Analytics-rollen bieden toegang tot al uw Log Analytics-werkruimten. 

> [!NOTE]
> Log Analytics-rollen verlenen ook leestoegang voor alle Azure-bronnen, maar worden alleen schrijfmachtigingen toegewezen aan Log Analytics-bronnen.


Een gebruiker die is toegewezen met **Azure Sentinel-lezer-** en **Azure-bijdrager (niet** azure **sentinel-bijdrager)** kan bijvoorbeeld gegevens bewerken in Azure Sentinel, hoewel deze alleen **verklikkermachtigingen** hebben. Als u daarom alleen in Azure Sentinel machtigingen wilt verlenen aan een alleen in Azure Sentinel, moet u de voorafgaande machtigingen van deze gebruiker zorgvuldig verwijderen om ervoor te zorgen dat u geen benodigde machtigingsrol voor een andere bron breekt.

> [!NOTE]
>- Azure Sentinel gebruikt playbooks voor geautomatiseerde bedreigingsrespons. Playbooks maken gebruik van Azure Logic Apps en zijn een afzonderlijke Azure-bron. U specifieke leden van uw beveiligingsteam toewijzen met de optie om Logische Apps te gebruiken voor soar-bewerkingen (Security Orchestration, Automation en Response). U de rol [logic-app-inzender](../role-based-access-control/built-in-roles.md#logic-app-contributor) of de [operatorrol logic app](../role-based-access-control/built-in-roles.md#logic-app-operator) gebruiken om expliciete toestemming toe te wijzen voor het gebruik van playbooks.
>- Om gegevensconnectors toe te voegen, worden de benodigde rollen voor elke connector per connectortype weergegeven en worden deze weergegeven op de relevante connectorpagina. Om een gegevensbron met elkaar te verbinden, moet u bovendien schrijftoestemming hebben voor de Azure Sentinel-werkruimte.



## <a name="roles-and-allowed-actions"></a>Rollen en toegestane acties

In de volgende tabel worden rollen en toegestane acties weergegeven in Azure Sentinel. Een X geeft aan dat de actie is toegestaan voor die rol.

| Rol | Playbooks maken en uitvoeren| Dashboards, analytische regels en andere Azure Sentinel-bronnen maken en bewerken | Incidenten beheren (afwijzen, toewijzen, enz.) | Gegevens, incidenten, dashboards en andere Azure Sentinel-bronnen weergeven |
|--- |---|---|---|---|
| Azure Sentinel-lezer | -- | -- | -- | X |
| Azure Sentinel-responder|--|--| X | X |
| Azure Sentinel-bijdrager | -- | X | X | X |
| Azure Sentinel-bijdragerr + Logic App-bijdrager | X | X | X | X |


> [!NOTE]
> - We raden u aan de rol toe te wijzen die gebruikers minimaal nodig hebben om hun taken uit te voeren. Wijs de azure sentinel-bijdragerol bijvoorbeeld alleen toe aan gebruikers die regels of dashboards moeten maken.
> - We raden u aan machtigingen in te stellen voor Azure Sentinel in het bereik van de brongroep, zodat de gebruiker toegang heeft tot alle Azure Sentinel-werkruimten in dezelfde brongroep.
>
## <a name="building-custom-rbac-roles"></a>Aangepaste RBAC-rollen bouwen

Naast of in plaats van ingebouwde RBAC-rollen te gebruiken, u aangepaste RBAC-rollen voor Azure Sentinel maken. Aangepaste RBAC-rollen voor Azure Sentinel worden op dezelfde manier gemaakt als andere [aangepaste Azure RBAC-rollen,](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) op basis van [specifieke machtigingen voor Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) en Azure Log [Analytics-bronnen.](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>Geavanceerde RBAC voor de gegevens die u in Azure Sentinel opslaat
  
U het geavanceerde toegangsbeheer voor rollen van Log Analytics gebruiken voor de gegevens in uw Azure Sentinel-werkruimte. Dit omvat zowel op rollen gebaseerde toegangscontrole per gegevenstype als op resources gerichte role-based toegangscontrole. Zie [Logboekgegevens en werkruimten beheren in Azure Monitor](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)voor meer informatie over loganalytics-rollen.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u met rollen voor Azure Sentinel-gebruikers werken en wat elke rol gebruikers in staat stelt te doen.

* [Azure Sentinel Blog](https://aka.ms/azuresentinelblog). Raadpleeg de blogberichten over beveiliging en naleving in Azure.
