---
title: Het wachtwoord van een gebruiker opnieuw instellen - Azure Active Directory | Microsoft Documenten
description: Instructies voor het opnieuw instellen van het wachtwoord van een gebruiker met Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4fdbbd4d71a9c97259678413cd9e59ee8aeae6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69032673"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Het wachtwoord van een gebruiker opnieuw instellen met Azure Active Directory

Als beheerder u het wachtwoord van een gebruiker opnieuw instellen als het wachtwoord wordt vergeten, als de gebruiker wordt uitgesloten van een apparaat of als de gebruiker nooit een wachtwoord heeft ontvangen.

>[!Note]
>Tenzij uw Azure AD-tenant de thuismap voor een gebruiker is, u het wachtwoord niet opnieuw instellen. Dit betekent dat als uw gebruiker zich aanmeldt bij uw organisatie met behulp van een account van een andere organisatie, een Microsoft-account of een Google-account, u zijn wachtwoord niet opnieuw instellen.<br><br>Als uw gebruiker een bron van autoriteit heeft als Windows Server Active Directory, u het wachtwoord alleen opnieuw instellen als u het terugschrijven van wachtwoorden hebt ingeschakeld.<br><br>Als uw gebruiker een bron van autoriteit heeft als Extern Azure AD, u het wachtwoord niet opnieuw instellen. Alleen de gebruiker of een beheerder in Extern Azure AD kan het wachtwoord opnieuw instellen.

>[!Note]
>Zie [Uw werk- of schoolwachtwoord](../user-help/active-directory-passwords-update-your-own-password.md)opnieuw instellen als u geen beheerder bent en in plaats daarvan op zoek bent naar instructies over het opnieuw instellen van uw eigen werk- of schoolwachtwoord.

## <a name="to-reset-a-password"></a>Een wachtwoord opnieuw instellen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) als gebruikersbeheerder of wachtwoordbeheerder. Zie [Beheerdersrollen toewijzen in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md#available-roles) voor meer informatie over de beschikbare rollen

2. Selecteer **Azure Active Directory,** selecteer **Gebruikers,** zoek naar en selecteer de gebruiker die de reset nodig heeft en selecteer **Vervolgens Wachtwoord opnieuw instellen**.

    De pagina **Alain Charon - Profiel** wordt weergegeven met de optie Wachtwoord opnieuw **instellen.**

    ![Profielpagina van de gebruiker, met de optie Wachtwoord opnieuw instellen gemarkeerd](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. Selecteer **op** de pagina Wachtwoord opnieuw instellen de optie **Wachtwoord opnieuw instellen**.

    > [!Note]
    > Bij het gebruik van Azure Active Directory wordt een tijdelijk wachtwoord automatisch gegenereerd voor de gebruiker. Wanneer u Active Directory on-premises gebruikt, maakt u het wachtwoord voor de gebruiker.

4. Kopieer het wachtwoord en geef het aan de gebruiker. De gebruiker moet het wachtwoord wijzigen tijdens het volgende aanmeldingsproces.

    >[!Note]
    >Het tijdelijke wachtwoord verloopt nooit. De volgende keer dat de gebruiker zich aanmeldt, werkt het wachtwoord nog steeds, ongeacht hoeveel tijd is verstreken sinds het tijdelijke wachtwoord is gegenereerd.

## <a name="next-steps"></a>Volgende stappen

Nadat u het wachtwoord van uw gebruiker opnieuw hebt ingesteld, u de volgende basisprocessen uitvoeren:

- [Gebruikers toevoegen of verwijderen](add-users-azure-active-directory.md)

- [Rollen toewijzen aan gebruikers](active-directory-users-assign-role-azure-portal.md)

- [Profielgegevens toevoegen of wijzigen](active-directory-users-profile-azure-portal.md)

- [Een basisgroep maken en leden toevoegen](active-directory-groups-create-azure-portal.md)

U ook complexere gebruikersscenario's uitvoeren, zoals het toewijzen van gemachtigden, het gebruik van beleid en het delen van gebruikersaccounts. Zie [Azure Active Directory-gebruikersbeheerdocumentatie](../users-groups-roles/index.yml)voor meer informatie over andere beschikbare acties.
