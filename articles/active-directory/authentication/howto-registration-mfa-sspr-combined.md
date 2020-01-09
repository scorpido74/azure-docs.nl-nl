---
title: Aan de slag met gecombineerde registratie-Azure Active Directory
description: Gecombineerde Azure AD Multi-Factor Authentication en self-service voor wachtwoord herstel inschakelen (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25c64d9e959b1d68de23e83e26d3495bd3939986
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425152"
---
# <a name="enable-combined-security-information-registration-preview"></a>Registratie van gecombineerde beveiligings gegevens inschakelen (preview-versie)

Voordat u de nieuwe ervaring inschakelt, raadpleegt u het artikel [registratie van gecombineerde beveiligings gegevens (preview)](concept-registration-mfa-sspr-combined.md) om ervoor te zorgen dat u de functionaliteit en gevolgen van deze functie begrijpt.

![Verbeterde ervaring bij het registreren van gecombineerde beveiligings gegevens](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Registratie van gecombineerde beveiligings gegevens voor Azure Multi-Factor Authentication en Azure Active Directory (Azure AD) self-service voor het opnieuw instellen van wacht woorden is een open bare preview-functie van Azure AD. Zie [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews) voor meer informatie.|
|     |

> [!NOTE]
> Organisaties die de vorige Preview voor het registreren en beheren van beveiligings gegevens hebben ingeschakeld, moeten de volgende stappen uitvoeren om de verbeterde preview-ervaring in te scha kelen. Voor organisaties die de switch niet maken, op 8 oktober 2019, schakelt micro soft gebruikers van de vorige Preview uit voor het registreren en beheren van beveiligings gegevens voor de verbeterde ervaring. 
> 
> Als u geen versie van de preview hebt ingeschakeld, heeft dit geen invloed op uw organisatie.

## <a name="enable-combined-registration"></a>Gecombineerde registratie inschakelen

Voer de volgende stappen uit om gecombineerde registratie in te scha kelen:

1. Meld u aan bij de Azure Portal als gebruikers beheerder of globale beheerder.
2. Ga naar **Azure Active Directory** > **gebruikers instellingen** > **instellingen voor de preview-functie voor gebruikers onderdelen beheren**.
3. Onder **gebruikers kunnen preview-functies gebruiken voor het registreren en beheren van beveiligings gegevens**, kiezen om in te scha kelen voor een **geselecteerde** groep gebruikers of voor **alle** gebruikers.

   ![De preview-ervaring voor gecombineerde beveiligings info voor alle gebruikers inschakelen](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!IMPORTANT]
> Vanaf maart 2019 zijn de opties voor telefoon gesprekken niet beschikbaar voor Multi-Factor Authentication-en SSPR-gebruikers in gratis/proef versie van Azure AD-tenants. SMS-berichten worden niet beïnvloed door deze wijziging. De opties voor telefoon oproepen blijven beschikbaar voor gebruikers in betaalde Azure AD-tenants.

> [!NOTE]
> Nadat u gecombineerde registratie hebt ingeschakeld, kunnen gebruikers die hun telefoon nummer of mobiele app via de nieuwe ervaring registreren of bevestigen, deze gebruiken voor Multi-Factor Authentication en SSPR als deze methoden zijn ingeschakeld in de Multi-Factor Authentication en SSPR restrictie. Als u deze ervaring vervolgens uitschakelt, moeten gebruikers die naar de vorige registratie pagina van SSPR op `https://aka.ms/ssprsetup`, multi-factor Authentication uitvoeren voordat ze toegang kunnen krijgen tot de pagina.

Als u de lijst site toewijzing met zone hebt geconfigureerd in Internet Explorer, moeten de volgende sites zich in dezelfde zone bekomen:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Beleid voor voorwaardelijke toegang voor gecombineerde registratie

Beveiligen wanneer en hoe gebruikers zich registreren voor Azure Multi-Factor Authentication en self-service voor wacht woord opnieuw instellen is nu mogelijk met gebruikers acties in het beleid voor voorwaardelijke toegang. Deze preview-functie is beschikbaar voor organisaties die de [gecombineerde registratie preview](../authentication/concept-registration-mfa-sspr-combined.md)hebben ingeschakeld. Deze functionaliteit kan worden ingeschakeld in organisaties waar gebruikers moeten registreren voor Azure Multi-Factor Authentication en SSPR vanaf een centrale locatie, zoals een vertrouwde netwerk locatie tijdens het uitvoeren van een HR-systeem. Zie het artikel [Wat is de locatie voorwaarde in azure Active Directory voorwaardelijke toegang?](../conditional-access/location-condition.md#named-locations) voor meer informatie over het maken van vertrouwde locaties in voorwaardelijke toegang.

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Een beleid maken om registratie van een vertrouwde locatie te vereisen

Het volgende beleid is van toepassing op alle geselecteerde gebruikers die zich willen registreren met de gecombineerde registratie-ervaring en blokkeert de toegang tenzij ze verbinding maken vanaf een locatie die is gemarkeerd als vertrouwd netwerk.

![Een CA-beleid maken voor het beheren van de registratie van beveiligings gegevens](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Ga in het **Azure Portal**naar **Azure Active Directory** > **beveiliging** > **voorwaardelijke toegang**
1. Selecteer **Nieuw beleid**.
1. Voer bij naam een naam in voor dit beleid. Bijvoorbeeld **registratie van gecombineerde beveiligings gegevens op vertrouwde netwerken**
1. Klik onder **toewijzingen**op **gebruikers en groepen**en selecteer de gebruikers en groepen waarop u dit beleid wilt Toep assen

   > [!WARNING]
   > Gebruikers moeten zijn ingeschakeld voor de [gecombineerde registratie preview](../authentication/howto-registration-mfa-sspr-combined.md).

1. Selecteer onder **Cloud-apps of-acties** **gebruikers acties**, Controleer de **beveiligings gegevens registreren (preview-versie)**
1. Onder **voor waarden** > **locaties**
   1. **Ja** configureren
   1. **Een wille keurige locatie** toevoegen
   1. **Alle vertrouwde locaties** uitsluiten
   1. Klik op **gereed** op de Blade locaties
   1. Klik op **gereed** op de Blade voor waarden
1. Onder **Access controls** > **Grant**
   1. Klik op **toegang blok keren**
   1. Klik vervolgens op **selecteren**
1. Beleid **instellen op aan** **inschakelen**
1. Klik vervolgens op **maken**

## <a name="next-steps"></a>Volgende stappen

[Gebruikers dwingen om verificatie methoden opnieuw te registreren](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[Beschik bare methoden voor Multi-Factor Authentication en SSPR](concept-authentication-methods.md)

[Self-service voor wacht woord opnieuw instellen configureren](howto-sspr-deployment.md)

[Azure Multi-Factor Authentication configureren](howto-mfa-getstarted.md)

[Problemen met het registreren van gecombineerde beveiligings gegevens oplossen](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Wat is de voor waarde van de locatie in Azure Active Directory voorwaardelijke toegang?](../conditional-access/location-condition.md)
