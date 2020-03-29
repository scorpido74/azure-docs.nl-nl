---
title: Een groep toevoegen of verwijderen uit een andere groep - Azure AD
description: Instructies voor het toevoegen of verwijderen van een groep uit een andere groep met Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 830bf7134b3a8b0425c53673a1347dd77897a5bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75423049"
---
# <a name="add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>Een groep uit een andere groep toevoegen of verwijderen met Azure Active Directory
Met dit artikel u een groep uit een andere groep toevoegen en verwijderen met Azure Active Directory.

>[!Note]
>Als u de bovenliggende groep probeert te verwijderen, raadpleegt u [Een groep en haar leden bijwerken of verwijderen.](active-directory-groups-delete-group.md)

## <a name="add-a-group-to-another-group"></a>Een groep toevoegen aan een andere groep
U een bestaande beveiligingsgroep toevoegen aan een andere bestaande beveiligingsgroep (ook wel geneste groepen genoemd), een ledengroep (subgroep) en een bovenliggende groep maken. De ledengroep neemt de kenmerken en eigenschappen van de bovenliggende groep over, waardoor u configuratietijd bespaart.

>[!Important]
>We ondersteunen momenteel geen:<ul><li>Groepen toevoegen aan een groep die is gesynchroniseerd met on-premises Active Directory.</li><li>Beveiligingsgroepen toevoegen aan Office 365-groepen.</li><li>Office 365-groepen toevoegen aan beveiligingsgroepen of andere Office 365-groepen.</li><li>Apps toewijzen aan geneste groepen.</li><li>Licenties toepassen op geneste groepen.</li><li>Distributiegroepen toevoegen in nestscenario's.</li></ul>

### <a name="to-add-a-group-as-a-member-of-another-group"></a>Een groep toevoegen als lid van een andere groep

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory**en selecteer **Groepen**.

3. Zoek op de pagina **Groepen - Alle groepen** naar de groep die lid wil worden van een andere groep. Voor deze oefening gebruiken we de **MDM-beleidsgroep West.**

    >[!Note]
    >U uw groep als lid toevoegen aan slechts één groep tegelijk. Bovendien **filtert** het vak Groep selecteren het display op basis van het afstemmen van uw vermelding op een onderdeel van een gebruiker of apparaatnaam. Jokertekens worden echter niet ondersteund.

    ![Groepen - Pagina Alle groepen met MDM-beleid - Westgroep geselecteerd](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. Selecteer op de pagina **MDM-beleid - West - Groepslidmaatschappen** **groep groepslidmaatschappen,** selecteer **Toevoegen,** zoek de groep waarvan u wilt dat uw groep lid wordt en kies vervolgens **Selecteren**. Voor deze oefening gebruiken we het **MDM-beleid - Alle** org-groep.

    De **MDM-beleid - West-groep** is nu lid van het **MDM-beleid - Alle organisatiegroep,** waarbij alle eigenschappen en configuratie van het MDM-beleid worden overgenomen - Alle organisatiegroep.

    ![Een groepslidmaatschap maken door groep toe te voegen aan een andere groep](media/active-directory-groups-membership-azure-portal/group-add-group-membership.png)

5. Bekijk de pagina **MDM-beleid - West - Groepslidmaatschappen** om de groeps- en ledenrelatie te bekijken.

6. Voor een meer gedetailleerde weergave van de groeps- en ledenrelatie selecteert u de groepsnaam **(MDM-beleid - Alle org)** en bekijkt u de details van de **MDM-pagina - Westpagina.**

## <a name="remove-a-group-from-another-group"></a>Een groep uit een andere groep verwijderen
U een bestaande beveiligingsgroep uit een andere beveiligingsgroep verwijderen. Als u de groep verwijdert, worden echter ook overgenomen kenmerken en eigenschappen voor de leden verwijderd.

### <a name="to-remove-a-member-group-from-another-group"></a>Een ledengroep uit een andere groep verwijderen
1. Zoek op de pagina **Groepen - Alle groepen** naar de groep die moet worden verwijderd als lid van een andere groep. Voor deze oefening, zijn we weer met behulp van de **MDM beleid - West** groep.

2. Selecteer op de **overzichtspagina MDM - West** **groepslidmaatschappen**.

3. Selecteer het **MDM-beleid - Alle organisatiegroep** in het **MDM-beleid - West - Groepslidmaatschappen** pagina, en selecteer **Vervolgens Verwijderen** uit het **MDM-beleid - West** pagina details.

    ![Groepslidmaatschapspagina met zowel de gegevens van het lid als de groep](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)

## <a name="additional-information"></a>Aanvullende informatie
Deze artikelen bevatten aanvullende informatie over Azure Active Directory.

- [Groepen en leden weergeven](active-directory-groups-view-azure-portal.md)

- [Een basisgroep maken en leden toevoegen](active-directory-groups-create-azure-portal.md)

- [Leden uit een groep toevoegen of verwijderen](active-directory-groups-members-azure-portal.md)

- [Instellingen van uw groep bewerken](active-directory-groups-settings-azure-portal.md)

- [Toegang tot SaaS-toepassingen beheren met behulp van een groep](../users-groups-roles/groups-saasapps.md)

- [Scenario's, beperkingen en bekende problemen met groepen om licenties te beheren in Azure Active Directory](../users-groups-roles/licensing-group-advanced.md#limitations-and-known-issues)
