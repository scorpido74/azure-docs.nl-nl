---
title: Mijn personeel gebruiken om gebruikersbeheer te delegeren (voorbeeld) - Azure AD | Microsoft Documenten
description: Gebruikersbeheer delegeren met mijn personeel en administratieve eenheden
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 3f7c12612dbe37de6b08cb05a64af460296ade93
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394213"
---
# <a name="manage-your-users-with-my-staff-preview"></a>Uw gebruikers beheren met Mijn personeel (voorbeeld)

Met Mijn personeel u delegeren aan een autoriteit, zoals een winkelmanager of een teamleider, de machtigingen om ervoor te zorgen dat hun medewerkers toegang hebben tot hun Azure AD-accounts. In plaats van te vertrouwen op een centrale helpdesk, kunnen organisaties veelvoorkomende taken delegeren, zoals het opnieuw instellen van wachtwoorden of het wijzigen van telefoonnummers aan een teammanager. Met Mijn personeel kan een gebruiker die geen toegang heeft tot zijn account in slechts een paar klikken weer toegang krijgen, zonder dat er helpdesk- of IT-medewerkers nodig zijn.

Voordat u Mijn personeel voor uw organisatie configureert, raden we u aan deze documentatie en de [gebruikersdocumentatie](../user-help/my-staff-team-manager.md) te bekijken om ervoor te zorgen dat u de functionaliteit en de impact van deze functie op uw gebruikers begrijpt. U de gebruikersdocumentatie gebruiken om uw gebruikers te trainen en voor te bereiden op de nieuwe ervaring en om een succesvolle implementatie te garanderen.

## <a name="how-my-staff-works"></a>Hoe mijn personeel werkt

