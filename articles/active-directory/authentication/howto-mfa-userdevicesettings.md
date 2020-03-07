---
title: Gebruikers en apparaten beheren Azure MFA-Azure Active Directory
description: Hoe kunnen beheerders gebruikers instellingen wijzigen, zoals het forceren van de gebruikers om het controle proces opnieuw uit te voeren.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07845bb5b742b1bcfbb22d260457e9a8e16edab6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378031"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gebruikers instellingen beheren met Azure Multi-Factor Authentication in de Cloud

Als beheerder kunt u de volgende gebruikers-en apparaatinstellingen beheren:

* Gebruikers moeten opnieuw contact methoden opgeven
* App-wacht woorden verwijderen
* MFA vereisen op alle vertrouwde apparaten

## <a name="manage-authentication-methods"></a>Verificatie methoden beheren

Als beheerder die de rol authenticatie beheerder toegewezen, kunt u vereisen dat gebruikers hun wacht woord opnieuw instellen, zich opnieuw registreren voor MFA of bestaande MFA-sessies intrekken vanuit hun gebruikers object.

![Verificatie methoden beheren vanuit de Azure Portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer aan de linkerkant **Azure Active Directory** > **gebruikers** > **alle gebruikers**.
1. Kies de gebruiker waarvoor u een actie wilt uitvoeren en selecteer **verificatie methoden**.
   - **Wacht woord opnieuw instellen** stelt het wacht woord van de gebruiker opnieuw in en wijst een tijdelijk wacht woord toe dat moet worden gewijzigd bij de volgende aanmelding.
   - **Vereisen dat het opnieuw registreren van MFA vereist** is zodat wanneer de gebruiker de volgende keer aanmeldt, wordt gevraagd om een nieuwe MFA-verificatie methode in te stellen.
   - Met **MFA-sessies intrekken** worden de MFA-sessies van de gebruiker gewist en moeten ze MFA de volgende keer worden uitgevoerd door het beleid op het apparaat.

## <a name="delete-users-existing-app-passwords"></a>Bestaande app-wacht woorden van gebruikers verwijderen

Met deze instelling worden alle app-wacht woorden verwijderd die een gebruiker heeft gemaakt. Niet-browser-apps die zijn gekoppeld aan deze app-wacht woorden, werken niet totdat er een nieuw app-wacht woord is gemaakt. Globale beheerders machtigingen zijn vereist om deze actie uit te voeren.

### <a name="how-to-delete-users-existing-app-passwords"></a>Bestaande app-wacht woorden van gebruikers verwijderen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer aan de linkerkant **Azure Active Directory** > **gebruikers** > **alle gebruikers**.
3. Selecteer aan de rechter kant **multi-factor Authentication** op de werk balk. De multi-factor Authentication-pagina wordt geopend.
4. Schakel het selectie vakje in naast de gebruiker of gebruikers die u wilt beheren. Er wordt aan de rechter kant een lijst met opties voor snelle stappen weer gegeven.
5. Selecteer **gebruikers instellingen beheren**.
6. Schakel het selectie vakje in om **alle bestaande app-wacht woorden te verwijderen die door de geselecteerde gebruikers zijn gegenereerd**.
   ![alle bestaande app-wacht woorden verwijderen](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Klik op **Opslaan**.
8. Klik op **sluiten**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het configureren van [Azure multi-factor Authentication-instellingen](howto-mfa-mfasettings.md)
- Als uw gebruikers hulp nodig hebben, kunt u ze naar de [Gebruikers handleiding voor verificatie in twee stappen](../user-help/multi-factor-authentication-end-user.md) laten wijzen
