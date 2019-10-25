---
title: De controle geschiedenis voor Azure AD-rollen in PIM-Azure Active Directory weer geven | Microsoft Docs
description: Meer informatie over het weer geven van de controle geschiedenis voor Azure AD-rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9aa1c5121c723148884584ca3f00522c1c459859
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808998"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>De controle geschiedenis voor Azure AD-rollen in PIM weer geven

U kunt de controle geschiedenis van de Privileged Identity Management (PIM) gebruiken om alle roltoewijzingen en activeringen in de afgelopen 30 dagen voor alle geprivilegieerde rollen weer te geven. Als u de volledige controle geschiedenis van de activiteiten in uw Azure Active Directory-organisatie (Azure AD) wilt zien, met inbegrip van beheerder, eind gebruiker en synchronisatie activiteit, kunt u de [Azure Active Directory beveiligings-en activiteiten rapporten](../reports-monitoring/overview-reports.md)gebruiken.

## <a name="view-audit-history"></a>Controlegeschiedenis weergeven

Volg deze stappen om de controle geschiedenis voor Azure AD-rollen weer te geven.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) met een gebruiker die lid is van de beheerdersrol [privileged Role](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

1. Open **Azure AD privileged Identity Management**.

1. Klik op **Azure AD-rollen**.

1. Klik op **controle geschiedenis van Directory rollen**.

    Afhankelijk van de controle geschiedenis wordt een kolom diagram samen met de totale activeringen, maximum aantal activeringen per dag en gemiddeld aantal activeringen per dag weer gegeven.

    ![Controle geschiedenis van Directory rollen](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Onder aan de pagina wordt een tabel weer gegeven met informatie over elke actie in de beschik bare controle geschiedenis. De kolommen hebben de volgende betekenissen:

    | Kolom | Beschrijving |
    | --- | --- |
    | Tijd | Wanneer de actie is uitgevoerd. |
    | Aanvrager | Gebruiker die de functie activeert of wijzigt. Als de waarde **Azure System**is, controleert u de controle geschiedenis van Azure voor meer informatie. |
    | Bewerking | Acties die door de aanvrager worden uitgevoerd. Acties kunnen toewijzen, intrekken, activeren, deactiveren of AddedOutsidePIM bevatten. |
    | Lid | Gebruiker die een rol activeert of toewijst. |
    | Rol | De rol die is toegewezen of geactiveerd door de gebruiker. |
    | Redeneren | Tekst die tijdens de activering is ingevoerd in het veld reden. |
    | verval | Wanneer een geactiveerde rol verloopt. Is alleen van toepassing op in aanmerking komende roltoewijzingen. |

1. Als u de controle geschiedenis wilt sorteren, klikt u op de knoppen **tijd**, **actie**en **rol** .

## <a name="filter-audit-history"></a>Controle geschiedenis filteren

1. Klik boven aan de pagina controle geschiedenis op de knop **filteren** .

    Het deel venster **grafiek parameters bijwerken** wordt weer gegeven.

1. Selecteer een tijds bereik in **tijds bereik**.

1. In **rollen**selecteert u de selectie vakjes om de rollen aan te geven die u wilt weer geven.

    ![Deel venster grafiek parameters bijwerken](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Klik op **gereed** om de gefilterde controle geschiedenis weer te geven.

## <a name="next-steps"></a>Volgende stappen

- [Activiteiten en controle geschiedenis voor Azure-resource rollen in Privileged Identity Management weer geven](azure-pim-resource-rbac.md)
