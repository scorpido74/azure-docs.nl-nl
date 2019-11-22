---
title: Verificatie gegevens registreren om uw eigen wacht woord opnieuw in te stellen-Azure AD
description: Registreer de gegevens van de verificatie methode voor de selfservice voor wachtwoord herstel van Azure AD, zodat u uw eigen wacht woord opnieuw kunt instellen zonder de hulp van de beheerder.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9748c0d04f958648f7919b7ac29dc58d5a137ac
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279058"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Uw informatie over de verificatie methode registreren om uw eigen wacht woord opnieuw in te stellen

> [!IMPORTANT]
> Bent u hier omdat u zich niet kunt aanmelden? Als dit het geval is, raadpleegt [u het wacht woord voor uw werk of school opnieuw instellen](active-directory-passwords-update-your-own-password.md).

Als eind gebruiker kunt u uw wacht woord opnieuw instellen of uw account ontgrendelen met behulp van Azure Active Directory (Azure AD) self-service voor wachtwoord herstel (SSPR). Voordat u deze functie kunt gebruiken, moet u uw verificatie methoden registreren of de vooraf gedefinieerde verificatie methoden bevestigen die uw beheerder heeft ingevuld.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Verificatiegegevens registreren of bevestigen met SSPR

1. Open de webbrowser op het apparaat en ga naar de [registratie pagina voor het opnieuw instellen van het wacht woord](https://aka.ms/ssprsetup).
2. Voer uw gebruikers naam en het wacht woord in dat door de beheerder is opgegeven.
3. Afhankelijk van de manier waarop uw IT-personeel dingen heeft geconfigureerd, zijn een of meer van de volgende opties beschikbaar voor u configureren en verifiëren. Als uw beheerder toestemming heeft om uw gegevens te gebruiken, kunnen ze een deel van de gegevens voor u invullen.
    * **Office Phone**: alleen de beheerder kan deze optie instellen.
    * **Telefoon voor authenticatie**: Stel deze optie in op een ander telefoon nummer waartoe u toegang hebt. Een voor beeld is een mobiele telefoon die een tekst of een aanroep kan ontvangen.
    * **Verificatie-e-mail**: Stel deze optie in op een alternatief e-mail adres dat u kunt openen zonder het wacht woord te gebruiken dat u wilt herstellen.
    * **Beveiligings vragen**: uw beheerder heeft deze lijst met vragen goedgekeurd die u kunt beantwoorden. U kunt dezelfde vraag of hetzelfde antwoord niet meer dan één keer gebruiken.
4. Geef en controleer de informatie die de beheerder nodig heeft. Als er meer dan één optie beschikbaar is, wordt u aangeraden meerdere methoden te registreren. Dit geeft u flexibiliteit wanneer een van de methoden niet beschikbaar is. Een voor beeld is wanneer u onderweg bent en u geen toegang hebt tot uw zakelijke telefoon.

    ![Registreer verificatie methoden en selecteer volt ooien][Register]

5. Selecteer **volt ooien**. U kunt SSPR nu gebruiken wanneer u in de toekomst wilt.

Als u gegevens voor **verificatie telefoon** of **verificatie-e-mail**opgeeft, wordt deze niet weer gegeven in de algemene map. De enige personen die deze gegevens kunnen zien, zijn u en uw beheerder. Alleen u kunt de antwoorden op uw beveiligings vragen zien.

Uw beheerders moeten mogelijk uw verificatie methoden na een bepaalde tijd bevestigen om ervoor te zorgen dat u nog steeds de juiste methoden hebt geregistreerd.

## <a name="common-problems-and-their-solutions"></a>Veelvoorkomende problemen en hun oplossingen

 Hier volgen enkele veelvoorkomende fout gevallen en hun oplossingen:

| Fout Case| Welke fout ziet u?| Oplossing |
| --- | --- | --- |
| Ik krijg de pagina ' Neem contact op met de beheerder ' nadat ik mijn gebruikers-ID heb ingevoerd | Neem contact op met de beheerder. <br> <br> Het wacht woord voor uw gebruikers account wordt niet beheerd door micro soft. Als gevolg hiervan kunnen we uw wacht woord niet automatisch opnieuw instellen. <br> <br> Neem contact op met uw IT-mede werkers voor verdere hulp. | U ziet dit bericht omdat uw IT-mede werkers uw wacht woord in uw on-premises omgeving beheren en u niet toestaat uw wacht woord opnieuw in te stellen via de koppeling **geen toegang tot uw account** . <br> <br> Als u uw wacht woord opnieuw wilt instellen, neemt u rechtstreeks contact op met uw IT-mede werkers. Laat ze weten dat u uw wacht woord opnieuw wilt instellen zodat deze functie voor u kan worden ingeschakeld.|
| De fout ' uw account is niet ingeschakeld voor wachtwoord herstel ' verschijnt na het invoeren van mijn gebruikers-ID | Het account is niet ingeschakeld voor het opnieuw instellen van het wacht woord. <br> <br> Uw IT-afdeling heeft uw account niet ingesteld voor gebruik met deze service. <br> <br> Als u wilt, kunnen we contact opnemen met een beheerder in uw organisatie om uw wacht woord opnieuw in te stellen. | U ziet dit bericht omdat uw IT-afdeling het wacht woord niet heeft ingeschakeld voor uw organisatie via de koppeling **geen toegang tot uw account** of een licentie heeft gekregen om de functie te gebruiken. <br> <br> Als u uw wacht woord opnieuw wilt instellen, selecteert u de koppeling **contact opnemen met de beheerder** . Er wordt een e-mail bericht verzonden naar de IT-afdeling van uw bedrijf. Het e-mail bericht laat u weten dat u uw wacht woord opnieuw wilt instellen, zodat deze functie voor u kan worden ingeschakeld. |
| De fout ' uw account is niet geverifieerd ' verschijnt na het invoeren van mijn gebruikers-ID | Uw account kan niet worden geverifieerd. <br> <br> Als u wilt, kunnen we contact opnemen met een beheerder in uw organisatie om uw wacht woord opnieuw in te stellen. | U ziet dit bericht omdat u bent ingeschakeld voor wachtwoord herstel, maar u bent niet geregistreerd voor het gebruik van de service. Als u zich wilt registreren voor het opnieuw instellen van wacht woorden, gaat u naar de [registratie pagina voor het opnieuw instellen van het wacht woord](https://aka.ms/ssprsetup) nadat u weer toegang hebt gekregen tot uw account. <br> <br> Als u uw wacht woord opnieuw wilt instellen, selecteert u de koppeling **contact opnemen** met de beheerder om een e-mail te verzenden naar de IT-afdeling van uw bedrijf. |

## <a name="next-steps"></a>Volgende stappen

* [Uw wacht woord wijzigen met selfservice voor wachtwoord herstel](active-directory-passwords-update-your-own-password.md)
* [Registratiepagina voor het opnieuw instellen van een wachtwoord](https://aka.ms/ssprsetup)
* [Portal voor het opnieuw instellen van een wachtwoord](https://passwordreset.microsoftonline.com/)
* [Wanneer u zich niet kunt aanmelden bij uw Microsoft-account](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "De registratie pagina voor het opnieuw instellen van wacht woorden met de geregistreerde methoden en de knop volt ooien"

