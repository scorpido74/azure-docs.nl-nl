---
title: Bronrollen wijzigen voor een toegangspakket in Azure AD-rechtenbeheer - Azure Active Directory
description: Meer informatie over het wijzigen van de resourcerollen voor een bestaand toegangspakket in Azure Active Directory-rechtenbeheer.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 102bbfbd1c02c93830f5c7fce89fe95d7fde54c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261890"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Resourcerollen wijzigen voor een toegangspakket in Azure AD-rechtenbeheer

Als toegangspakketbeheerder u de bronnen in een toegangspakket op elk gewenst moment wijzigen zonder dat u zich zorgen hoeft te maken over het inrichten van de toegang van de gebruiker tot de nieuwe bronnen of het verwijderen van de toegang uit de vorige bronnen. In dit artikel wordt beschreven hoe u de resourcerollen voor een bestaand toegangspakket wijzigen.

In deze video vindt u een overzicht van het wijzigen van een toegangspakket.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>Catalogus controleren op bronnen

Als u resources aan een toegangspakket wilt toevoegen, moet u controleren of de bronnen die u nodig hebt beschikbaar zijn in de catalogus. Als u een toegangspakketbeheerder bent, u geen bronnen toevoegen aan een catalogus, zelfs niet als u deze eigenaar bent. U bent beperkt tot het gebruik van de bronnen die beschikbaar zijn in de catalogus.

**Vereiste rol:** Globale beheerder, gebruikersbeheerder, cataloguseigenaar of Access-pakketbeheer

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Catalogus** en open de catalogus.

