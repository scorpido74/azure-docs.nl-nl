---
title: Controlerapport weergeven voor Azure-bronrollen in Privileged Identity Management (PIM) - Azure AD | Microsoft Documenten
description: Activiteits- en controlegeschiedenis weergeven voor Azure-resourcerollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bf65ad595fb1ab70eb6613b6d54ac2a4f69141e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329583"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Activiteits- en controlegeschiedenis weergeven voor Azure-resourcerollen in Privileged Identity Management

Met Azure Active Directory (Azure AD) Privileged Identity Management (PIM) u activiteit, activeringen en controlegeschiedenis voor Azure-resourcesrollen binnen uw organisatie weergeven. Dit omvat abonnementen, resourcegroepen en zelfs virtuele machines. Elke bron binnen de Azure-portal die gebruikmaakt van de azure-functie voor toegangsbeheer, kan profiteren van de beveiligings- en levenscyclusbeheermogelijkheden in Privileged Identity Management.

> [!NOTE]
> Als uw organisatie beheerfuncties heeft uitbesteed aan een serviceprovider die [azure-gedelegeerd resourcebeheer](../../lighthouse/concepts/azure-delegated-resource-management.md)gebruikt, worden roltoewijzingen die zijn geautoriseerd door die serviceprovider, hier niet weergegeven.

## <a name="view-activity-and-activations"></a>Activiteit en activeringen weergeven

Als u wilt zien welke acties een specifieke gebruiker heeft uitgevoerd in verschillende bronnen, u de Azure-bronactiviteit bekijken die is gekoppeld aan een bepaalde activeringsperiode.

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Azure-resources**.

1. Selecteer de resource waarvoor u activiteit en activeringen wilt weergeven.

1. Selecteer **Rollen** of **leden**.