Mijn personeel is gebaseerd op administratieve eenheden (AU's), die een container met resources zijn die kan worden gebruikt om de reikwijdte van de administratieve controle van een roltoewijzing te beperken. In Mijn personeel worden AU's gebruikt om een subset van gebruikers van een organisatie te definiëren, zoals een winkel of afdeling. Vervolgens kan bijvoorbeeld een teammanager worden toegewezen aan een rol waarvan het bereik een of meer AU's is. In het onderstaande voorbeeld heeft de gebruiker de rol Verificatiebeheer gekregen en de drie AU's zijn het bereik van de rol. Zie [Beheer van beheereenheden in Azure Active Directory](directory-administrative-units.md)voor meer informatie over beheereenheden.

## <a name="how-to-enable-my-staff"></a>Hoe mijn personeel in te schakelen

Zodra u AU's hebt geconfigureerd, u dit bereik toepassen op uw gebruikers die toegang hebben tot Mijn personeel. Alleen gebruikers aan wie een administratieve rol is toegewezen, hebben toegang tot Mijn personeel. Voer de volgende stappen uit om Mijn personeel in te schakelen:

1. Meld u aan bij de Azure-portal als gebruikersbeheerder.
2. Blader naar **Azure Active Directory** > **Gebruikersinstellingen** > **Gebruikersfunctievoorbeelden** > **Gebruikersfunctiepreviews Gebruikersfunctiepreview-instellingen beheren**.
3. Onder **Administrators heeft u toegang tot Mijn personeel**, u ervoor kiezen om in te schakelen voor alle gebruikers, geselecteerde gebruikers of geen gebruikerstoegang.

> [!Note]
> Alleen gebruikers aan wie een beheerdersrol is toegewezen, hebben toegang tot Mijn personeel. Als u Mijn personeel inschakelt voor een gebruiker die geen beheerdersrol heeft toegewezen, heeft deze persoon geen toegang tot Mijn personeel.

## <a name="using-my-staff"></a>Mijn personeel gebruiken

Wanneer een gebruiker naar Mijn personeel gaat, worden de namen weergegeven van de [administratieve eenheden](directory-administrative-units.md) waarover hij of zij administratieve machtigingen heeft. In de [gebruikersdocumentatie van Mijn Personeel](../user-help/my-staff-team-manager.md)gebruiken we de term "locatie" om te verwijzen naar administratieve eenheden. Als de machtigingen van een beheerder geen AU-bereik hebben, zijn de machtigingen van toepassing in de hele organisatie. Nadat Mijn personeel is ingeschakeld, kunnen de gebruikers die zijn ingeschakeld en [https://mystaff.microsoft.com](https://mystaff.microsoft.com)een administratieve rol hebben gekregen, deze openen via . Ze kunnen een AU selecteren om de gebruikers in die AU te bekijken en een gebruiker selecteren om zijn of haar profiel te openen.

## <a name="licenses"></a>Licenties

Elke gebruiker die is ingeschakeld in Mijn personeel moet een licentie hebben, zelfs als hij de mijn personeel-portal niet gebruikt. Elke ingeschakelde gebruiker moet een van de volgende Azure AD- of Microsoft 365-licenties hebben:

- Azure AD Premium P1 of P2
- Microsoft 365 F1 of F3

## <a name="reset-a-users-password"></a>Het wachtwoord van een gebruiker opnieuw instellen

De volgende rollen hebben toestemming om het wachtwoord van een gebruiker opnieuw in te stellen:

- [Verificatiebeheerder](directory-assign-admin-roles.md#authentication-administrator)
- [Bevoegde verificatiebeheerder](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Globale beheerder](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [Helpdeskbeheerder](directory-assign-admin-roles.md#helpdesk-administrator)
- [Gebruikersbeheerder](directory-assign-admin-roles.md#user-administrator)
- [Wachtwoordbeheerder](directory-assign-admin-roles.md#password-administrator)

Vanuit **Mijn personeel,** open het profiel van een gebruiker. Selecteer **Wachtwoord opnieuw instellen**.

- Als de gebruiker alleen in de cloud is, ziet u een tijdelijk wachtwoord dat u aan de gebruiker geven.
- Als de gebruiker wordt gesynchroniseerd vanuit on-premises Active Directory, u een wachtwoord invoeren dat voldoet aan uw on-premises AD-beleid. U dat wachtwoord vervolgens aan de gebruiker geven.

    ![Voortgangsindicator voor het opnieuw instellen van wachtwoorden en melding van succes](media/my-staff-configure/reset-password.png)

De gebruiker moet zijn wachtwoord wijzigen wanneer hij zich de volgende keer aanmeldt.

## <a name="manage-a-phone-number"></a>Een telefoonnummer beheren

Vanuit **Mijn personeel,** open het profiel van een gebruiker.

- De sectie **Telefoonnummer toevoegen** selecteren om een telefoonnummer voor de gebruiker toe te voegen
- Selecteer **Telefoonnummer bewerken** om het telefoonnummer te wijzigen
- Selecteer **Telefoonnummer verwijderen** om het telefoonnummer voor de gebruiker te verwijderen

Afhankelijk van uw instellingen kan de gebruiker vervolgens het telefoonnummer gebruiken dat u hebt ingesteld om in te loggen met sms, multi-factor authenticatie uit te voeren en zelfservicewachtwoord opnieuw instellen uit te voeren.

Als u het telefoonnummer van een gebruiker wilt beheren, moet u een van de volgende rollen toegewezen krijgen:

- [Verificatiebeheerder](directory-assign-admin-roles.md#authentication-administrator)
- [Bevoegde verificatiebeheerder](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Globale beheerder](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>Search

U zoeken naar AU's en gebruikers in uw organisatie met behulp van de zoekbalk in Mijn personeel. U zoeken in alle AU's en gebruikers in uw organisatie, maar u alleen wijzigingen aanbrengen in gebruikers die deel uitmaken van een AU waarover u beheerdersmachtigingen hebt gekregen.

U ook zoeken naar een gebruiker binnen een AU. Gebruik hiervoor de zoekbalk boven aan de gebruikerslijst.

## <a name="audit-logs"></a>Auditlogboeken

U controlelogboeken weergeven voor acties die zijn uitgevoerd in Mijn personeel in de Azure Active Directory-portal. Als een controlelogboek is gegenereerd door een actie in Mijn personeel, ziet u dit onder AANVULLENDE DETAILS in de controlegebeurtenis.

## <a name="next-steps"></a>Volgende stappen

[Documentatie van de gebruikersdocumentatie](../user-help/my-staff-team-manager.md)
[van](directory-administrative-units.md) Mijn Personeel
