---
title: Aan de slag met gecombineerde registratie - Azure Active Directory
description: Gecombineerde Azure AD Multi-Factor Authentication en selfservice password reset registration inschakelen (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f454b0296a3463d7346c2945b21162e5a38c0eb7
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652511"
---
# <a name="enable-combined-security-information-registration-preview"></a>Registratie van gecombineerde beveiligingsgegevens inschakelen (voorbeeld)

Voordat u de nieuwe ervaring inschakelt, bekijkt u het artikel [Gecombineerde registratie van beveiligingsgegevens (voorbeeld)](concept-registration-mfa-sspr-combined.md) om ervoor te zorgen dat u de functionaliteit en effecten van deze functie begrijpt.

![Gecombineerde registratie van beveiligingsgegevens verbeterde ervaring](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Gecombineerde beveiligingsgegevensregistratie voor Azure Multi-Factor Authentication en Azure Active Directory (Azure AD) selfservice wachtwoord reset is een openbare preview-functie van Azure AD. Zie [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews) voor meer informatie.|
|     |

> [!NOTE]
> Organisaties die de vorige preview hebben ingeschakeld voor het registreren en beheren van beveiligingsgegevens, moeten de onderstaande stappen uitvoeren om de verbeterde voorbeeldervaring in te schakelen. Voor organisaties die de overstap niet maken, schakelt Microsoft op 8 oktober 2019 van gebruiker van de vorige preview voor voor het registreren en beheren van beveiligingsgegevens naar de verbeterde ervaring. 
> 
> Als u geen enkele versie van de preview hebt ingeschakeld, wordt er geen invloed op uw organisatie.

## <a name="enable-combined-registration"></a>Gecombineerde registratie inschakelen

Voer deze stappen uit om gecombineerde registratie in te schakelen:

1. Meld u aan bij de Azure-portal als gebruikersbeheerder of globale beheerder.
2. Ga naar Azure Active > **Directory-gebruikersinstellingen** > **Beheer de preview-instellingen voor gebruikersfuncties**. **Azure Active Directory**
3. Onder **Gebruikers kunnen voorbeeldfuncties gebruiken voor het registreren en beheren van beveiligingsgegevens,** kiezen om in te schakelen voor een **geselecteerde** groep gebruikers of voor **alle** gebruikers.

   ![De gecombineerde preview-ervaring voor beveiligingsgegevens inschakelen voor alle gebruikers](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!IMPORTANT]
> Vanaf maart 2019 zijn de opties voor bellen niet beschikbaar voor Multi-Factor Authentication- en SSPR-gebruikers in gratis/trial Azure AD-tenants. SMS-berichten worden niet beïnvloed door deze wijziging. De telefoongespreksopties zijn nog steeds beschikbaar voor gebruikers in betaalde Azure AD-tenants.

> [!NOTE]
> Nadat u gecombineerde registratie hebt ingeschakeld, kunnen gebruikers die hun telefoonnummer of mobiele app registreren of bevestigen via de nieuwe ervaring deze gebruiken voor Multi-Factor Authentication en SSPR, als deze methoden zijn ingeschakeld in het Multi-Factor Authentication- en SSPR-beleid. Als u deze ervaring vervolgens uitschakelt, moeten gebruikers die `https://aka.ms/ssprsetup` naar de vorige SSPR-registratiepagina gaan, multifactorauthenticatie uitvoeren voordat ze toegang hebben tot de pagina.

Als u de lijst met site-toewijzing naar zone hebt geconfigureerd in Internet Explorer, moeten de volgende sites zich in dezelfde zone bevinden:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Beleid voor voorwaardelijke toegang voor gecombineerde registratie

Beveiligen wanneer en hoe gebruikers zich registreren voor Azure Multi-Factor Authentication en self-service wachtwoord resetten is nu mogelijk met gebruikersacties in het beleid voor voorwaardelijke toegang. Deze voorbeeldfunctie is beschikbaar voor organisaties die de [gecombineerde registratievoorbeeld](../authentication/concept-registration-mfa-sspr-combined.md)hebben ingeschakeld. Deze functionaliteit kan worden ingeschakeld in organisaties waar ze willen dat gebruikers zich registreren voor Azure Multi-Factor Authentication en SSPR vanaf een centrale locatie, zoals een vertrouwde netwerklocatie tijdens HR-onboarding. Zie het artikel [Wat is de locatievoorwaarde in Voorwaardelijke Active Directory Voorwaardelijke toegang voor](../conditional-access/location-condition.md#named-locations) meer informatie over het maken van vertrouwde locaties in Voorwaardelijke toegang?

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Een beleid maken om registratie vanaf een vertrouwde locatie te vereisen

Het volgende beleid is van toepassing op alle geselecteerde gebruikers, die proberen zich te registreren met behulp van de gecombineerde registratie-ervaring, en blokkeert de toegang, tenzij ze verbinding maken vanaf een locatie die is gemarkeerd als vertrouwd netwerk.

![Een CA-beleid maken om de registratie van beveiligingsgegevens te beheren](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Blader in de **Azure-portal**naar voorwaardelijke**toegang** **voor Azure Active Directory** > **Beveiliging** > 
1. Selecteer **Nieuw beleid**.
1. Voer in Naam een naam in voor dit beleid. Gecombineerde **beveiligingsgegevensregistratie op vertrouwde netwerken** bijvoorbeeld
1. Klik **onder Toewijzingen**op **Gebruikers en groepen**en selecteer de gebruikers en groepen waarop u dit beleid wilt toepassen

   > [!WARNING]
   > Gebruikers moeten zijn ingeschakeld voor de [gecombineerde registratie preview](../authentication/howto-registration-mfa-sspr-combined.md).

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

[Gebruikers dwingen om verificatiemethoden opnieuw te registreren](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[Beschikbare methoden voor multi-factor authenticatie en SSPR](concept-authentication-methods.md)

[Self-service voor wachtwoord opnieuw instellen configureren](howto-sspr-deployment.md)

[Azure Multi-Factor Authentication configureren](howto-mfa-getstarted.md)

[Problemen met gecombineerde beveiligingsgegevensregistratie oplossen](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Wat is de locatievoorwaarde in voorwaardelijke toegang van Azure Active Directory?](../conditional-access/location-condition.md)