1. Klik in het linkermenu op **Bronnen** om de lijst met bronnen in deze catalogus weer te geven.

    ![Lijst met bronnen in een catalogus](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. Als u een toegangspakketbeheerder bent en u resources aan de catalogus moet toevoegen, u de eigenaar van de catalogus vragen deze toe te voegen.

## <a name="add-resource-roles"></a>Resourcerollen toevoegen

Een resourcerol is een verzameling machtigingen die zijn gekoppeld aan een resource. De manier waarop u resources beschikbaar stelt voor gebruikers om te vragen, is door resourcerollen toe te voegen aan uw toegangspakket. U resourcerollen toevoegen voor groepen, teams, toepassingen en SharePoint-sites.

**Vereiste rol:** Globale beheerder, gebruikersbeheerder, cataloguseigenaar of Access-pakketbeheer

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Access-pakketten** en open het toegangspakket.

1. Klik in het linkermenu op **Resourcerollen**.

1. Klik **op Resourcerollen toevoegen** om de pagina Resourcerollen toevoegen te openen om toegang te krijgen tot pakketpagina.

    ![Toegangspakket - Resourcerollen toevoegen](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. Afhankelijk van of u een groep, team, toepassing of SharePoint-site wilt toevoegen, voert u de stappen uit in een van de volgende resourcerolsecties.

## <a name="add-a-group-or-team-resource-role"></a>Een groeps- of teamresourcerol toevoegen

U het beheer van de rechten automatisch gebruikers toevoegen aan een groep of een team in Microsoft Teams wanneer ze een toegangspakket toegewezen krijgen. 

- Wanneer een groep of team deel uitmaakt van een toegangspakket en een gebruiker aan dat toegangspakket is toegewezen, wordt de gebruiker toegevoegd aan die groep of team, zo niet al aanwezig.
- Wanneer de toewijzing van het toegangspakket van een gebruiker verloopt, worden ze uit de groep of het team verwijderd, tenzij ze momenteel een toewijzing hebben voor een ander toegangspakket dat dezelfde groep of team bevat.

U een [Azure AD-beveiligingsgroep of Office 365-groep selecteren.](../fundamentals/active-directory-groups-create-azure-portal.md) Beheerders kunnen elke groep aan een catalogus toevoegen. cataloguseigenaren kunnen elke groep aan de catalogus toevoegen als ze eigenaar zijn van de groep. Houd rekening met de volgende Azure AD-beperkingen bij het selecteren van een groep:

- Wanneer een gebruiker, inclusief een gast, als lid aan een groep of team wordt toegevoegd, kan hij of zij alle andere leden van die groep of team zien.
- Azure AD kan het lidmaatschap van een groep die is gesynchroniseerd vanuit Windows Server Active Directory met Azure AD Connect of die is gemaakt in Exchange Online als een distributiegroep, niet wijzigen.  
- Het lidmaatschap van dynamische groepen kan niet worden bijgewerkt door het toevoegen of verwijderen van een lid, zodat dynamische groepslidmaatschappen niet geschikt zijn voor gebruik met het beheer van rechten.

Zie [Groepen vergelijken](https://docs.microsoft.com/office365/admin/create-groups/compare-groups) en [Office 365-groepen en Microsoft Teams](https://docs.microsoft.com/microsoftteams/office-365-groups)voor meer informatie.

1. Klik op de pagina **Resourcerollen toevoegen om toegang te krijgen tot pakketpagina** op **Groepen en Teams** om het deelvenster Groepen selecteren te openen.

1. Selecteer de groepen en teams die u wilt opnemen in het toegangspakket.

    ![Toegangspakket - Resourcerollen toevoegen - Groepen selecteren](./media/entitlement-management-access-package-resources/group-select.png)

1. Klik **op Selecteren**.

    Zodra u de groep of het team hebt geselecteerd, wordt in de kolom **Subtype** een van de volgende subtypen weergegeven:

    |  |  |
    | --- | --- |
    | Beveiliging | Wordt gebruikt voor het verlenen van toegang tot bronnen. |
    | Distributie | Wordt gebruikt voor het verzenden van meldingen naar een groep mensen. |
    | O365 | Office 365-groep die niet is ingeschakeld voor Teams. Wordt gebruikt voor samenwerking tussen gebruikers, zowel binnen als buiten uw bedrijf. |
    | Team | Office 365-groep die is ingeschakeld voor Teams. Wordt gebruikt voor samenwerking tussen gebruikers, zowel binnen als buiten uw bedrijf. |

1. Selecteer **Eigenaar** of **Lid**in de lijst **Met rollen** .

    U selecteert meestal de rol lid. Als u de rol Eigenaar selecteert, kunnen gebruikers andere leden of eigenaren toevoegen of verwijderen.

    ![Toegangspakket - Resourcerol toevoegen voor een groep of team](./media/entitlement-management-access-package-resources/group-role.png)

1. Klik op**toevoegen**.

    Gebruikers met bestaande toewijzingen aan het toegangspakket worden automatisch lid van deze groep of team wanneer deze wordt toegevoegd.

## <a name="add-an-application-resource-role"></a>Een toepassingsbronrol toevoegen

U Azure AD gebruikers automatisch toegang laten geven tot een Azure AD-bedrijfstoepassing, inclusief zowel SaaS-toepassingen als de toepassingen van uw organisatie die worden gefedereerd naar Azure AD, wanneer een gebruiker een toegangspakket toegewezen krijgt. Voor toepassingen die met Azure AD worden geïntegreerd via federatieve enkele aanmelding, geeft Azure AD federatietokens uit voor gebruikers die aan de toepassing zijn toegewezen.

Toepassingen kunnen meerdere rollen hebben. Wanneer u een toepassing toevoegt aan een toegangspakket, moet u de juiste rol voor die gebruikers opgeven als die toepassing meer dan één rol heeft. Als u toepassingen ontwikkelt, u meer lezen over hoe deze rollen aan uw toepassingen worden toegevoegd in [Hoe de rolclaim te configureren die is uitgegeven in het SAML-token voor bedrijfstoepassingen.](../develop/active-directory-enterprise-app-role-management.md)

Zodra een toepassingsrol deel uitmaakt van een toegangspakket:

- Wanneer een gebruiker dat toegangspakket toegewezen krijgt, wordt de gebruiker aan die toepassingsrol toegevoegd, zo niet al aanwezig.
- Wanneer de toewijzing van het toegangspakket van een gebruiker verloopt, wordt de toegang uit de toepassing verwijderd, tenzij deze een toewijzing heeft voor een ander toegangspakket dat die toepassingsrol bevat.

Hier volgen enkele overwegingen bij het selecteren van een toepassing:

- Toepassingen kunnen ook groepen toegewezen aan hun rollen ook.  U ervoor kiezen om een groep toe te voegen in plaats van een toepassingsrol in een toegangspakket, maar dan is de toepassing niet zichtbaar voor de gebruiker als onderdeel van het toegangspakket in de My Access-portal.

1. Klik op de pagina **Resourcerollen toevoegen om toegang te krijgen tot pakketpagina** op **Toepassingen** om het deelvenster Toepassingen selecteren te openen.

1. Selecteer de toepassingen die u wilt opnemen in het toegangspakket.

    ![Toegangspakket - Resourcerollen toevoegen - Toepassingen selecteren](./media/entitlement-management-access-package-resources/application-select.png)

1. Klik **op Selecteren**.

1. Selecteer **in** de lijst Rol een toepassingsrol.

    ![Toegangspakket - Resourcerol toevoegen voor een toepassing](./media/entitlement-management-access-package-resources/application-role.png)

1. Klik op**toevoegen**.

    Alle gebruikers met bestaande toewijzingen aan het toegangspakket krijgen automatisch toegang tot deze toepassing wanneer deze wordt toegevoegd.

## <a name="add-a-sharepoint-site-resource-role"></a>Een SharePoint-sitebronrol toevoegen

Azure AD kan gebruikers automatisch toegang geven tot een SharePoint Online-site of SharePoint Online-siteverzameling wanneer ze een toegangspakket toegewezen krijgen.

1. Klik op de pagina **Resourcerollen toevoegen om toegang te krijgen tot pakketpagina** op **SharePoint-sites** om het deelvenster SharePoint Online-sites selecteren te openen.

1. Selecteer de SharePoint Online-sites die u in het toegangspakket wilt opnemen.

    ![Toegangspakket - Resourcerollen toevoegen - SharePoint Online-sites selecteren](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. Klik **op Selecteren**.

1. Selecteer **in** de lijst Rol een SharePoint Online-siterol.

    ![Toegangspakket - Bronrol toevoegen voor een SharePoint Online-site](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. Klik op**toevoegen**.

    Alle gebruikers met bestaande toewijzingen aan het toegangspakket krijgen automatisch toegang tot deze SharePoint Online-site wanneer deze wordt toegevoegd.

## <a name="remove-resource-roles"></a>Resourcerollen verwijderen

**Vereiste rol:** Globale beheerder, gebruikersbeheerder, cataloguseigenaar of Access-pakketbeheer

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Access-pakketten** en open het toegangspakket.

1. Klik in het linkermenu op **Resourcerollen**.

1. Zoek in de lijst met resourcerollen de resourcerol die u wilt verwijderen.

1. Klik op de ellips (**... )** en klik vervolgens op **Bronrol verwijderen**.

    Gebruikers met bestaande toewijzingen tot het toegangspakket hebben automatisch hun toegang tot deze bronrol ingetrokken wanneer deze wordt verwijderd.

## <a name="when-changes-are-applied"></a>Wanneer wijzigingen worden toegepast

In het beheer van rechten verwerkt Azure AD bulkwijzigingen voor toewijzing en resources in uw toegangspakketten meerdere keren per dag. Als u dus een toewijzing maakt of de resourcerollen van uw toegangspakket wijzigt, kan het tot 24 uur duren voordat die wijziging is aangebracht in Azure AD, plus de hoeveelheid tijd die nodig is om deze wijzigingen te verspreiden naar andere Microsoft Online Services of verbonden SaaS Toepassingen. Als uw wijziging slechts een paar objecten beïnvloedt, duurt het waarschijnlijk slechts enkele minuten voordat de wijziging is toegepast in Azure AD, waarna andere Azure AD-componenten die wijziging detecteren en de SaaS-toepassingen bijwerken. Als de wijziging van invloed is op duizenden objecten, duurt de wijziging langer. Als u bijvoorbeeld een toegangspakket hebt met 2 toepassingen en 100 gebruikerstoewijzingen en u besluit een SharePoint-siterol toe te voegen aan het toegangspakket, kan dit vertraging oplopen totdat alle gebruikers deel uitmaken van die SharePoint-siterol. U de voortgang controleren via het Azure AD-controlelogboek, het Azure AD-inrichtingslogboek en de sharepoint-sitecontrolelogboeken.

Wanneer u een lid van een team verwijdert, wordt deze persoon ook verwijderd uit de Office 365-groep. Het verwijderen uit de chatfunctionaliteit van het team kan worden uitgesteld. Zie [Groepslidmaatschap](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership)voor meer informatie .

## <a name="next-steps"></a>Volgende stappen

- [Een basisgroep maken en leden toevoegen met Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md)
- [How to: De rolclaim configureren die is uitgegeven in het SAML-token voor bedrijfstoepassingen](../develop/active-directory-enterprise-app-role-management.md)
- [Inleiding tot SharePoint Online](/sharepoint/introduction)