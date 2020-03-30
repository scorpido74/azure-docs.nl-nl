---
title: Azure AD-wizard rollen beveiliging in PIM - Azure Active Directory | Microsoft Documenten
description: Beschrijft de beveiligingswizard die u gebruiken om permanente bevoorrechte AZURE AD-roltoewijzingen om te zetten in aanmerking komen met Azure AD Privileged Identity Management (PIM).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266570"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Wizard Azure AD-rollen beveiliging in Privileged Identity Management

Als u de eerste persoon bent die Privileged Identity Management (PIM) gebruikt in uw Azure Active Directory (Azure AD)-organisatie, krijgt u een wizard te zien om aan de slag te gaan. De wizard helpt u inzicht te krijgen in de beveiligingsrisico's van bevoorrechte identiteiten en hoe u Privileged Identity Management gebruiken om deze risico's te beperken. U hoeft geen wijzigingen aan te brengen in bestaande roltoewijzingen in de wizard, als u dit later liever doet.

## <a name="wizard-overview"></a>Overzicht van wizard

Voordat uw organisatie Privileged Identity Management gaat gebruiken, zijn alle roltoewijzingen permanent: de gebruikers bevinden zich altijd in deze rollen, zelfs als ze momenteel hun bevoegdheden niet nodig hebben. In de eerste stap van de wizard ziet u een lijst met functies met hoge bevoegdheden en hoeveel gebruikers zich momenteel in die rollen bevinden. U inzoomen op een bepaalde rol om meer te weten te komen over gebruikers als een of meer van hen niet bekend zijn.

De tweede stap van de wizard biedt u de mogelijkheid om de roltoewijzingen van de beheerder te wijzigen.  

> [!WARNING]
> Het is belangrijk dat u ten minste één globale beheerder hebt en meer dan één bevoorrechte rolbeheerder met een organisatieaccount (geen Microsoft-account). Als er slechts één bevoegde rolbeheerder is, kan de organisatie privileged identity management niet beheren als dat account wordt verwijderd.
> Houd ook roltoewijzingen permanent als een gebruiker een Microsoft-account heeft (met andere woorden, een account dat hij of zij gebruikt om zich aan te melden bij Microsoft-services zoals Skype en Outlook.com). Als u van plan bent om multi-factor authenticatie voor activering voor die rol te vereisen, wordt die gebruiker buitengesloten.

## <a name="run-the-wizard"></a>De wizard uitvoeren

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Azure AD-rollen** en selecteer **Wizard**.

    ![Azure AD-rollen - Wizardpagina met de 3 stappen om de wizard uit te voeren](./media/pim-security-wizard/wizard-start.png)

1. Selecteer **1 Bevoorrechte rollen ontdekken**.

1. Bekijk de lijst met bevoorrechte rollen om te zien welke gebruikers permanent of in aanmerking komen.

    ![Bevoorrechte rollen ontdekken - Rolvenster met permanente en in aanmerking komende leden](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Selecteer **Volgende** om de gebruikers of groepen te selecteren die u in aanmerking wilt laten komen.

    ![Leden converteren naar in aanmerking komende pagina met opties om leden te selecteren die u wilt maken in aanmerking te komen voor rollen](./media/pim-security-wizard/convert-members-eligible.png)

1. Nadat u de gebruikers of groepen hebt geselecteerd, selecteert u **Volgende**.

    ![De pagina Wijzigingen bekijken met leden met permanente roltoewijzingen die worden geconverteerd](./media/pim-security-wizard/review-changes.png)

1. Selecteer **OK** om de permanente toewijzingen om te zetten naar in aanmerking komen.

    Wanneer de conversie is voltooid, ziet u een melding.

    ![Melding met de status van een conversie](./media/pim-security-wizard/notification-completion.png)

Als u andere bevoorrechte roltoewijzingen wilt converteren naar in aanmerking komende, u de wizard opnieuw uitvoeren. Zie [Azure AD-rollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)als u de interface voor beheer van geprivilegieerd identiteitsbeheer wilt gebruiken.

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Toegang verlenen aan andere beheerders om privileged identity management te beheren](pim-how-to-give-access-to-pim.md)
