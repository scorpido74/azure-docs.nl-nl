---
title: Verificatiegegevens registreren om uw eigen wachtwoord opnieuw in te stellen - Azure AD
description: Registreer de gegevens van uw verificatiemethode voor het opnieuw instellen van azure AD-zelfservicewachtwoorden, zodat u uw eigen wachtwoord opnieuw instellen zonder hulp van de beheerder.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 397e1fd7695fd7e74e1f22959d0f9f24af7d1ea3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062639"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Uw gegevens over de verificatiemethode registreren om uw eigen wachtwoord opnieuw in te stellen

Als u uw werk- of schoolwachtwoord bent vergeten, nooit een wachtwoord van uw organisatie hebt ontvangen of uw account bent geblokkeerd, u uw beveiligingsgegevens en uw mobiele apparaat gebruiken om uw werk- of schoolwachtwoord opnieuw in te stellen.

Uw beheerder moet deze functie inschakelen om uw gegevens te kunnen registreren en uw eigen wachtwoord opnieuw in te stellen. Als u de optie **Mijn wachtwoord vergeten** niet ziet, betekent dit dat uw beheerder de functie voor uw organisatie niet heeft ingeschakeld. Als u denkt dat dit onjuist is, neem dan contact op met uw helpdesk voor hulp.

>[!Important]
>Dit artikel is bedoeld voor gebruikers die zich aanmelden voor het opnieuw instellen van het zelfservicewachtwoord. Dit betekent dat u uw eigen werk- of schoolwachtwoord alain@contoso.com(zoals), resetten zonder de hulp van uw beheerder te behoeven. Zie de [selfservicereset van Azure AD voor selfservice-wachtwoord opnieuw](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment)instellen en andere artikelen als u informatie zoekt over het instellen van selfservicewachtwoorden voor uw werknemers of andere gebruikers.

## <a name="set-up-your-password-reset-verification-method"></a>Verificatiemethode voor het opnieuw instellen van wachtwoorden instellen

1. Open de webbrowser op uw apparaat en ga naar de [pagina met beveiligingsgegevens](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup).

2. Afhankelijk van hoe uw beheerder uw organisatie heeft ingesteld, zijn een of meer van de volgende opties beschikbaar voor u om in te stellen als uw beveiligingsverificatiemethode. Als er meerdere opties beschikbaar zijn, raden we u ten zeerste aan om meer dan één als beveiligingsverificatiemethode te gebruiken, voor het geval een van uw methoden niet meer beschikbaar is.

    - **Verificatie-app.** Kies ervoor om de Microsoft Authenticator-app of een andere authenticator-app te gebruiken als uw beveiligingsverificatiemethode. Zie [De Microsoft Authenticator-app instellen als verificatiemethode voor](security-info-setup-auth-app.md)meer informatie over het instellen van de app.

    - **Tekstberichten.** Kies ervoor om uzelf sms-berichten naar uw mobiele apparaat te sturen. Zie Sms instellen als verificatiemethode voor meer informatie over het instellen van [sms-berichten.](security-info-setup-text-msg.md)

    - **Telefoongesprekken.** Kies ervoor om een telefoontje te krijgen naar uw geregistreerde telefoonnummer. Zie Een telefoonnummer instellen als [verificatiemethode](security-info-setup-phone-number.md)voor meer informatie over het instellen van telefoongesprekken.

    - **Beveiligingssleutel.** Kies ervoor om een beveiligingssleutel die compatibel is met Microsoft te gebruiken. Zie [Een beveiligingssleutel instellen als verificatiemethode voor](security-info-setup-security-key.md)meer informatie.

    - **E-mailadres.** Kies ervoor om een alternatief e-mailadres te gebruiken dat kan worden gebruikt zonder dat u uw vergeten of ontbrekende wachtwoord nodig hebt. Dit werkt alleen voor het opnieuw instellen van wachtwoorden, niet als een beveiligingsverificatiemethode. Zie [Een e-mailadres instellen als verificatiemethode voor](security-info-setup-email.md)meer informatie over het instellen van een e-mailadres.

    - **Veiligheidsvragen.** Kies ervoor om vooraf gedefinieerde beveiligingsvragen in te stellen en te beantwoorden die door uw beheerder zijn ingesteld. Dit werkt alleen voor het opnieuw instellen van wachtwoorden, niet als een beveiligingsverificatiemethode. Zie [Beveiligingsvragen instellen als verificatiemethode voor](security-info-setup-questions.md)meer informatie over beveiligingsvragen.

