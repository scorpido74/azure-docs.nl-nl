---
title: Azure AD-rollen detectie en inzichten (preview) in Privileged Identity Management voormalige beveiligings wizard-Azure Active Directory
description: Met detectie en inzichten (voorheen beveiligings wizard) kunt u permanente toewijzingen van Azure AD-rollen omzetten in just-in-time-toewijzingen met Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 08/07/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef1de7e98a9cb57f83b87589ceddedc3cdd80927
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88005911"
---
# <a name="discovery-and-insights-preview-for-azure-ad-roles-formerly-security-wizard"></a>Detectie en inzichten (preview) voor Azure AD-rollen (voorheen beveiligings wizard)

Als u in uw Azure Active Directory (Azure AD)-organisatie met Privileged Identity Management (PIM) begint, kunt u de pagina **detectie en inzichten (preview)** gebruiken om aan de slag te gaan. Met deze functie kunt u zien wie is toegewezen aan bevoorrechte rollen in uw organisatie en hoe u PIM kunt gebruiken om snel permanente roltoewijzingen te wijzigen in just-in-time-toewijzingen. U kunt uw permanente geprivilegieerde roltoewijzingen weer geven of wijzigen in **detectie en inzichten (preview)**. Het is een analyse programma en een actie programma.

## <a name="discovery-and-insights-preview"></a>Detectie en inzichten (preview-versie)

Voordat uw organisatie begint met Privileged Identity Management, zijn alle roltoewijzingen permanent. Gebruikers zijn altijd in hun toegewezen rollen, zelfs wanneer ze hun bevoegdheden niet nodig hebben. Detectie en inzichten (preview), waarmee de voormalige beveiligings wizard wordt vervangen, toont u een lijst met geprivilegieerde rollen en het aantal gebruikers dat momenteel deel uitmaakt van deze rollen. U kunt de toewijzingen voor een rol weer geven om meer te weten te komen over de toegewezen gebruikers als een of meer hiervan niet bekend zijn.

: heavy_check_mark: **micro soft raadt** u aan om 2-afbreek glazen te houden die permanent zijn toegewezen aan de rol van globale beheerder waarvoor multi-factor Authentication niet is vereist bij het aanmelden. U kunt deze gebruiken in een wille keurig uitzonderings scenario of als niemand in staat is om te verhogen naar de rol van globale beheerder.

Zorg er ook voor dat de roltoewijzingen permanent blijven als een gebruiker een Microsoft-account heeft (met andere woorden, een account dat wordt gebruikt om u aan te melden bij micro soft-services zoals Skype of Outlook.com). Als u multi-factor Authentication moet worden geactiveerd in een dergelijke rol, wordt de gebruiker vergrendeld.

## <a name="open-discovery-and-insights-preview"></a>Detectie en inzichten openen (preview-versie)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure AD-rollen** en selecteer vervolgens **detectie en inzichten (preview)**. Als u de pagina opent, wordt het detectie proces gestart om relevante roltoewijzingen te vinden.

    ![Azure AD-rollen-detectie-en inzichten pagina met de 3 opties](./media/pim-security-wizard/new-preview-link.png)

1. Selecteer **globale beheerders verminderen**.

    ![Het deel venster globale beheerders verlagen: alle leden worden weer gegeven](./media/pim-security-wizard/new-preview-page.png)

1. Bekijk de lijst met algemene toewijzingen voor beheerdersrol.

    ![Het deel venster globale beheerders verlagen: alle leden worden weer gegeven](./media/pim-security-wizard/new-global-administrator-list.png)

1. Selecteer **volgende** om de gebruikers of groepen te selecteren die u in aanmerking wilt nemen, en selecteer vervolgens **in aanmerking komende** of **Verwijder toewijzing**maken.

    ![Leden converteren naar een in aanmerking komende pagina met opties om leden te selecteren die u in aanmerking wilt stellen voor rollen](./media/pim-security-wizard/new-global-administrator-buttons.png)

1. U kunt ook alle globale beheerders vragen hun eigen toegang te controleren.

    ![Pagina globale beheerders met toegangs beoordelingen sectie](./media/pim-security-wizard/new-global-administrator-access-review.png)

1. Nadat u een van deze wijzigingen hebt geselecteerd, ziet u een Azure-melding.

1. U kunt vervolgens **permanente toegang elimineren** of **service-principals controleren** om de bovenstaande stappen te herhalen op andere geprivilegieerde rollen en op Service Principal Role-toewijzingen. Voor Service Principal-roltoewijzingen kunt u alleen roltoewijzingen verwijderen.

    ![Aanvullende inzichten opties voor het elimineren van permanente toegang en het controleren van service-principals ](./media/pim-security-wizard/new-preview-page-service-principals.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Toegang verlenen aan andere beheerders om Privileged Identity Management te beheren](pim-how-to-give-access-to-pim.md)
