---
title: Wizard Beveiliging van Azure AD-rollen in PIM-Azure Active Directory | Microsoft Docs
description: Hierin wordt de wizard Beveiliging beschreven waarmee u permanente privileged Azure AD-roltoewijzingen kunt converteren naar kwalificeren met Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d6d94df29ba16ecee06d70f5edac15a96a4299d
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804011"
---
# <a name="azure-ad-roles-security-wizard-in-pim"></a>Wizard Beveiliging van Azure AD-rollen in PIM

Als u de eerste persoon bent die Azure Active Directory (Azure AD) Privileged Identity Management (PIM) voor uw organisatie wilt uitvoeren, wordt er een wizard weer gegeven. De wizard helpt u bij het begrijpen van de beveiligings Risico's van bevoorrechte identiteiten en het gebruik van PIM om deze Risico's te verminderen. U hoeft geen wijzigingen aan te brengen in bestaande roltoewijzingen in de wizard, als u dat liever later doet.

## <a name="wizard-overview"></a>Wizard-overzicht

Voordat uw organisatie begint met het gebruik van PIM, zijn alle roltoewijzingen permanent: de gebruikers zijn altijd in deze rollen, zelfs als ze hun bevoegdheden niet op dat moment nodig hebben. In de eerste stap van de wizard ziet u een lijst met zeer privilegede rollen en het aantal gebruikers dat momenteel deel uitmaken van deze rollen. U kunt inzoomen op een bepaalde rol om meer te weten te komen over gebruikers als een of meer hiervan niet bekend zijn.

De tweede stap van de wizard biedt u de mogelijkheid om de roltoewijzingen van de beheerder te wijzigen.  

> [!WARNING]
> Het is belang rijk dat u ten minste één globale beheerder hebt en dat er meer dan één geprivilegieerde rol beheerder is met een organisatie account (geen Microsoft-account). Als er slechts één rol beheerder met beheerders rechten is, kan de organisatie geen PIM beheren als dat account wordt verwijderd.
> Zorg er ook voor dat de roltoewijzingen permanent blijven als een gebruiker een Microsoft-account heeft (een account dat wordt gebruikt om u aan te melden bij micro soft-services zoals Skype en Outlook.com). Als u MFA wilt vereisen voor activering voor die rol, wordt die gebruiker vergrendeld.

## <a name="run-the-wizard"></a>De wizard uitvoeren

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Open **Azure AD privileged Identity Management**.

1. Klik op **Azure AD-rollen** en klik vervolgens op **wizard**.

    ![Azure AD-rollen: wizard pagina met de drie stappen voor het uitvoeren van de wizard](./media/pim-security-wizard/wizard-start.png)

1. Klik op **1 geprivilegieerde rollen detecteren**.

1. Bekijk de lijst met geprivilegieerde rollen om te zien welke gebruikers permanent of in aanmerking komen.

    ![Geprivilegieerde rollen detecteren: deel venster met permanente en in aanmerking komende leden](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Klik op **volgende** om de leden te selecteren die u in aanmerking wilt stellen.

    ![Leden converteren naar een in aanmerking komende pagina met opties om leden te selecteren die u in aanmerking wilt stellen voor rollen](./media/pim-security-wizard/convert-members-eligible.png)

1. Zodra u de leden hebt geselecteerd, klikt u op **volgende**.

    ![Pagina wijzigingen bekijken met leden met permanente roltoewijzingen die worden geconverteerd](./media/pim-security-wizard/review-changes.png)

1. Klik op **OK** om de permanente toewijzingen naar in aanmerking te converteren.

    Wanneer de conversie is voltooid, ziet u een melding.

    ![Melding waarin de status van een conversie wordt weer gegeven](./media/pim-security-wizard/notification-completion.png)

Als u andere geprivilegieerde roltoewijzingen wilt converteren naar in aanmerking komende toewijzingen, kunt u de wizard opnieuw uitvoeren. Zie [Azure AD-rollen toewijzen in PIM](pim-how-to-add-role-to-user.md)als u de PIM-interface wilt gebruiken in plaats van de wizard.

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rollen toewijzen in PIM](pim-how-to-add-role-to-user.md)
- [Toegang verlenen aan andere beheerders om PIM te beheren](pim-how-to-give-access-to-pim.md)
