---
title: Aan de slag met gecombineerde registratie - Azure Active Directory
description: Gecombineerde Azure AD Multi-Factor Authentication en selfservice wachtwoordresetregistratie inschakelen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d9544b1f4dd5ecbf66493f26c373c5502dce68a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451075"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Gecombineerde beveiligingsgegevensregistratie inschakelen in Azure Active Directory

Voor gecombineerde registratie registreerden gebruikers verificatiemethoden voor Azure Multi-Factor Authentication en selfservice password reset (SSPR) afzonderlijk. Mensen waren in de war dat soortgelijke methoden werden gebruikt voor Multi-Factor Authentication en SSPR, maar ze moesten registreren voor beide functies. Nu, met gecombineerde registratie, kunnen gebruikers zich eenmalig registreren en de voordelen van zowel Multi-Factor Authentication als SSPR krijgen.

Voordat u de nieuwe ervaring inschakelt, bekijkt u het artikel [Gecombineerde registratie van beveiligingsgegevens](concept-registration-mfa-sspr-combined.md) om ervoor te zorgen dat u de functionaliteit en effecten van deze functie begrijpt.

![Gecombineerde registratie van beveiligingsgegevens verbeterde ervaring](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Gecombineerde registratie inschakelen

Voer deze stappen uit om gecombineerde registratie in te schakelen:

1. Meld u aan bij de Azure-portal als gebruikersbeheerder of globale beheerder.
2. Ga naar Azure Active > **Directory-gebruikersinstellingen** > **Beheer de preview-instellingen voor gebruikersfuncties**. **Azure Active Directory**
3. Onder **Gebruikers kunnen voorbeeldfuncties gebruiken voor het registreren en beheren van beveiligingsgegevens,** kiezen om in te schakelen voor een **geselecteerde** groep gebruikers of voor **alle** gebruikers.

   ![De gecombineerde preview-ervaring voor beveiligingsgegevens inschakelen voor alle gebruikers](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!NOTE]
> Nadat u gecombineerde registratie hebt ingeschakeld, kunnen gebruikers die hun telefoonnummer of mobiele app registreren of bevestigen via de nieuwe ervaring deze gebruiken voor Multi-Factor Authentication en SSPR, als deze methoden zijn ingeschakeld in het Multi-Factor Authentication- en SSPR-beleid. Als u deze ervaring vervolgens uitschakelt, moeten gebruikers die `https://aka.ms/ssprsetup` naar de vorige SSPR-registratiepagina gaan, multifactorauthenticatie uitvoeren voordat ze toegang hebben tot de pagina.

Als u de lijst met site-toewijzing naar zone hebt geconfigureerd in Internet Explorer, moeten de volgende sites zich in dezelfde zone bevinden:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Beleid voor voorwaardelijke toegang voor gecombineerde registratie

Beveiligen wanneer en hoe gebruikers zich registreren voor Azure Multi-Factor Authentication en self-service wachtwoord resetten is nu mogelijk met gebruikersacties in het beleid voor voorwaardelijke toegang. Deze functie is beschikbaar voor organisaties die de [gecombineerde registratiefunctie](../authentication/concept-registration-mfa-sspr-combined.md)hebben ingeschakeld. Deze functionaliteit kan worden ingeschakeld in organisaties waar ze willen dat gebruikers zich registreren voor Azure Multi-Factor Authentication en SSPR vanaf een centrale locatie, zoals een vertrouwde netwerklocatie tijdens HR-onboarding. Zie het artikel [Wat is de locatievoorwaarde in Voorwaardelijke Active Directory Voorwaardelijke toegang voor](../conditional-access/location-condition.md#named-locations) meer informatie over het maken van vertrouwde locaties in Voorwaardelijke toegang?

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Een beleid maken om registratie vanaf een vertrouwde locatie te vereisen

Het volgende beleid is van toepassing op alle geselecteerde gebruikers, die proberen zich te registreren met behulp van de gecombineerde registratie-ervaring, en blokkeert de toegang, tenzij ze verbinding maken vanaf een locatie die is gemarkeerd als vertrouwd netwerk.

![Een CA-beleid maken om de registratie van beveiligingsgegevens te beheren](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Blader in de **Azure-portal**naar voorwaardelijke**toegang** **voor Azure Active Directory** > **Beveiliging** > 
1. Selecteer **Nieuw beleid**.
1. Voer in Naam een naam in voor dit beleid. Gecombineerde **beveiligingsgegevensregistratie op vertrouwde netwerken** bijvoorbeeld
1. Klik **onder Toewijzingen**op **Gebruikers en groepen**en selecteer de gebruikers en groepen waarop u dit beleid wilt toepassen

   > [!WARNING]
   > Gebruikers moeten zijn ingeschakeld voor [gecombineerde registratie](../authentication/howto-registration-mfa-sspr-combined.md).

1. Selecteer **Onder Cloud-apps of -acties**de optie **Gebruikersacties**, schakel **beveiligingsgegevens registreren (voorbeeld)**
1. Onder **voorwaarden** > **locaties**
   1. **Ja** configureren
   1. **Elke locatie opnemen**
   1. Alle **vertrouwde locaties** uitsluiten
   1. Klik op **Gereed** op het blad Locaties
   1. Klik op **Gereed** op het onderseer van de voorwaarden
1. Onder **Toegangscontroles** > **Grant**
   1. Klik **op Toegang blokkeren**
   1. Klik vervolgens op **Selecteren**
1. **Beleid inschakelen instellen** op **Aan**
1. Klik vervolgens op **Maken**

## <a name="next-steps"></a>Volgende stappen

Als u hulp nodig hebt, raadpleegt u hoe [u problemen oplossen met gecombineerde registratie van beveiligingsgegevens](howto-registration-mfa-sspr-combined-troubleshoot.md) of hoe u bepalen wat de [locatievoorwaarde is in voorwaardelijke toegang van Azure Active Directory?](../conditional-access/location-condition.md)

Als u de functies in uw Azure [AD-tenant](tutorial-enable-sspr.md) wilt inschakelen, raadpleegt u de zelfstudies om het opnieuw instellen van het zelfservicewachtwoord in te schakelen en Azure [Multi-Factor Authentication in te schakelen.](tutorial-enable-azure-mfa.md)

Meer informatie over het [inschakelen van gecombineerde registratie in uw tenant](howto-registration-mfa-sspr-combined.md) of het dwingen van gebruikers om [verificatiemethoden opnieuw te registreren.](howto-mfa-userdevicesettings.md#manage-user-authentication-options)

U ook de [beschikbare methoden voor Azure Multi-Factor Authentication en SSPR bekijken.](concept-authentication-methods.md)
