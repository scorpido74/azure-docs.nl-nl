---
title: Beheerders beheren gebruikers en apparaten-Azure MFA-Azure Active Directory
description: Hoe kunnen beheerders gebruikers instellingen wijzigen, zoals het forceren van de gebruikers om het controle proces opnieuw uit te voeren.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 190d697dca56fa51d92987f32db0146aa79881aa
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "70162400"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gebruikers instellingen beheren met Azure Multi-Factor Authentication in de Cloud

Als beheerder kunt u de volgende gebruikers-en apparaatinstellingen beheren:

* Gebruikers moeten opnieuw contact methoden opgeven
* App-wacht woorden verwijderen
* MFA vereisen op alle vertrouwde apparaten

## <a name="manage-authentication-methods"></a>Verificatie methoden beheren

Als beheerder die de rol authenticatie beheerder toegewezen, kunt u vereisen dat gebruikers hun wacht woord opnieuw instellen, zich opnieuw registreren voor MFA of bestaande MFA-sessies intrekken vanuit hun gebruikers object.

![Verificatie methoden beheren vanuit de Azure Portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods.png)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer aan de linkerkant **Azure Active Directory**  > **gebruikers**  > **alle gebruikers**.
1. Kies de gebruiker waarvoor u een actie wilt uitvoeren en selecteer **verificatie methoden**.
   - **Wacht woord opnieuw instellen** stelt het wacht woord van de gebruiker opnieuw in en wijst een tijdelijk wacht woord toe dat moet worden gewijzigd bij de volgende aanmelding.
   - **Vereisen dat het opnieuw registreren van MFA vereist** is zodat wanneer de gebruiker de volgende keer aanmeldt, wordt gevraagd om een nieuwe MFA-verificatie methode in te stellen.
   - Met **MFA-sessies intrekken** worden de MFA-sessies van de gebruiker gewist en moeten ze MFA de volgende keer worden uitgevoerd door het beleid op het apparaat.

## <a name="require-users-to-provide-contact-methods-again"></a>Gebruikers moeten opnieuw contact methoden opgeven

Met deze instelling wordt de gebruiker gedwongen het registratie proces opnieuw te volt ooien. Niet-browser-apps blijven werken als de gebruiker app-wacht woorden voor hen heeft.  U kunt de app-wacht woorden van gebruikers verwijderen door ook **alle bestaande app-wacht woorden verwijderen die door de geselecteerde gebruikers zijn gegenereerd**, te selecteren.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Gebruikers vereisen dat ze opnieuw contact methoden opgeven

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer aan de linkerkant **Azure Active Directory**  > **gebruikers**  > **alle gebruikers**.
3. Selecteer aan de rechter kant **multi-factor Authentication** op de werk balk. De multi-factor Authentication-pagina wordt geopend.
4. Schakel het selectie vakje in naast de gebruiker of gebruikers die u wilt beheren. Er wordt aan de rechter kant een lijst met opties voor snelle stappen weer gegeven.
5. Selecteer **gebruikers instellingen beheren**.
6. Schakel het selectie vakje in om **geselecteerde gebruikers te vereisen dat ze opnieuw contact methoden opgeven**.
   ![Require gebruikers opnieuw contact methoden te geven ](./media/howto-mfa-userdevicesettings/reproofup.png)
7. Klik op **Opslaan**.
8. Klik op **sluiten**.

Organisaties kunnen deze stappen met Power shell volt ooien met behulp van het volgende als richt lijn voor het wissen van het kenmerk `StrongAuthenticationMethods`:

```PowerShell
$Upn = "theuser@domain.com"
$noMfaConfig = @()
Set-MsolUser -UserPrincipalName $Upn -StrongAuthenticationMethods $noMfaConfig
```

## <a name="delete-users-existing-app-passwords"></a>Bestaande app-wacht woorden van gebruikers verwijderen

Met deze instelling worden alle app-wacht woorden verwijderd die een gebruiker heeft gemaakt. Niet-browser-apps die zijn gekoppeld aan deze app-wacht woorden, werken niet totdat er een nieuw app-wacht woord is gemaakt.

### <a name="how-to-delete-users-existing-app-passwords"></a>Bestaande app-wacht woorden van gebruikers verwijderen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer aan de linkerkant **Azure Active Directory**  > **gebruikers**  > **alle gebruikers**.
3. Selecteer aan de rechter kant **multi-factor Authentication** op de werk balk. De multi-factor Authentication-pagina wordt geopend.
4. Schakel het selectie vakje in naast de gebruiker of gebruikers die u wilt beheren. Er wordt aan de rechter kant een lijst met opties voor snelle stappen weer gegeven.
5. Selecteer **gebruikers instellingen beheren**.
6. Schakel het selectie vakje in om **alle bestaande app-wacht woorden te verwijderen die door de geselecteerde gebruikers zijn gegenereerd**.
   ![Delete alle bestaande app-wacht woorden ](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Klik op **Opslaan**.
8. Klik op **sluiten**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>MFA herstellen op alle onthouden apparaten voor een gebruiker

Een van de Configureer bare functies van Azure Multi-Factor Authentication geeft uw gebruikers de mogelijkheid om apparaten als vertrouwd te markeren. Zie [Azure multi-factor Authentication-instellingen configureren](howto-mfa-mfasettings.md#remember-multi-factor-authentication)voor meer informatie.

Gebruikers kunnen zich afmelden voor verificatie in twee stappen voor een configureerbaar aantal dagen op hun normale apparaten. Als een account is aangetast of een vertrouwd apparaat verloren is gegaan, moet u de vertrouwde status kunnen verwijderen en opnieuw verificatie in twee stappen vereisen.

Als u dit selectie vakje inschakelt, moeten gebruikers **van meerdere factoren** de verificatie in twee stappen uitvoeren wanneer ze zich de volgende keer aanmelden, zelfs als ze hun apparaat als vertrouwd hebben gemarkeerd.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>MFA herstellen op alle onderbroken apparaten voor een gebruiker

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer aan de linkerkant **Azure Active Directory**  > **gebruikers**  > **alle gebruikers**.
3. Selecteer aan de rechter kant **multi-factor Authentication** op de werk balk. De multi-factor Authentication-pagina wordt geopend.
4. Schakel het selectie vakje in naast de gebruiker of gebruikers die u wilt beheren. Er wordt aan de rechter kant een lijst met opties voor snelle stappen weer gegeven.
5. Selecteer **gebruikers instellingen beheren**.
6. Schakel het selectie vakje in voor het **herstellen van multi-factor Authentication op alle onthouden apparaten** 
    ![Restore multi-factor Authentication op alle onthouden apparaten ](./media/howto-mfa-userdevicesettings/rememberdevices.png)
7. Klik op **Opslaan**.
8. Klik op **sluiten**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het configureren van [Azure multi-factor Authentication-instellingen](howto-mfa-mfasettings.md)
- Als uw gebruikers hulp nodig hebben, kunt u ze naar de [Gebruikers handleiding voor verificatie in twee stappen](../user-help/multi-factor-authentication-end-user.md) laten wijzen
