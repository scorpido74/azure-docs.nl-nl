---
title: Machtigingen in azure Sentinel | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe Azure Sentinel gebruikmaakt van op rollen gebaseerd toegangs beheer om machtigingen toe te wijzen aan gebruikers en de toegestane acties voor elke rol te identificeren.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2020
ms.author: yelevin
ms.openlocfilehash: 9f9a84726b54569d612a94f183531567b2242ff5
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87127159"
---
# <a name="permissions-in-azure-sentinel"></a>Machtigingen in azure Sentinel

Azure Sentinel maakt gebruik [van op rollen gebaseerde Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md) om [ingebouwde rollen](../role-based-access-control/built-in-roles.md)te bieden   die kunnen worden toegewezen aan gebruikers, groepen en services in Azure.

Gebruik RBAC om rollen binnen uw beveiligings team te maken en toe te wijzen om de juiste toegang tot Azure Sentinel te verlenen. De verschillende rollen bieden een nauw keurige controle over wat gebruikers van Azure Sentinel kunnen zien en doen. RBAC-rollen kunnen rechtstreeks worden toegewezen in de Azure Sentinel-werk ruimte (Zie de opmerking hieronder) of in een abonnement of resource groep waartoe de werk ruimte behoort, die wordt overgenomen door Azure Sentinel.

## <a name="roles-for-working-in-azure-sentinel"></a>Rollen voor het werken in azure-Sentinel

### <a name="azure-sentinel-specific-roles"></a>Sentinel-specifieke rollen van Azure

Er zijn drie speciale, ingebouwde Azure-Sentinel rollen.

**Alle ingebouwde Azure Sentinel-rollen verlenen Lees toegang tot de gegevens in uw Azure Sentinel-werk ruimte.**

