---
title: Wizard Beveiliging van Azure AD-rollen in PIM-Azure Active Directory | Microsoft Docs
description: Hierin wordt de wizard Beveiliging beschreven waarmee u permanente privileged Azure AD-roltoewijzingen kunt converteren naar kwalificeren met Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04bd0993873568ba7cce368ddd9277ed356b636c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375472"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>De wizard Beveiliging van Azure AD-rollen in Privileged Identity Management

Als u de eerste persoon bent die Privileged Identity Management (PIM) in uw Azure Active Directory-organisatie (Azure AD) wilt gebruiken, wordt u gevraagd om aan de slag te gaan. De wizard helpt u bij het begrijpen van de beveiligings Risico's van geprivilegieerde identiteiten en het gebruik van Privileged Identity Management om deze Risico's te verminderen. U hoeft geen wijzigingen aan te brengen in bestaande roltoewijzingen in de wizard, als u dat liever later doet.

## <a name="wizard-overview"></a>Wizard-overzicht

Voordat uw organisatie begint met Privileged Identity Management, zijn alle roltoewijzingen permanent: de gebruikers zijn altijd in deze rollen, zelfs als ze niet de juiste bevoegdheden hebben. In de eerste stap van de wizard ziet u een lijst met zeer privilegede rollen en het aantal gebruikers dat momenteel deel uitmaken van deze rollen. U kunt inzoomen op een bepaalde rol om meer te weten te komen over gebruikers als een of meer hiervan niet bekend zijn.

De tweede stap van de wizard biedt u de mogelijkheid om de roltoewijzingen van de beheerder te wijzigen.  

> [!WARNING]
> Het is belang rijk dat u ten minste één globale beheerder hebt en dat er meer dan één geprivilegieerde rol beheerder is met een organisatie account (geen Microsoft-account). Als er slechts één rol beheerder met beheerders rechten is, kan de organisatie Privileged Identity Management niet beheren als dat account wordt verwijderd.
> Zorg er ook voor dat de roltoewijzingen permanent blijven als een gebruiker een Microsoft-account heeft (met andere woorden, een account dat wordt gebruikt om u aan te melden bij micro soft-services zoals Skype en Outlook.com). Als u multi-factor Authentication wilt vereisen voor activering voor die rol, wordt die gebruiker vergrendeld.

## <a name="run-the-wizard"></a>De wizard uitvoeren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure AD-rollen** en selecteer vervolgens **wizard**.

    ![Azure AD-rollen: wizard pagina met de drie stappen voor het uitvoeren van de wizard](./media/pim-security-wizard/wizard-start.png)

1. Selecteer **1 geprivilegieerde rollen detecteren**.

1. Bekijk de lijst met geprivilegieerde rollen om te zien welke gebruikers permanent of in aanmerking komen.

    ![Geprivilegieerde rollen detecteren: deel venster met permanente en in aanmerking komende leden](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Selecteer **volgende** om de gebruikers of groepen te selecteren die u in aanmerking wilt nemen.

    ![Leden converteren naar een in aanmerking komende pagina met opties om leden te selecteren die u in aanmerking wilt stellen voor rollen](./media/pim-security-wizard/convert-members-eligible.png)

1. Wanneer u de gebruikers of groepen hebt geselecteerd, selecteert u **volgende**.

    ![Pagina wijzigingen bekijken met leden met permanente roltoewijzingen die worden geconverteerd](./media/pim-security-wizard/review-changes.png)

1. Selecteer **OK** om de permanente toewijzingen naar in aanmerking te converteren.

    Wanneer de conversie is voltooid, ziet u een melding.

    ![Melding waarin de status van een conversie wordt weer gegeven](./media/pim-security-wizard/notification-completion.png)

Als u andere geprivilegieerde roltoewijzingen wilt converteren naar in aanmerking komende toewijzingen, kunt u de wizard opnieuw uitvoeren. Als u de Privileged Identity Management Interface wilt gebruiken in plaats van de wizard, raadpleegt u [Azure AD-rollen toewijzen in privileged Identity Management](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Toegang verlenen aan andere beheerders om Privileged Identity Management te beheren](pim-how-to-give-access-to-pim.md)
