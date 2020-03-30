---
title: Pim - Azure Active Directory gebruiken | Microsoft Documenten
description: Meer informatie over het inschakelen en aan de slag gaan met Azure AD Privileged Identity Management (PIM) in de Azure-portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 03/13/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cbb9b4340a7cdb9be5039722a8f75e09288ec48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472857"
---
# <a name="start-using-privileged-identity-management"></a>Aan de slag met Privileged Identity Management

Met Privileged Identity Management (PIM) u toegang beheren, beheren en bewaken binnen uw Azure AD-organisatie (Azure AD) van Azure. Dit bereik omvat toegang tot Azure-bronnen, Azure AD en andere Online-services van Microsoft, zoals Office 365 of Microsoft Intune.

In dit artikel wordt beschreven hoe u Privileged Identity Management inschakelen en aan de slag gaan.

## <a name="prerequisites"></a>Vereisten

Als u Privileged Identity Management wilt gebruiken, moet u over een van de volgende licenties beschikken:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Zie [Licentievereisten voor het gebruik van Privileged Identity Management voor](subscription-requirements.md)meer informatie.

## <a name="sign-up-pim-for-azure-ad-roles"></a>PIM aanmelden voor Azure AD-rollen

Zodra u Privileged Identity Management voor uw directory hebt ingeschakeld, moet u privileged identity management aanmelden om Azure AD-rollen te beheren.

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Azure AD-rollen**.

    ![Privileged Identity Management aanmelden voor Azure AD-rollen](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Selecteer **Aanmelden**.

1. Klik in het bericht dat wordt weergegeven op **Ja** om privileged identity management aan te melden om Azure AD-rollen te beheren.

    ![Privileged Identity Management aanmelden voor het bericht van Azure AD-rollen](./media/pim-getting-started/sign-up-pim-message.png)

    Wanneer het aanmelden is voltooid, worden de Azure AD-opties ingeschakeld. Mogelijk moet u de portal vernieuwen.

    Zie [Azure-bronnen ontdekken in Privileged Identity Management](pim-resource-roles-discover-resources.md)voor informatie over het ontdekken en selecteren van de Azure-resources die u wilt beveiligen met Privileged Identity Management.

## <a name="navigate-to-your-tasks"></a>Navigeer naar uw taken

Zodra Privileged Identity Management is ingesteld, u uw identiteitsbeheertaken starten.

![Navigatievenster in Privileged Identity Management met opties voor taken en beheer](./media/pim-getting-started/pim-quickstart-tasks.png)

| Taak + Beheren | Beschrijving |
| --- | --- |
| **Mijn rollen**  | Hiermee wordt een lijst weergegeven met in aanmerking komende en actieve rollen die aan u zijn toegewezen. Hier kunt u alle in aanmerking komende toegewezen rollen activeren. |
| **Mijn verzoeken** | Hiermee geeft u uw aanvragen in behandeling om in aanmerking komende roltoewijzingen te activeren. |
| **Aanvragen goedkeuren** | Hiermee wordt een lijst weergegeven met aanvragen voor het activeren van in aanmerking komende rollen door gebruikers in uw map die u moet goedkeuren. |
| **Toegang beoordelen** | Hier worden actieve toegangsbeoordelingen weergegeven die u hebt toegewezen, of u de toegang voor uzelf of iemand anders bekijkt. |
| **Azure AD-rollen** | Hiermee worden een dashboard en instellingen weergegeven voor bevoorrechte rolbeheerders om Azure AD-roltoewijzingen te beheren. Dit dashboard is uitgeschakeld voor iedereen die geen beheerder met een bevoorrechte rol is. Deze gebruikers hebben toegang tot een speciaal dashboard met de titel Mijn weergave. Het dashboard Mijn weergave bevat alleen informatie over de gebruiker die toegang heeft tot het dashboard, niet de gehele tenant. |
| **Azure-resources** | Hiermee worden een dashboard en instellingen weergegeven voor bevoorrechte rolbeheerders om toewijzingen van Azure-bronnen te beheren. Dit dashboard is uitgeschakeld voor iedereen die geen beheerder met een bevoorrechte rol is. Deze gebruikers hebben toegang tot een speciaal dashboard met de titel Mijn weergave. Het dashboard Mijn weergave bevat alleen informatie over de gebruiker die toegang heeft tot het dashboard, niet de gehele tenant. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Een PIM-tegel toevoegen aan het dashboard

Als u het openen van Privileged Identity Management eenvoudiger wilt maken, voegt u een tegel Voor voorkeursidentiteitsbeheer toe aan uw Azure-portaldashboard.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Selecteer **Alle services** en zoek de Azure AD Privileged Identity **Management-service.**

    ![Azure AD Privileged Identity Management in Alle services](./media/pim-getting-started/pim-all-services-find.png)

1. Selecteer de Quickstart Voor voorkeursidentiteitsbeheer.

1. Schakel **Het mes vastmaken aan het dashboard** om het quickstartblad voor voorkeursidentiteitsbeheer vast te maken aan het dashboard.

    ![Pictogram Pushpin om het blade van Privileged Identity Management vast te maken aan het dashboard](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Op het Azure-dashboard ziet u een tegel als volgt:

    ![Tegel Snelstart voor voorkeursidentiteitsbeheer op het dashboard](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Azure-bronnen ontdekken die moeten worden beheerd in Privileged Identity Management](pim-resource-roles-discover-resources.md)