3. Nadat u uw methoden hebt geselecteerd en ingesteld, kiest u **Voltooien** om het proces te voltooien.

    > [!Note]
    > Informatie die is toegevoegd voor uw telefoonnummer of e-mailadres wordt niet gedeeld met de globale directory van uw organisatie. De enige mensen die deze informatie kunnen zien, zijn u en uw beheerder. Alleen u de antwoorden op uw beveiligingsvragen zien.

## <a name="common-problems-and-their-solutions"></a>Veelvoorkomende problemen en oplossingen

 Hier zijn enkele veel voorkomende foutgevallen en hun oplossingen:

| Foutbericht |  Mogelijke oplossing |
| --- | --- | --- |
| Neem contact op met de beheerder.<br>We hebben ontdekt dat het wachtwoord van uw gebruikersaccount niet door Microsoft wordt beheerd. Hierdoor kunnen we uw wachtwoord niet automatisch opnieuw instellen.<br>Neem contact op met uw IT-medewerkers voor verdere hulp.| Als u dit foutbericht ontvangt nadat u uw gebruikersnaam hebt getypt, betekent dit dat uw organisatie uw wachtwoord intern beheert en niet wil dat u uw wachtwoord opnieuw instelt via de koppeling **Geen toegang tot uw account.** Als u uw wachtwoord in deze situatie opnieuw wilt instellen, moet u contact opnemen met de helpdesk van uw organisatie of uw beheerder voor hulp. |
| Uw account is niet ingeschakeld voor het opnieuw instellen van het wachtwoord.<br>Het spijt ons, maar uw IT-medewerkers hebben uw account niet ingesteld voor gebruik met deze service.<br>Als u wilt, kunnen we contact opnemen met een beheerder in uw organisatie om uw wachtwoord voor u opnieuw in te stellen. | Als u dit foutbericht ontvangt nadat u uw gebruikersnaam hebt getypt, betekent dit dat uw organisatie de functie voor het opnieuw instellen van het wachtwoord niet heeft ingeschakeld of dat u deze niet mag gebruiken. Als u uw wachtwoord in deze situatie opnieuw wilt instellen, moet u de koppeling **Contact opnemen met een beheerder** selecteren. Nadat u op de koppeling hebt geklikt, wordt er een e-mail verzonden naar de helpdesk of beheerder van uw organisatie, zodat ze weten dat u uw wachtwoord opnieuw wilt instellen. |
| We kunnen je account niet verifiëren.<br>Als u wilt, kunnen we contact opnemen met een beheerder in uw organisatie om uw wachtwoord voor u opnieuw in te stellen. | Als u dit foutbericht ontvangt nadat u uw gebruikersnaam hebt getypt, betekent dit dat uw organisatie een wachtwoordreset heeft ingeschakeld en dat u deze gebruiken, maar dat u zich niet hebt geregistreerd voor de service. In deze situatie moet u contact opnemen met de helpdesk of beheerder van uw organisatie om uw wachtwoord opnieuw in te stellen. Zie het bovenstaande proces in dit artikel voor informatie over het opnieuw instellen van wachtwoorden nadat u weer op uw apparaat bent. |

## <a name="next-steps"></a>Volgende stappen

- [Uw wachtwoord wijzigen door het opnieuw instellen van selfservicewachtwoorden te gebruiken](active-directory-passwords-update-your-own-password.md)

- [Pagina Beveiligingsgegevens](https://mysignins.microsoft.com/security-info)

- [Portal voor het opnieuw instellen van een wachtwoord](https://passwordreset.microsoftonline.com/)

- [Als u zich niet kunt aanmelden bij uw Microsoft-account](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
