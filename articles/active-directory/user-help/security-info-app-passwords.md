---
title: App-wachtwoorden maken op de pagina Beveiligingsgegevens (voorbeeld) - Azure AD
description: Maak automatisch gegenereerde wachtwoorden (app-wachtwoorden) die u gebruiken met een app zonder browser of met een app die geen tweestapsverificatie in uw organisatie ondersteunt. Dit app-wachtwoord staat los van een normaal wachtwoord en kan worden ingesteld vanaf de pagina Beveiligingsgegevens.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2018
ms.author: curtand
ms.openlocfilehash: 787fa67ee77997fd1f9967db3abdbfc83d4ffad2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064067"
---
# <a name="create-app-passwords-from-the-security-info-preview-page"></a>App-wachtwoorden maken op de pagina Beveiligingsgegevens (voorbeeld)

Bepaalde apps, zoals Outlook 2010, bieden geen ondersteuning voor verificatie in twee stappen. Dit gebrek aan ondersteuning betekent dat als u verificatie in twee stappen in uw organisatie gebruikt, de app niet werkt. Om dit probleem te omzeilen, u een automatisch gegenereerd wachtwoord maken dat u met elke niet-browser-app gebruiken, gescheiden van uw normale wachtwoord.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>Uw beheerder staat u mogelijk niet toe app-wachtwoorden te gebruiken. Als u **App-wachtwoorden** niet als een optie ziet, zijn ze niet beschikbaar in uw organisatie.

Bij het gebruik van app-wachtwoorden is het belangrijk om te onthouden:

- App-wachtwoorden worden automatisch gegenereerd en moeten eenmaal per app worden gemaakt en ingevoerd.

- Er is een limiet van 40 wachtwoorden per gebruiker. Als u na die limiet een wachtwoord probeert te maken, wordt u gevraagd een bestaand wachtwoord te verwijderen voordat u het nieuwe wachtwoord mag maken.

    >[!Note]
    >Office 2013-clients (inclusief Outlook) ondersteunen nieuwe verificatieprotocollen en kunnen worden gebruikt met verificatie in twee stappen. Deze ondersteuning betekent dat u na verificatie in twee stappen geen app-wachtwoorden meer nodig hebt voor Office 2013-clients. Zie het artikel [Hoe moderne verificatie werkt voor Office 2013 en Office 2016 clientapps voor](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) meer informatie.

## <a name="create-new-app-passwords"></a>Nieuwe app-wachtwoorden maken

Als u verificatie in twee stappen gebruikt met uw werk- of schoolaccount en uw beheerder de beveiligingsgegevenservaring heeft ingeschakeld, u uw app-wachtwoorden maken en verwijderen via de pagina **Beveiligingsgegevens.**

>[!Note]
>Als uw beheerder de beveiligingsgegevenservaring niet heeft ingeschakeld, moet u de instructies en informatie in de sectie [App-wachtwoorden beheren voor verificatie in twee stappen](multi-factor-authentication-end-user-app-passwords.md) volgen.

### <a name="to-create-a-new-app-password"></a>Een nieuw app-wachtwoord maken

1. Meld u aan bij uw werk- https://myprofile.microsoft.com/ of schoolaccount en ga vervolgens naar uw pagina.

    ![Mijn profielpagina met gemarkeerde koppelingen naar beveiligingsgegevens](media/security-info/securityinfo-myprofile-page.png)

2. Selecteer **Beveiligingsgegevens** in het linkernavigatiedeelvenster of in de koppeling in het blok **Beveiligingsgegevens** en selecteer **vervolgens Methode toevoegen** op de pagina **Beveiligingsgegevens.**

    ![Pagina Beveiligingsgegevens met gemarkeerde optie Methode toevoegen](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Selecteer **op** de pagina Een methode toevoegen het **app-wachtwoord** in de vervolgkeuzelijst en selecteer **Vervolgens Toevoegen**.

    ![Het vakmethode toevoegen, met het wachtwoord app geselecteerd](media/security-info/securityinfo-myprofile-addpassword.png)

4. Typ de naam van de app waarvoor het app-wachtwoord vereist is en selecteer **Volgende**.

    ![App-wachtwoordpagina, met naam van de app](media/security-info/securityinfo-myprofile-password-appname.png)

5. Kopieer de tekst uit het vak **Wachtwoord,** plak het wachtwoord in het wachtwoordgebied van de app (in dit voorbeeld Outlook 2010) en selecteer **Gereed**.

    ![App-wachtwoordpagina, met naam van de app](media/security-info/securityinfo-myprofile-password-copytext.png)

    Het wachtwoord wordt toegevoegd en u zich in de toekomst met succes aanmelden bij uw app.

## <a name="delete-your-app-passwords"></a>Uw app-wachtwoorden verwijderen

Als u een app waarvoor een app-wachtwoord vereist is, niet langer hoeft te worden gebruikt, u het bijbehorende app-wachtwoord verwijderen. Als u het wachtwoord van de app verwijderd, wordt een van de beschikbare app-wachtwoordplekken vrijgemaakt voor gebruik in de toekomst.

>[!Important]
>Als u een app-wachtwoord per ongeluk verwijdert, u dit niet ongedaan maken. U moet een nieuw app-wachtwoord maken en het opnieuw invoeren in de app, volgens de stappen in het gedeelte [Nieuwe app-wachtwoorden maken](#create-new-app-passwords) van dit artikel.

### <a name="to-delete-an-app-password"></a>Een app-wachtwoord verwijderen

1. Selecteer op de pagina **Beveiligingsgegevens** de koppeling **Verwijderen** naast de optie **App-wachtwoord** voor de specifieke app.

    ![Koppeling om de wachtwoordmethode van de app uit beveiligingsgegevens te verwijderen](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. Selecteer **Ja** in het bevestigingsvak om het **app-wachtwoord**te verwijderen. Nadat het app-wachtwoord is verwijderd, wordt het verwijderd uit uw beveiligingsgegevens en verdwijnt het van de pagina **Beveiligingsgegevens.**

## <a name="for-more-information"></a>Voor meer informatie

- Zie [Overzicht van beveiligingsgegevens](user-help-security-info-overview.md) voor meer informatie over de pagina **Beveiligingsgegevens** en hoe u deze instellen
