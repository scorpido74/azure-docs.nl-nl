---
title: Gebruikers en apparaten azure MFA beheren - Azure Active Directory
description: Hoe kunnen beheerders gebruikersinstellingen wijzigen, zoals de gebruikers dwingen om het controleproces opnieuw uit te voeren.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d94fa761980151c420984eb7e8c3254a3509ef
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653502"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gebruikersinstellingen beheren met Azure Multi-Factor Authentication in de cloud

Als beheerder u de volgende gebruikers- en apparaatinstellingen beheren:

* Gebruikers verplichten om opnieuw contactmethoden op te geven
* App-wachtwoorden verwijderen
* MFA vereisen op alle vertrouwde apparaten

## <a name="manage-authentication-methods"></a>Verificatiemethoden beheren

Als beheerder de rol Verificatiebeheerder heeft toegewezen, u gebruikers verplichten hun wachtwoord opnieuw in te stellen, zich opnieuw te registreren voor MFA of bestaande MFA-sessies uit hun gebruikersobject in te trekken.

![Verificatiemethoden beheren vanuit de Azure-portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer aan de linkerkant **Azure Active Directory** > **Users** > **All users**.
1. Kies de gebruiker waarop u een actie wilt uitvoeren en selecteer **Verificatiemethoden**.
   - **Als wachtwoord opnieuw worden ingesteld,** wordt het wachtwoord van de gebruiker opnieuw ingesteld en wordt een tijdelijk wachtwoord toegewezen dat moet worden gewijzigd bij het volgende aanmeldingsformulier.
   - **Vereisen Opnieuw registreren MFA** zal het zo maken dat wanneer de gebruiker zich de volgende keer aanmeldt, ze zullen worden gevraagd om een nieuwe MFA-authenticatiemethode in te stellen.
   - **Mfa-sessies intrekken** wist de onthouden MFA-sessies van de gebruiker en vereist dat deze MFA uitvoert de volgende keer dat dit vereist is door het beleid op het apparaat.

## <a name="delete-users-existing-app-passwords"></a>Gebruikers bestaande app-wachtwoorden verwijderen

Met deze instelling worden alle app-wachtwoorden die een gebruiker heeft gemaakt, verwijderd. Niet-browserapps die aan deze app-wachtwoorden zijn gekoppeld, werken niet meer totdat er een nieuw app-wachtwoord is gemaakt. Globale beheerdersmachtigingen zijn vereist om deze actie uit te voeren.

### <a name="how-to-delete-users-existing-app-passwords"></a>Bestaande app-wachtwoorden voor gebruikers verwijderen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer aan de linkerkant **Azure Active Directory** > **Users** > **All users**.
3. Selecteer rechts **Multi-Factor Authentication** op de werkbalk. De pagina met meervoudige verificatie wordt geopend.
4. Schakel het selectievakje in naast de gebruiker of gebruikers die u wilt beheren. Aan de rechterkant verschijnt een lijst met opties voor snelle stappen.
5. Selecteer **Gebruikersinstellingen beheren**.
6. Schakel het selectievakje in voor **Alle bestaande app-wachtwoorden die door de geselecteerde gebruikers zijn gegenereerd, verwijderen**.
   ![Alle bestaande app-wachtwoorden verwijderen](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Klik op **Opslaan**.
8. Klik **op sluiten**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [configureren van Azure Multi-Factor Authentication-instellingen](howto-mfa-mfasettings.md)
- Als uw gebruikers hulp nodig hebben, wijst u deze naar de [gebruikershandleiding voor verificatie in twee stappen](../user-help/multi-factor-authentication-end-user.md)
