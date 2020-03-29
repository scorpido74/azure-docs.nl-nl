---
title: Gebruikersprofielgegevens toevoegen of bijwerken - Azure AD
description: Instructies voor het toevoegen van informatie aan het profiel van een gebruiker in Azure Active Directory, inclusief een afbeelding en taakgegevens.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ba51b0ad7f314058f34092dff4e26411020086a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422883"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>De profielgegevens van een gebruiker toevoegen of bijwerken met behulp van Azure Active Directory
Voeg gebruikersprofielgegevens toe, waaronder een profielfoto, taakspecifieke informatie en enkele instellingen met Azure AD (Azure AD). Zie [Gebruikers toevoegen of verwijderen in Azure Active Directory](add-users-azure-active-directory.md)voor meer informatie over het toevoegen van nieuwe gebruikers.

## <a name="add-or-change-profile-information"></a>Profielgegevens toevoegen of wijzigen
Zoals u zult zien, is er meer informatie beschikbaar in het profiel van een gebruiker dan wat u toevoegen tijdens het maken van de gebruiker. Al deze aanvullende informatie is optioneel en kan naar behoefte worden toegevoegd door uw organisatie.

## <a name="to-add-or-change-profile-information"></a>Profielgegevens toevoegen of wijzigen
1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) als gebruikersbeheerder voor de organisatie.

2. Selecteer **Azure Active Directory,** selecteer **Gebruikers**en selecteer vervolgens een gebruiker. Bijvoorbeeld, _Alain Charon_.

    De **pagina Alain Charon - Profiel** wordt weergegeven.

    ![Profielpagina van de gebruiker, inclusief bewerkbare informatie](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Selecteer **Bewerken** om de informatie in elk van de beschikbare secties optioneel toe te voegen of bij te werken.

    ![Profielpagina van de gebruiker, met de bewerkbare gebieden](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Profielfoto.** Selecteer een miniatuurafbeelding voor het account van de gebruiker. Deze afbeelding wordt weergegeven in Azure Active Directory en op de persoonlijke pagina's van de gebruiker, zoals de pagina myapps.microsoft.com.

    - **Identiteit.** Voeg een extra identiteitswaarde voor de gebruiker toe of werk deze bij, zoals een gehuwde achternaam. U deze naam onafhankelijk van de waarden voornaam en achternaam instellen. U het bijvoorbeeld gebruiken om initialen, een bedrijfsnaam of de volgorde van de weergegeven namen op te nemen. In een ander voorbeeld, voor twee gebruikers met de namen 'Chris Green' u de identity string gebruiken om hun namen in te stellen op 'Chris B. Green' 'Chris R. Green (Contoso).'

    - **Werkinfo.** Voeg alle taakgerelateerde informatie toe, zoals de functietitel, afdeling of manager van de gebruiker.

    - **Instellingen.** Bepaal of de gebruiker zich kan aanmelden bij Azure Active Directory-tenant. U ook de globale locatie van de gebruiker opgeven.

    - **Contactgegevens.** Voeg alle relevante contactgegevens voor de gebruiker toe, behalve de telefoon- of mobiele contactgegevens van sommige gebruikers (alleen een globale beheerder kan worden bijgewerkt voor gebruikers in beheerdersrollen).

    - **Contactgegevens voor verificatie.** Controleer deze informatie om te controleren of er een actief telefoonnummer en e-mailadres voor de gebruiker is. Deze informatie wordt gebruikt door Azure Active Directory om ervoor te zorgen dat de gebruiker echt de gebruiker is tijdens het aanmelden. Contactgegevens voor verificatie kunnen alleen worden bijgewerkt door een globale beheerder.

4. Selecteer **Opslaan**.

    Al uw wijzigingen worden opgeslagen voor de gebruiker.

    >[!Note]
    >U moet Windows Server Active Directory gebruiken om de identiteit, contactgegevens of taakgegevens bij te werken voor gebruikers waarvan de bron van de autoriteit Windows Server Active Directory is. Nadat u de update hebt voltooid, moet u wachten tot de volgende synchronisatiecyclus is voltooid voordat u de wijzigingen ziet.

## <a name="next-steps"></a>Volgende stappen
Nadat u de profielen van uw gebruikers hebt bijgewerkt, u de volgende basisprocessen uitvoeren:

- [Gebruikers toevoegen of verwijderen](add-users-azure-active-directory.md)

- [Rollen toewijzen aan gebruikers](active-directory-users-assign-role-azure-portal.md)

- [Een basisgroep maken en leden toevoegen](active-directory-groups-create-azure-portal.md)

U ook andere gebruikersbeheertaken uitvoeren, zoals het toewijzen van gemachtigden, het gebruik van beleid en het delen van gebruikersaccounts. Zie [Azure Active Directory-gebruikersbeheerdocumentatie](../users-groups-roles/index.yml)voor meer informatie over andere beschikbare acties.