- [Azure Sentinel Reader](../role-based-access-control/built-in-roles.md#azure-sentinel-reader) kan gegevens, incidenten, werkmappen en andere Azure Sentinel-bronnen weer geven.

- [Azure Sentinel responder](../role-based-access-control/built-in-roles.md#azure-sentinel-responder) kan naast het bovenstaande ook incidenten beheren (toewijzen, verwijderen, enz.)

- [Azure Sentinel contributor](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) kan naast het bovenstaande ook werkmappen, analyse regels en andere Azure-Sentinel-resources maken en bewerken.

> [!NOTE]
>
> - Voor de beste resultaten moeten deze rollen worden toegewezen aan de **resource groep** die de Azure Sentinel-werk ruimte bevat. Op deze manier worden de rollen toegepast op alle resources die zijn geïmplementeerd ter ondersteuning van Azure Sentinel, omdat deze resources ook in dezelfde resource groep moeten worden geplaatst.
>
> - Een andere mogelijkheid is om de rollen rechtstreeks toe te wijzen aan de Azure Sentinel- **werk ruimte** zelf. Als u dit doet, moet u ook dezelfde rollen toewijzen aan de resource van de SecurityInsights- **oplossing** in die werk ruimte. Mogelijk moet u ze ook toewijzen aan andere resources, en moet u voortdurend roltoewijzingen voor resources beheren.

### <a name="additional-roles-and-permissions"></a>Aanvullende rollen en machtigingen

Gebruikers met bepaalde taak vereisten moeten mogelijk aanvullende rollen of specifieke machtigingen toewijzen om hun taken uit te voeren.

- Werken met playbooks om reacties op bedreigingen te automatiseren

    Azure Sentinel maakt gebruik van **playbooks** voor automatische reactie op bedreigingen. Playbooks zijn gebaseerd op **Azure Logic apps**en zijn een afzonderlijke Azure-resource. U kunt het beste aan specifieke leden van uw beveiligings team toewijzen om Logic Apps te gebruiken voor via-bewerkingen (Security Orchestration, Automation en Response). U kunt de rol [Inzender voor logische apps](../role-based-access-control/built-in-roles.md#logic-app-contributor) of de rol van [operator voor logische apps](../role-based-access-control/built-in-roles.md#logic-app-operator) gebruiken om expliciete machtigingen toe te wijzen voor het gebruik van playbooks.

- Gegevens bronnen verbinden met Azure Sentinel

    Als een gebruiker **gegevensconnectors**wilt toevoegen, moet u de schrijf machtigingen van de gebruiker toewijzen aan de Azure Sentinel-werk ruimte. Let ook op de vereiste extra machtigingen voor elke connector, zoals vermeld op de relevante connector pagina.

Zie de [onderstaande tabel](#roles-and-allowed-actions)voor een vergelijking naast elkaar.

### <a name="other-roles-you-might-see-assigned"></a>Andere functies die u kunt zien, zijn toegewezen

Bij het toewijzen van Azure Sentinel-specifieke RBAC-rollen kunt u andere Azure-en Log Analytics RBAC-rollen vinden die voor andere doel einden aan gebruikers zijn toegewezen. Houd er rekening mee dat deze rollen een bredere set machtigingen verlenen die toegang hebben tot uw Azure Sentinel-werk ruimte en andere resources:

- **Azure-rollen:** [eigenaar](../role-based-access-control/built-in-roles.md#owner), [bijdrager](../role-based-access-control/built-in-roles.md#contributor)en [lezer](../role-based-access-control/built-in-roles.md#reader). Azure-rollen verlenen toegang tot alle Azure-resources, waaronder Log Analytics-werk ruimten en Azure-Sentinel-resources.

- **Log Analytics rollen:** [Log Analytics Inzender](../role-based-access-control/built-in-roles.md#log-analytics-contributor) en [log Analytics lezer](../role-based-access-control/built-in-roles.md#log-analytics-reader). Log Analytics rollen verlenen toegang tot uw Log Analytics-werk ruimten. 

Bijvoorbeeld, een gebruiker aan wie de rol van **Azure Sentinel Reader** is toegewezen, maar niet de rol **Azure Sentinel contributor** , kan nog steeds items in azure Sentinel bewerken als de rol **Inzender** op Azure-niveau is toegewezen. Als u daarom machtigingen voor een gebruiker alleen in azure Sentinel wilt verlenen, moet u de vorige machtigingen van deze gebruiker zorgvuldig verwijderen en ervoor zorgen dat u geen toegang hebt tot een andere bron.

## <a name="roles-and-allowed-actions"></a>Rollen en toegestane acties

De volgende tabel bevat een overzicht van de rollen en toegestane acties in azure Sentinel. 

| Rol | Playbooks maken en uitvoeren| Werkmappen, analytische regels en andere Azure Sentinel-resources maken en bewerken | Incidenten beheren (sluiten, toewijzen, enz.) | Gegevens, incidenten, werkmappen en andere Azure Sentinel-bronnen weer geven |
|---|---|---|---|---|
| Azure Sentinel Reader | -- | -- | -- | &#10003; |
| Azure Sentinel Responder | -- | -- | &#10003; | &#10003; |
| Azure Sentinel Contributor | -- | &#10003; | &#10003; | &#10003; |
| Inzender voor Azure Sentinel contributor + Logic apps | &#10003; | &#10003; | &#10003; | &#10003; |

## <a name="custom-roles-and-advanced-rbac"></a>Aangepaste rollen en geavanceerde RBAC

- Naast of in plaats van het gebruik van ingebouwde RBAC-rollen, kunt u aangepaste Azure-rollen maken voor Azure Sentinel. Aangepaste Azure-rollen voor Azure Sentinel worden op dezelfde manier gemaakt als bij het maken van andere [aangepaste Azure RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) -rollen, op basis van [specifieke machtigingen voor Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) en [Azure log Analytics-resources](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights).

- U kunt het Log Analytics geavanceerd toegangs beheer op basis van rollen gebruiken voor de gegevens in uw Azure Sentinel-werk ruimte. Dit omvat zowel RBAC op gegevens type als resource gerichte RBAC. Zie [logboek gegevens en-werk ruimten beheren in azure monitor](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)voor meer informatie over log Analytics rollen.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u kunt werken met rollen voor Azure Sentinel-gebruikers en wat elke rol gebruikers kan doen.

* [Azure-verklikker blog](https://aka.ms/azuresentinelblog). Raadpleeg de blogberichten over beveiliging en naleving in Azure.
