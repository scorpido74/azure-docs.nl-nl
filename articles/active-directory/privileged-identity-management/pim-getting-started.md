---
title: Beginnen met het gebruik van PIM-Azure Active Directory | Microsoft Docs
description: Meer informatie over het inschakelen en aan de slag met Azure AD Privileged Identity Management (PIM) in de Azure Portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 733743f4680f3197a1754d87b4672641b2321907
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399338"
---
# <a name="start-using-privileged-identity-management"></a>Aan de slag met Privileged Identity Management

Met Privileged Identity Management (PIM) kunt u de toegang in uw Azure Active Directory Azure AD-organisatie beheren, controleren en controleren. Dit bereik omvat toegang tot Azure-resources, Azure AD en andere micro soft-onlineservices, zoals Office 365 of Microsoft Intune.

In dit artikel wordt beschreven hoe u Privileged Identity Management kunt inschakelen en aan de slag kunt gaan.

## <a name="prerequisites"></a>Vereisten

Als u Privileged Identity Management wilt gebruiken, hebt u een van de volgende licenties nodig:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Zie [licentie vereisten voor het gebruik van privileged Identity Management](subscription-requirements.md)voor meer informatie.

## <a name="first-person-to-use-pim"></a>Eerste persoon die PIM moet gebruiken

Als u de eerste persoon bent die Privileged Identity Management in uw directory moet gebruiken, worden automatisch de rollen [beveiligings beheerder](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) en [bevoegde rol beheerder](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) in de Directory toegewezen. Alleen bevoegde beheerdersrol kunnen toewijzingen van Azure AD-rollen van gebruikers beheren. Daarnaast kunt u ervoor kiezen om de [beveiligings wizard](pim-security-wizard.md) uit te voeren die u door de eerste detectie-en toewijzings ervaring leidt.

## <a name="enable-pim"></a>PIM inschakelen

Als u Privileged Identity Management in uw Directory wilt gaan gebruiken, moet u Privileged Identity Management eerst inschakelen.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) als globale beheerder van uw Directory.

    U moet een globale beheerder zijn met een organisatie account (bijvoorbeeld @yourdomain.com), niet een Microsoft-account (bijvoorbeeld @outlook.com) om Privileged Identity Management in te scha kelen voor een map.

1. Klik op **alle services** en zoek de **Azure AD privileged Identity Management** -service.

    ![Azure AD Privileged Identity Management in alle services](./media/pim-getting-started/pim-all-services-find.png)

1. Klik om de Privileged Identity Management Quick Start te openen.

1. Klik in de lijst op **toestemming voor PIM**.

    ![Toestemming geven Privileged Identity Management om Privileged Identity Management in te scha kelen](./media/pim-getting-started/consent-pim.png)

1. Klik op **Mijn identiteit verifiëren** om uw identiteit te verifiëren met Azure MFA. U wordt gevraagd een account te kiezen.

    ![Kies een account venster om uw identiteit te verifiëren](./media/pim-getting-started/pick-account.png)

1. Als er meer informatie is vereist voor verificatie, wordt u begeleid bij het proces. Zie [hulp vragen bij verificatie in twee stappen](../user-help/multi-factor-authentication-end-user-troubleshoot.md)voor meer informatie.

    ![Meer informatie over de vereiste Window als uw organisatie meer informatie nodig heeft](./media/pim-getting-started/more-information-required.png)

    U wordt mogelijk gevraagd om verificatie via de telefoon.

    ![Pagina aanvullende beveiligings verificatie waarin wordt gevraagd hoe u contact met u kunt opnemen](./media/pim-getting-started/additional-security-verification.png)

1. Zodra u het verificatie proces hebt voltooid, klikt u op de knop **toestemming** .

