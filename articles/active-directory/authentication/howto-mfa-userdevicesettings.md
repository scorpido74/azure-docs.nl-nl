---
title: Gebruikersinstellingen beheren voor Azure Multi-Factor Authentication - Azure Active Directory
description: Meer informatie over hoe u Azure Active Directory-gebruikersinstellingen voor Azure Multi-Factor Authentication configureren
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 048224a55c2bbcbc99281d070d88d34e2dc77168
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309762"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Gebruikersinstellingen voor Azure Multi-Factor Authentication beheren

Als u de gebruikers van Azure Multi-Factor Authentication wilt beheren, u gebruikers verplichten hun wachtwoord opnieuw in te stellen, zich opnieuw te registreren voor MFA of bestaande MFA-sessies in te trekken. Voor gebruikers die app-wachtwoorden hebben gedefinieerd, u er ook voor kiezen om deze wachtwoorden te verwijderen, waardoor verouderde verificatie in die toepassingen mislukt. Deze acties kunnen nodig zijn als u een gebruiker hulp moet bieden of als u zijn beveiligingsstatus wilt herstellen.

## <a name="manage-user-authentication-options"></a>Opties voor gebruikersverificatie beheren

Als u de rol *Verificatiebeheerder* toegewezen krijgt, u gebruikers verplichten hun wachtwoord opnieuw in te stellen, zich opnieuw te registreren voor MFA of bestaande MFA-sessies uit hun gebruikersobject in te trekken. Voer de volgende stappen uit om gebruikersinstellingen te beheren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer aan de linkerkant **Azure Active Directory** > **Users** > **All users**.
1. Kies de gebruiker waarop u een actie wilt uitvoeren en selecteer **Verificatiemethoden**. Kies boven aan het venster een van de volgende opties voor de gebruiker:
   - **Reset Password** reset het wachtwoord van de gebruiker en wijst een tijdelijk wachtwoord toe dat moet worden gewijzigd bij de volgende aanmelding.
   - Mfa opnieuw registreren is zo dat wanneer de gebruiker zich de volgende keer aanmeldt, wordt gevraagd een nieuwe **MFA-verificatiemethode** in te stellen.
   - **Mfa-sessies intrekken** wist de onthouden MFA-sessies van de gebruiker en vereist dat deze MFA uitvoert de volgende keer dat het beleid op het apparaat vereist is.

   ![Verificatiemethoden beheren vanuit de Azure-portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>Gebruikers bestaande app-wachtwoorden verwijderen

Indien nodig u alle app-wachtwoorden verwijderen die een gebruiker heeft gemaakt. Niet-browserapps die aan deze app-wachtwoorden zijn gekoppeld, werken niet meer totdat er een nieuw app-wachtwoord is gemaakt. *Globale beheerdersmachtigingen* zijn vereist om deze actie uit te voeren.

Voer de volgende stappen uit om de app-wachtwoorden van een gebruiker te verwijderen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer aan de linkerkant **Azure Active Directory** > **Users** > All**users**.
1. Selecteer **Meervoudige verificatie**. Mogelijk moet u naar rechts scrollen om deze menuoptie te zien. Selecteer de onderstaande voorbeeldschermafbeelding om het volledige Azure-portalvenster en de locatie van het menu te bekijken:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Multifactorverificatie selecteren in het venster Gebruikers in Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Schakel het selectievakje in naast de gebruiker of gebruikers die u wilt beheren. Aan de rechterkant verschijnt een lijst met opties voor snelle stappen.
1. Schakel **Gebruikersinstellingen beheren**en schakel het selectievakje in voor **Alle bestaande app-wachtwoorden die door de geselecteerde gebruikers zijn gegenereerd,** verwijderen, zoals in het volgende voorbeeld: ![Alle bestaande app-wachtwoorden verwijderen](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Selecteer **Opslaan**en **sluit vervolgens**.

## <a name="next-steps"></a>Volgende stappen

Dit artikel heeft geholpen bij het configureren van individuele gebruikersinstellingen. Zie Instellingen voor Azure Multi-Factor Authentication configureren voor Azure Multi-Factor Authentication [service-instellingen.](howto-mfa-mfasettings.md)

Als uw gebruikers hulp nodig hebben, raadpleegt u de [gebruikershandleiding voor Azure Multi-Factor Authentication.](../user-help/multi-factor-authentication-end-user.md)
