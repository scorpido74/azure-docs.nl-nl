---
title: Het wacht woord van een gebruiker opnieuw instellen-Azure Active Directory | Microsoft Docs
description: Instructies voor het opnieuw instellen van het wacht woord van een gebruiker met behulp van Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 397c74203aae2f52ce81844695266cc36fdf3042
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370896"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Het wachtwoord van een gebruiker opnieuw instellen met Azure Active Directory

Als beheerder kunt u het wacht woord van een gebruiker opnieuw instellen als het wacht woord is verg eten, als de gebruiker de verbinding met een apparaat heeft geblokkeerd of als de gebruiker nooit een wacht woord heeft ontvangen.

>[!Note]
>Tenzij uw Azure AD-Tenant de basis directory voor een gebruiker is, kunt u het wacht woord niet opnieuw instellen. Dit betekent dat als uw gebruiker zich aanmeldt bij uw organisatie met behulp van een account van een andere organisatie, een Microsoft-account of een Google-account, het wacht woord niet opnieuw kan worden ingesteld.<br><br>Als uw gebruiker een bron van de autoriteit als Windows Server Active Directory heeft, kunt u het wacht woord alleen opnieuw instellen als u wacht woord terugschrijven hebt ingeschakeld.<br><br>Als uw gebruiker een bron van de autoriteit als externe Azure AD heeft, kunt u het wacht woord niet opnieuw instellen. Alleen de gebruiker of een beheerder in externe Azure AD kan het wacht woord opnieuw instellen.

>[!Note]
>Als u geen beheerder bent en u in plaats daarvan naar instructies zoekt over het opnieuw instellen van uw eigen werk-of school wachtwoord, raadpleegt u [het wacht woord voor uw werk of school opnieuw instellen](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="to-reset-a-password"></a>Een wacht woord opnieuw instellen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) als gebruikers beheerder of wachtwoord beheerder. Zie [beheerders rollen toewijzen in azure Active Directory](../roles/permissions-reference.md#available-roles) voor meer informatie over de beschik bare rollen.

2. Selecteer **Azure Active Directory**, selecteer **gebruikers**, zoek de gebruiker die het opnieuw moet instellen en selecteer vervolgens **wacht woord opnieuw instellen**.

    De pagina **Alain Charon-profiel** wordt weer gegeven met de optie **wacht woord opnieuw instellen** .

    ![De profiel pagina van de gebruiker, met de optie wacht woord opnieuw instellen gemarkeerd](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. Selecteer **wacht woord opnieuw instellen**op de pagina **wacht woord opnieuw instellen** .

    > [!Note]
    > Wanneer u Azure Active Directory gebruikt, wordt er automatisch een tijdelijk wacht woord voor de gebruiker gegenereerd. Wanneer u Active Directory on-premises gebruikt, maakt u het wacht woord voor de gebruiker.

4. Kopieer het wacht woord en geef het aan de gebruiker. De gebruiker moet het wacht woord wijzigen tijdens het volgende aanmeldings proces.

    >[!Note]
    >Het tijdelijke wacht woord verloopt nooit. De volgende keer dat de gebruiker zich aanmeldt, zal het wacht woord nog steeds werken, ongeacht hoeveel tijd er is verstreken sinds het tijdelijke wacht woord is gegenereerd.

## <a name="next-steps"></a>Volgende stappen

Nadat u het wacht woord van de gebruiker opnieuw hebt ingesteld, kunt u de volgende basis processen uitvoeren:

- [Gebruikers toevoegen of verwijderen](add-users-azure-active-directory.md)

- [Rollen toewijzen aan gebruikers](active-directory-users-assign-role-azure-portal.md)

- [Profiel gegevens toevoegen of wijzigen](active-directory-users-profile-azure-portal.md)

- [Een basisgroep maken en leden toevoegen](active-directory-groups-create-azure-portal.md)

Of u kunt complexere gebruikers scenario's uitvoeren, zoals het toewijzen van gemachtigden, het gebruiken van beleid en het delen van gebruikers accounts. Zie [Azure Active Directory-documentatie voor gebruikers beheer](../enterprise-users/index.yml)voor meer informatie over andere beschik bare acties.