1. In het bericht dat wordt weer gegeven, klikt u op **Ja** om toestemming te geven aan de privileged Identity Management-service.

    ![Toestemming geven Privileged Identity Management bericht voor het volt ooien van het toestemming proces](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>PIM registreren voor Azure AD-rollen

Zodra u Privileged Identity Management voor uw adres lijst hebt ingeschakeld, moet u zich registreren Privileged Identity Management om Azure AD-rollen te beheren.

1. Open **Azure AD privileged Identity Management**.

1. Klik op **Azure AD-rollen**.

    ![Privileged Identity Management voor Azure AD-rollen registreren](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Klik op **registreren**.

1. In het bericht dat wordt weer gegeven, klikt u op **Ja** om u te registreren PRIVILEGED Identity Management Azure AD-rollen te beheren.

    ![Privileged Identity Management registreren voor Azure AD-rollen bericht](./media/pim-getting-started/sign-up-pim-message.png)

    Wanneer de registratie is voltooid, worden de Azure AD-opties ingeschakeld. Mogelijk moet u de portal vernieuwen.

    Voor informatie over het detecteren en selecteren van de Azure-resources die u wilt beveiligen met Privileged Identity Management raadpleegt [u Azure-resources detecteren om te beheren in privileged Identity Management](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>Navigeer naar uw taken

Zodra Privileged Identity Management is ingesteld, kunt u uw identiteits beheer taken starten.

![Navigatie venster in Privileged Identity Management taken weer geven en opties beheren](./media/pim-getting-started/pim-quickstart-tasks.png)

| Taak + beheren | Beschrijving |
| --- | --- |
| **Mijn rollen**  | Geeft een lijst weer met in aanmerking komende en actieve rollen die aan u zijn toegewezen. Hier kunt u alle in aanmerking komende toegewezen rollen activeren. |
| **Mijn aanvragen** | Hiermee worden de in behandeling zijnde aanvragen voor het activeren van in aanmerking komende roltoewijzingen weer gegeven. |
| **Aanvragen goedkeuren** | Geeft een lijst weer van aanvragen voor het activeren van in aanmerking komende rollen door gebruikers in uw directory die u hebt ingesteld om goed te keuren. |
| **Toegang controleren** | Een lijst met actieve toegangs beoordelingen die u hebt toegewezen aan volt ooien, of u nu de toegang bekijkt voor uzelf of iemand anders. |
| **Azure AD-rollen** | Geeft een dash board en instellingen weer voor bevoegde beheerdersrol voor het beheren van toewijzingen van Azure AD-rollen. Dit dashboard is uitgeschakeld voor iedereen die geen beheerder met een bevoorrechte rol is. Deze gebruikers hebben toegang tot een speciaal dashboard met de titel Mijn weergave. Het dashboard Mijn weergave bevat alleen informatie over de gebruiker die toegang heeft tot het dashboard, niet de gehele tenant. |
| **Azure-resources** | Geeft een dash board en instellingen weer voor beheerders met verhoogde rollen voor het beheren van toewijzingen van Azure-resource rollen. Dit dashboard is uitgeschakeld voor iedereen die geen beheerder met een bevoorrechte rol is. Deze gebruikers hebben toegang tot een speciaal dashboard met de titel Mijn weergave. Het dashboard Mijn weergave bevat alleen informatie over de gebruiker die toegang heeft tot het dashboard, niet de gehele tenant. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Een PIM-tegel toevoegen aan het dash board

U kunt het openen van Privileged Identity Management eenvoudiger maken door een Privileged Identity Management tegel toe te voegen aan uw Azure Portal dash board.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Klik op **alle services** en zoek de **Azure AD privileged Identity Management** -service.

    ![Azure AD Privileged Identity Management in alle services](./media/pim-getting-started/pim-all-services-find.png)

1. Klik om de Privileged Identity Management Quick Start te openen.

1. Schakel de **Blade vastmaken aan dash board** in om de blade privileged Identity Management Snelstartgids aan het dash board vast te maken.

    ![Pictogram punaise om Privileged Identity Management Blade aan het dash board vast te maken](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    In het Azure-dash board ziet u een tegel zoals deze:

    ![Tegel Privileged Identity Management Snelstartgids op het dash board](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Azure-resources ontdekken om te beheren in Privileged Identity Management](pim-resource-roles-discover-resources.md)
