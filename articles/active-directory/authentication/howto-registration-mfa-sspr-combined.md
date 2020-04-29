---
title: Aan de slag met gecombineerde registratie-Azure Active Directory
description: Gecombineerde Azure AD Multi-Factor Authentication en self-service voor wachtwoord herstel inschakelen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 466b063253ee49ab58c2685f359b4bb8a4079532
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81639680"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Registratie van gecombineerde beveiligings gegevens inschakelen in Azure Active Directory

Vóór de gecombineerde registratie hebben gebruikers de verificatie methoden voor Azure Multi-Factor Authentication en self-service voor wachtwoord herstel (SSPR) afzonderlijk geregistreerd. Het is bekend dat er soort gelijke methoden werden gebruikt voor Multi-Factor Authentication en SSPR, maar dat ze voor beide functies moesten registreren. Met gecombineerde registratie kunnen gebruikers zich eenmaal registreren en profiteren van de voor delen van Multi-Factor Authentication en SSPR.

Voordat u de nieuwe ervaring inschakelt, raadpleegt u het artikel [registratie van gecombineerde beveiligings gegevens](concept-registration-mfa-sspr-combined.md) om ervoor te zorgen dat u de functionaliteit en gevolgen van deze functie begrijpt.

![Verbeterde ervaring bij het registreren van gecombineerde beveiligings gegevens](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Gecombineerde registratie inschakelen

Voer de volgende stappen uit om gecombineerde registratie in te scha kelen:

1. Meld u aan bij de Azure Portal als gebruikers beheerder of globale beheerder.
2. Ga naar **Azure Active Directory** > **gebruikers instellingen** > **instellingen voor de preview-versie van gebruikers onderdelen beheren**.
3. Onder **gebruikers kunnen preview-functies gebruiken voor het registreren en beheren van beveiligings gegevens**, kiezen om in te scha kelen voor een **geselecteerde** groep gebruikers of voor **alle** gebruikers.

   ![De preview-ervaring voor gecombineerde beveiligings info voor alle gebruikers inschakelen](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!NOTE]
> Nadat u gecombineerde registratie hebt ingeschakeld, kunnen gebruikers die hun telefoon nummer of mobiele app via de nieuwe ervaring registreren of bevestigen, deze gebruiken voor Multi-Factor Authentication en SSPR als deze methoden zijn ingeschakeld in het beleid voor Multi-Factor Authentication en SSPR. Als u deze ervaring vervolgens uitschakelt, moeten gebruikers die naar de vorige registratie pagina `https://aka.ms/ssprsetup` van de SSPR gaan, multi-factor Authentication uitvoeren voordat ze toegang kunnen krijgen tot de pagina.

Als u de lijst site toewijzing met zone hebt geconfigureerd in Internet Explorer, moeten de volgende sites zich in dezelfde zone bekomen:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Beleid voor voorwaardelijke toegang voor gecombineerde registratie

Beveiligen wanneer en hoe gebruikers zich registreren voor Azure Multi-Factor Authentication en self-service voor wacht woord opnieuw instellen is nu mogelijk met gebruikers acties in het beleid voor voorwaardelijke toegang. Deze functie is beschikbaar voor organisaties die de functie voor [gecombineerde registratie](../authentication/concept-registration-mfa-sspr-combined.md)hebben ingeschakeld. Deze functionaliteit kan worden ingeschakeld in organisaties waar gebruikers moeten registreren voor Azure Multi-Factor Authentication en SSPR vanaf een centrale locatie, zoals een vertrouwde netwerk locatie tijdens het uitvoeren van een HR-systeem.

Zie het artikel [Wat is de locatie voorwaarde in azure Active Directory voorwaardelijke toegang?](../conditional-access/location-condition.md#named-locations) voor meer informatie over het maken van vertrouwde locaties in voorwaardelijke toegang.

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Een beleid maken om registratie van een vertrouwde locatie te vereisen

Het volgende beleid is van toepassing op alle geselecteerde gebruikers die zich willen registreren met de gecombineerde registratie-ervaring en blokkeert de toegang tenzij ze verbinding maken vanaf een locatie die is gemarkeerd als vertrouwd netwerk.

1. Blader in het **Azure Portal**naar **Azure Active Directory** > **voorwaardelijke toegang** voor**beveiliging** > 
1. Selecteer **+ Nieuw beleid**
1. Voer een naam in voor dit beleid, zoals de *registratie van gecombineerde beveiligings gegevens op vertrouwde netwerken*.
1. Onder **Toewijzingen** selecteert u **Gebruikers en groepen**. Kies de gebruikers en groepen waarop u dit beleid wilt Toep assen en selecteer vervolgens **gereed**.

   > [!WARNING]
   > Gebruikers moeten zijn ingeschakeld voor gecombineerde registratie.

1. Selecteer onder **Cloud-apps of acties**de optie **gebruikers acties**. Controleer **beveiligings gegevens registreren**en selecteer vervolgens **gereed**.

    ![Een beleid voor voorwaardelijke toegang maken om de registratie van beveiligings gegevens te beheren](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Configureer onder **voor waarden** > **locaties**de volgende opties:
   1. **Ja** configureren
   1. **Een wille keurige locatie** toevoegen
   1. **Alle vertrouwde locaties** uitsluiten
1. Selecteer **gereed** in het venster *locaties* en selecteer vervolgens **gereed** in het venster *voor waarden* .
1. Onder **toegangs beheer** > **toekennen**kiest u **toegang blok keren**en **selecteert** u vervolgens
1. Beleid **instellen op aan** **inschakelen**
1. Selecteer **maken** om het beleid te volt ooien

## <a name="next-steps"></a>Volgende stappen

Als u hulp nodig hebt, raadpleegt u de [registratie van gecombineerde beveiligings gegevens oplossen](howto-registration-mfa-sspr-combined-troubleshoot.md) of leest u [Wat is de voor waarde voor de locatie in azure Active Directory voorwaardelijke toegang?](../conditional-access/location-condition.md)

Als u de functies in uw Azure AD-Tenant wilt inschakelen, raadpleegt u de zelf studies om [selfservice voor wachtwoord herstel in te scha kelen](tutorial-enable-sspr.md) en [Azure multi-factor Authentication in te scha kelen](tutorial-enable-azure-mfa.md).

Meer informatie over het [inschakelen van gecombineerde registratie in uw Tenant of het](howto-registration-mfa-sspr-combined.md) [afdwingen van verificatie methoden door gebruikers te forceren](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

U kunt ook de [beschik bare methoden voor Azure multi-factor Authentication en SSPR](concept-authentication-methods.md)bekijken.
