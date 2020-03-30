---
title: Rapport van het controlelogboek weergeven voor Azure AD-rollen in Azure AD PIM | Microsoft Documenten
description: Meer informatie over het weergeven van de auditloggeschiedenis voor Azure AD-rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/07/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b8aef68e0f61e6ca995fc2bb362d59aba73ead2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329499"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Controlegeschiedenis weergeven voor Azure AD-rollen in Privileged Identity Management

U de auditgeschiedenis van Privileged Identity Management (PIM) gebruiken om alle roltoewijzingen en activeringen in de afgelopen 30 dagen te bekijken voor alle bevoorrechte rollen. Als u de volledige controlegeschiedenis van de activiteit wilt zien in uw Azure Active Directory-organisatie (Azure AD), inclusief beheerder, eindgebruiker en synchronisatieactiviteit, u de [beveiligings- en activiteitenrapporten](../reports-monitoring/overview-reports.md)van Azure Active Directory gebruiken.

## <a name="determine-your-version-of-pim"></a>Bepaal uw versie van PIM

Vanaf november 2019 wordt het gedeelte Azure AD-rollen van Privileged Identity Management bijgewerkt naar een nieuwe versie die overeenkomt met de ervaringen voor Azure-bronrollen. Dit zorgt voor extra functies en [wijzigingen in de bestaande API.](azure-ad-roles-features.md#api-changes) Terwijl de nieuwe versie wordt uitgerold, zijn welke procedures u in dit artikel volgt, afhankelijk van de versie van Privileged Identity Management die u momenteel hebt. Volg de stappen in deze sectie om te bepalen welke versie van Privileged Identity Management u hebt. Nadat u uw versie van Privileged Identity Management hebt weten, u de procedures in dit artikel selecteren die overeenkomen met die versie.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met een gebruiker die zich in de rol [Van Privileged role administrator](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) bevindt.
1. Azure **AD Privileged Identity Management openen**. Als u boven aan de overzichtspagina een banner hebt staan, volgt u de instructies op het tabblad **Nieuwe versie** van dit artikel. Volg anders de instructies op het tabblad **Vorige versie.**

    [![Azure AD-rollen nieuwe versie](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Het tabblad voor uw versie selecteren")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

# <a name="new-version"></a>[Nieuwe versie](#tab/new)

Volg deze stappen om de controlegeschiedenis voor Azure AD-rollen weer te geven.

## <a name="view-resource-audit-history"></a>De auditgeschiedenis van resources weergeven

Resourcecontrole geeft u een overzicht van alle activiteiten die zijn gekoppeld aan uw Azure AD-rollen.

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **Resourceaudit**.

1. Filter de geschiedenis met een vooraf gedefinieerde datum of aangepast bereik.

    ![Lijst met broncontrole met filters](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Mijn controle bekijken

Mijn audit stelt u in staat om uw persoonlijke rolactiviteit te bekijken.

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer de resource waarvoor u de controlegeschiedenis wilt weergeven.

1. Selecteer **Mijn controle**.

1. Filter de geschiedenis met een vooraf gedefinieerde datum of aangepast bereik.

    ![Controlelijst voor de huidige gebruiker](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-version"></a>[Vorige versie](#tab/previous)

## <a name="view-audit-history"></a>Controlegeschiedenis weergeven

Volg deze stappen om de controlegeschiedenis voor Azure AD-rollen weer te geven.

1. Meld u aan bij [azure portal](https://portal.azure.com/) met een gebruiker die lid is van de functie Privileged [Role Administrator.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **controlegeschiedenis van directoryrollen**.

    Afhankelijk van uw controlegeschiedenis wordt een kolomdiagram weergegeven, samen met de totale activeringen, maximale activeringen per dag en gemiddelde activeringen per dag.

    [![Azure AD-rollen nieuwe versie](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Controlegeschiedenis van directoryrollen weergeven")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Onder aan de pagina wordt een tabel weergegeven met informatie over elke actie in de beschikbare controlegeschiedenis. De kolommen hebben de volgende betekenissen:

    | Kolom | Beschrijving |
    | --- | --- |
    | Time | Toen de actie plaatsvond. |
    | Requestor | Gebruiker die de activering of wijziging van de rol heeft aangevraagd. Als de waarde **Azure System**is, raadpleegt u de azure-controlegeschiedenis voor meer informatie. |
    | Actie | Acties van de aanvrager. Acties kunnen Toewijzen, Toewijzen, Activeren, Deactiveren of AddedOutsidePIM omvatten. |
    | Lid | Gebruiker die een rol activeert of aan een rol is toegewezen. |
    | Rol | Rol toegewezen of geactiveerd door de gebruiker. |
    | Redenering | Tekst die tijdens activering in het redenveld is ingevoerd. |
    | Verloopdatum | Wanneer een geactiveerde rol verloopt. Geldt alleen voor in aanmerking komende roltoewijzingen. |

1. Als u de controlegeschiedenis wilt sorteren, klikt u op de knoppen **Tijd,** **Actie**en **Rol.**

## <a name="filter-audit-history"></a>Controlegeschiedenis filteren

1. Klik boven aan de pagina controlegeschiedenis op de knop **Filter.**

    Het deelvenster **Grafiekparameters bijwerken** wordt weergegeven.

1. Selecteer **in tijdbereik**een tijdsbereik.

1. Schakel in **Rollen**de selectievakjes in om aan te geven welke rollen u wilt weergeven.

    ![Deelvenster Grafiekparameters bijwerken](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Selecteer **Gereed** om de gefilterde controlegeschiedenis weer te geven.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Reden, goedkeurder en ticketnummer voor goedkeuringsevenementen

1. Meld u aan bij de [Azure-portal](https://aad.portal.azure.com) met machtigingen voor bevoegdheden voor privileged role administrator en open Azure AD.
1. Selecteer **Auditlogboeken**.
1. Gebruik het **filter Service** om alleen controlegebeurtenissen weer te geven voor de service Privileged identity Management. Op de pagina **Controlelogboeken** u het als:

    - Bekijk de reden voor een controle-gebeurtenis in de kolom **Statusreden.**
    - Zie de goedkeuringsfunctie in de kolom **Geïnitieerd door (actor)** voor de gebeurtenis 'Lid toevoegen aan goedgekeurde rolaanvraag'.

    [![Azure AD-rollen nieuwe versie](media/pim-how-to-use-audit-log/filter-audit-logs.png "Het controlelogboek voor de PIM-service filteren")](media/pim-how-to-use-audit-log/filter-audit-logs.png)

1. Selecteer een auditlogboekgebeurtenis om het ticketnummer te zien op het tabblad **Activiteit** van het deelvenster **Details.**
  
    [![Azure AD-rollen nieuwe versie](media/pim-how-to-use-audit-log/audit-event-ticket-number.png "Controleer het ticketnummer voor de auditgebeurtenis")](media/pim-how-to-use-audit-log/audit-event-ticket-number.png)

1. U de aanvrager (persoon die de rol activeert) bekijken op het tabblad **Doelen** van het deelvenster **Details** voor een controlegebeurtenis. Er zijn twee doeltypen voor Azure AD-rollen:

    - De rol (**Type** = Rol)
    - De aanvrager (**Type** = Gebruiker)

De gebeurtenis voor het controlelogboek direct boven de goedkeuringsgebeurtenis is doorgaans een gebeurtenis voor 'Lid toevoegen aan voltooide rol' waarbij de **geïnitieerde door (actor)** de aanvrager is. In de meeste gevallen hoeft u de aanvrager niet in de goedkeuringsaanvraag te vinden vanuit een controleperspectief.

---

## <a name="next-steps"></a>Volgende stappen

- [Activiteits- en controlegeschiedenis weergeven voor Azure-resourcerollen in Privileged Identity Management](azure-pim-resource-rbac.md)
