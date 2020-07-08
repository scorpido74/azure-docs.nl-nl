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
ms.openlocfilehash: 7feb69b2ea53794b780a983ed8ab4ba5874ac022
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85260845"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Registratie van gecombineerde beveiligings gegevens inschakelen in Azure Active Directory

Vóór de gecombineerde registratie hebben gebruikers de verificatie methoden voor Azure Multi-Factor Authentication en self-service voor wachtwoord herstel (SSPR) afzonderlijk geregistreerd. Het is bekend dat er soort gelijke methoden werden gebruikt voor Azure Multi-Factor Authentication en SSPR, maar dat ze voor beide functies moesten registreren. Met gecombineerde registratie kunnen gebruikers zich eenmaal registreren en profiteren van de voor delen van Azure Multi-Factor Authentication en SSPR.

Voordat u de nieuwe ervaring inschakelt, raadpleegt u het artikel [registratie van gecombineerde beveiligings gegevens](concept-registration-mfa-sspr-combined.md) om ervoor te zorgen dat u de functionaliteit en gevolgen van deze functie begrijpt.

![Verbeterde ervaring bij het registreren van gecombineerde beveiligings gegevens](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Gecombineerde registratie inschakelen

Voer de volgende stappen uit om gecombineerde registratie in te scha kelen:

1. Meld u aan bij de Azure Portal als gebruikers beheerder of globale beheerder.
2. Ga naar **Azure Active Directory**  >  **gebruikers instellingen**  >  **instellingen voor de preview-versie van gebruikers onderdelen beheren**.
3. Onder **gebruikers kunnen de functie voor registratie van gecombineerde beveiligings gegevens worden gebruikt**. Kies deze optie om in te scha kelen voor een **geselecteerde** groep gebruikers of voor **alle** gebruikers.

   ![De gecombineerde beveiligings informatie-ervaring voor gebruikers inschakelen](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> Nadat u gecombineerde registratie hebt ingeschakeld, kunnen gebruikers die hun telefoon nummer of mobiele app via de nieuwe ervaring registreren of bevestigen, ze gebruiken voor Azure Multi-Factor Authentication en SSPR als deze methoden zijn ingeschakeld in de Azure Multi-Factor Authentication-en SSPR-beleid. Als u deze ervaring vervolgens uitschakelt, moeten gebruikers die naar de vorige registratie pagina van de SSPR gaan, `https://aka.ms/ssprsetup` multi-factor Authentication uitvoeren voordat ze toegang kunnen krijgen tot de pagina.

Als u de lijst site toewijzing met zone hebt geconfigureerd in Internet Explorer, moeten de volgende sites zich in dezelfde zone bekomen:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Beleid voor voorwaardelijke toegang voor gecombineerde registratie

Beveiligen wanneer en hoe gebruikers zich registreren voor Azure Multi-Factor Authentication en self-service voor wacht woord opnieuw instellen is nu mogelijk met gebruikers acties in het beleid voor voorwaardelijke toegang. Deze functie is beschikbaar voor organisaties die de functie voor [gecombineerde registratie](../authentication/concept-registration-mfa-sspr-combined.md)hebben ingeschakeld. Deze functionaliteit kan worden ingeschakeld in organisaties waar gebruikers moeten registreren voor Azure Multi-Factor Authentication en SSPR vanaf een centrale locatie, zoals een vertrouwde netwerk locatie tijdens het uitvoeren van een HR-systeem.

> [!NOTE]
> Dit beleid is alleen van toepassing wanneer een gebruiker toegang heeft tot een gecombineerde registratie pagina. Dit beleid dwingt geen MFA-inschrijving af wanneer een gebruiker toegang heeft tot andere toepassingen. U kunt een MFA-registratie beleid maken met behulp van [Azure Identity Protection-MFA-beleid configureren](../identity-protection/howto-identity-protection-configure-mfa-policy.md).

Zie het artikel [Wat is de locatie voorwaarde in azure Active Directory voorwaardelijke toegang?](../conditional-access/location-condition.md#named-locations) voor meer informatie over het maken van vertrouwde locaties in voorwaardelijke toegang.

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Een beleid maken om registratie van een vertrouwde locatie te vereisen

Het volgende beleid is van toepassing op alle geselecteerde gebruikers die zich willen registreren met de gecombineerde registratie-ervaring en blokkeert de toegang tenzij ze verbinding maken vanaf een locatie die is gemarkeerd als vertrouwd netwerk.

1. Blader in het **Azure Portal**naar **Azure Active Directory**  >  **Security**  >  **voorwaardelijke toegang**voor beveiliging.
1. Selecteer **+ Nieuw beleid**.
1. Voer een naam in voor dit beleid, zoals de *registratie van gecombineerde beveiligings gegevens op vertrouwde netwerken*.
1. Onder **Toewijzingen** selecteert u **Gebruikers en groepen**. Kies de gebruikers en groepen waarop u dit beleid wilt Toep assen en selecteer vervolgens **gereed**.

   > [!WARNING]
   > Gebruikers moeten zijn ingeschakeld voor gecombineerde registratie.

1. Selecteer onder **Cloud-apps of acties**de optie **gebruikers acties**. Controleer **beveiligings gegevens registreren**en selecteer vervolgens **gereed**.

    ![Een beleid voor voorwaardelijke toegang maken om de registratie van beveiligings gegevens te beheren](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Configureer onder **voor waarden**  >  **locaties**de volgende opties:
   1. Configureer **Ja**.
   1. **Een wille keurige locatie**bevatten.
   1. **Alle vertrouwde locaties**uitsluiten.
1. Selecteer **gereed** in het venster *locaties* en selecteer vervolgens **gereed** in het venster *voor waarden* .
1. Onder **toegangs beheer**  >  **toekennen**kiest u **toegang blok keren**en **selecteert**u vervolgens.
1. Stel **Beleid inschakelen** in op **Aan**.
1. Selecteer **maken**om het beleid te volt ooien.

## <a name="next-steps"></a>Volgende stappen

Als u hulp nodig hebt, raadpleegt u de [registratie van gecombineerde beveiligings gegevens oplossen](howto-registration-mfa-sspr-combined-troubleshoot.md) of leest u [Wat is de voor waarde voor de locatie in azure Active Directory voorwaardelijke toegang?](../conditional-access/location-condition.md)

Als u de functies in uw Azure AD-Tenant wilt inschakelen, raadpleegt u de zelf studies om [selfservice voor wachtwoord herstel in te scha kelen](tutorial-enable-sspr.md) en [Azure multi-factor Authentication in te scha kelen](tutorial-enable-azure-mfa.md).

Meer informatie over hoe u [gebruikers dwingt om verificatie methoden opnieuw te registreren](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

U kunt ook de [beschik bare methoden voor Azure multi-factor Authentication en SSPR](concept-authentication-methods.md)bekijken.