1. Selecteer een gebruiker.

    U ziet een overzicht van de acties van de gebruiker in Azure-bronnen op datum. Het toont ook de recente rolactiveringen over diezelfde periode.

    ![Gebruikersgegevens met overzicht van resourceactiviteit en functieactiveringen](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Selecteer een specifieke functieactivering om details en bijbehorende Azure-resourceactiviteit te zien die zijn opgetreden terwijl die gebruiker actief was.

    [![Rolactivering geselecteerd en activiteitsdetails](media/azure-pim-resource-rbac/export-membership.png "Rolactivering geselecteerd en activiteitsdetails")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>Rolopdrachten exporteren met kinderen

Mogelijk hebt u een nalevingsvereiste wanneer u een volledige lijst met roltoewijzingen aan auditors moet opgeven. Met Privileged Identity Management u roltoewijzingen opvragen bij een specifieke resource, waaronder roltoewijzingen voor alle onderliggende resources. Voorheen was het moeilijk voor beheerders om een volledige lijst met roltoewijzingen voor een abonnement te krijgen en moesten ze roltoewijzingen exporteren voor elke specifieke resource. Met Privileged Identity Management u vragen stellen voor alle actieve en in aanmerking komende roltoewijzingen in een abonnement, inclusief roltoewijzingen voor alle resourcegroepen en resources.

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Azure-resources**.

1. Selecteer de resource waarvoor u roltoewijzingen wilt exporteren, zoals een abonnement.

1. Selecteer **Leden**.

1. Selecteer **Exporteren** om het deelvenster Lidmaatschap exporteren te openen.

    [![Lidmaatschapsvenster exporteren om alle leden te exporteren](media/azure-pim-resource-rbac/export-membership.png "Lidmaatschapspagina exporteren om alle leden te exporteren")](media/azure-pim-resource-rbac/export-membership.png)

1. Selecteer **Alle leden exporteren** om alle roltoewijzingen in een CSV-bestand te exporteren.

    ![Geëxporteerde roltoewijzingen in CSV-bestand als weergave in Excel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>De auditgeschiedenis van resources weergeven

Resourcecontrole geeft u een overzicht van alle rolactiviteiten voor een resource.

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Azure-resources**.

1. Selecteer de resource waarvoor u de controlegeschiedenis wilt weergeven.

1. Selecteer **Resourceaudit**.

1. Filter de geschiedenis met een vooraf gedefinieerde datum of aangepast bereik.

    [![Lijst met broncontrole met filters](media/azure-pim-resource-rbac/rbac-resource-audit.png "Lijst met broncontrole met filters")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Selecteer Activeren **(Toegewezen + geactiveerd)** voor **Controletype.**

    [![Lijst met broncontrole die is gefilterd op](media/azure-pim-resource-rbac/rbac-audit-activity.png "Lijst met broncontrole gefilterd op Activeren")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![de controlelijst van het audittype activeren, die wordt gefilterd op controletype activeren](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. Klik **onder Actie**op **(activiteit)** zodat een gebruiker de activiteitsdetails van die gebruiker in Azure-bronnen wilt zien.

    ![Details van gebruikersactiviteit voor een bepaalde actie](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Mijn controle bekijken

Mijn audit stelt u in staat om uw persoonlijke rolactiviteit te bekijken.

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Azure-resources**.

1. Selecteer de resource waarvoor u de controlegeschiedenis wilt weergeven.

1. Selecteer **Mijn controle**.

1. Filter de geschiedenis met een vooraf gedefinieerde datum of aangepast bereik.

    [![Controlelijst voor de huidige gebruiker](media/azure-pim-resource-rbac/my-audit-time.png "Controlelijst voor de huidige gebruiker")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> Toegang tot auditgeschiedenis vereist een globale beheerder of een rol van privileged role administrator.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Reden, goedkeurder en ticketnummer voor goedkeuringsevenementen

1. Meld u aan bij de [Azure-portal](https://aad.portal.azure.com) met machtigingen voor bevoegdheden voor privileged role administrator en open Azure AD.
1. Selecteer **Auditlogboeken**.
1. Gebruik het **filter Service** om alleen controlegebeurtenissen weer te geven voor de service Privileged identity Management. Op de pagina **Controlelogboeken** u het als:

    - Bekijk de reden voor een controle-gebeurtenis in de kolom **Statusreden.**
    - Zie de goedkeuringsfunctie in de kolom **Geïnitieerd door (actor)** voor de gebeurtenis 'Lid toevoegen aan goedgekeurde rolaanvraag'.

    [![Het controlelogboek voor de PIM-service filteren](media/azure-pim-resource-rbac/filter-audit-logs.png "Het controlelogboek voor de PIM-service filteren")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. Selecteer een auditlogboekgebeurtenis om het ticketnummer te zien op het tabblad **Activiteit** van het deelvenster **Details.**
  
    [![Controleer het ticketnummer voor het auditevenement](media/azure-pim-resource-rbac/audit-event-ticket-number.png "Controleer het ticketnummer voor de auditgebeurtenis")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)]

1. U de aanvrager (persoon die de rol activeert) bekijken op het tabblad **Doelen** van het deelvenster **Details** voor een controlegebeurtenis. Er zijn drie doeltypen voor Azure-bronrollen:

    - De rol (**Type** = Rol)
    - De aanvrager (**Type** = Andere)
    - De keurgever (**Type** = Gebruiker)

    [![Controleer het doeltype](media/azure-pim-resource-rbac/audit-event-target-type.png "Controleer het doeltype")](media/azure-pim-resource-rbac/audit-event-target-type.png)

De logboekgebeurtenis direct boven de goedkeuringsgebeurtenis is doorgaans een gebeurtenis voor 'Lid toevoegen aan voltooide rol' waarbij de **geïnitieerde door (actor)** de aanvrager is. In de meeste gevallen hoeft u de aanvrager niet in de goedkeuringsaanvraag te vinden vanuit een controleperspectief.

## <a name="next-steps"></a>Volgende stappen

- [Azure-bronrollen toewijzen in Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Aanvragen voor Azure-bronrollen in Privileged Identity Management goedkeuren of weigeren](pim-resource-roles-approval-workflow.md)
- [Controlegeschiedenis weergeven voor Azure AD-rollen in Privileged Identity Management](pim-how-to-use-audit-log.md)
