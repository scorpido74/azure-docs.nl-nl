---
title: Uw recente aanmeldingsactiviteit weergeven en doorzoeken vanaf de pagina Mijn aanmelding (voorbeeld) - Azure Active Directory | Microsoft Documenten
description: Details over het bekijken en doorzoeken van uw recente aanmeldingsactiviteit op de pagina Mijn aanmeldingen van de portal Mijn account.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: b68e7b517ddaa9b2aaef00cf87d5b6e63871654b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064016"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>Uw recente aanmeldingsactiviteit weergeven en doorzoeken op de pagina Mijn aanmeldingen (voorbeeld)

Je al je recente aanmeldingsactiviteit voor werk of schoolaccount bekijken op de pagina **Mijn aanmeldingen** van de mijn **accountportal.** Als u uw aanmeldingsgeschiedenis bekijkt, u controleren op ongebruikelijke activiteiten door u te helpen het overzicht te krijgen:

- Als iemand probeert uw wachtwoord te raden.

- Als een aanvaller zich heeft aangemeld bij uw account en vanaf welke locatie.

- Welke apps de aanvaller probeerde te openen.

## <a name="view-your-recent-sign-in-activity"></a>Uw recente aanmeldingsactiviteiten bekijken

1. Meld u aan bij uw werk- https://myprofile.microsoft.com/ of schoolaccount en ga vervolgens naar uw pagina.

2. Selecteer **Mijn aanmelding (voorbeeld)** in het linkernavigatiedeelvenster of selecteer de koppeling **Recente activiteit controleren** in het blok Mijn **aanmeldingen (voorbeeld).**

    ![Pagina Mijn account, met gemarkeerde recente activiteitskoppelingen](media/my-account-portal/my-account-portal-sign-ins.png)

3. Vouw en bekijk elk van de aanmeldingsitems, zodat u ze allemaal herkent. Als u een aanmeldingsitem vindt dat er niet bekend uitziet, raden we u ten zeerste aan uw wachtwoord te wijzigen om uw account te beschermen als het is gecompromitteerd.

    ![Recente activiteitenpagina met uitgebreide aanmeldingsgegevens](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>Als u een aangemeld voor geslaagden ziet

Je moet je eigen activiteit als normaal herkennen. Als u echter een succesvolle aanmelding ziet vanaf een vreemde locatie, browser of besturingssysteem, kan dit betekenen dat een aanvaller toegang heeft gekregen tot uw account. In deze situatie raden we u aan uw wachtwoord onmiddellijk te wijzigen en vervolgens naar de pagina [Beveiligingsgegevens](https://mysignins.microsoft.com/security-info) te gaan om uw beveiligingsinstellingen bij te werken.

Voordat u vaststelt dat iets onjuist is, moet u ervoor zorgen dat u geen vals-positief ziet (waarbij het item er twijfelachtig uitziet, maar in orde is). We bepalen bijvoorbeeld uw locatie en kaart bij benadering op basis van uw IP-adres. Mobiele netwerken zijn vooral moeilijk te lokaliseren, omdat ze soms het verkeer route door verre locaties. Dus als u zich hebt aangemeld met uw mobiele apparaat in de staat Washington, kan de locatie de aanmelding vanuit Californië weergeven. Daarom raden we u ten zeerste aan om meer details te controleren, behalve alleen de locatie. U moet er ook voor zorgen dat het besturingssysteem, de browser en de app ook allemaal zinvol zijn.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Als u een niet-geslaagde aanmelding ziet

Een mislukte aanmelding zonder sessieactiviteit betekent dat uw primaire verificatiemethode (gebruikersnaam/wachtwoord) is mislukt. Dit kan betekenen dat u uw gebruikersnaam of wachtwoord verkeerd hebt getypt, maar het kan ook betekenen dat een aanvaller uw wachtwoord probeerde te raden. Als u denkt dat het een aanvaller was die tevergeefs uw wachtwoord probeerde te raden, hoeft u uw wachtwoord niet te wijzigen, maar we raden u ten zeerste aan zich te registreren voor Azure Multi-Factor Authentication (MFA). Met MFA, zelfs als de hacker uiteindelijk uw wachtwoord raadt, zal het niet genoeg zijn om toegang te krijgen tot uw account.

Als u een mislukte aanmelding ziet, met een notitie onder Sessieactiviteit met de tekst **Extra verificatie, ongeldige code,** betekent dit dat uw primaire verificatie (gebruikersnaam/wachtwoord) is geslaagd, maar MFA is mislukt. Als dit een aanvaller was, hebben ze uw wachtwoord correct geraden, maar konden ze de MFA-uitdaging nog steeds niet doorstaan. In dit geval raden we u aan uw wachtwoord nog steeds te wijzigen, omdat de aanvaller dat onderdeel goed heeft, en vervolgens naar de pagina [Beveiligingsgegevens](https://mysignins.microsoft.com/security-info) gaat om uw beveiligingsinstellingen bij te werken.

## <a name="search-for-specific-sign-in-activity"></a>Zoeken naar specifieke aanmeldingsactiviteit

U uw recente aanmeldingsactiviteit doorzoeken op een van de beschikbare informatie. U bijvoorbeeld zoeken naar uw recente aanmeldingsactiviteit op besturingssysteem, locatie, app, enzovoort.

1. Typ **op** de pagina Recente activiteit controleren de informatie die u wilt zoeken in de **zoekbalk.** Typ bijvoorbeeld `My Account` om te zoeken naar alle activiteiten die worden verzameld door de app Mijn account.

2. Selecteer de knop **Zoeken** om te beginnen met zoeken.

    ![Recente activiteitspagina met gemarkeerde zoekbalk, zoekknop en resultaten](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u uw recente aanmeldingsactiviteit hebt bekeken, u het nieuwe programma:

- Uw [beveiligingsgegevens](user-help-security-info-overview.md)weergeven of beheren.

- Uw verbonden [apparaten](my-account-portal-devices-page.md)weergeven of beheren.

- Uw [organisaties](my-account-portal-organizations-page.md)weergeven of beheren.

- Bekijk hoe uw organisatie [uw privacygerelateerde gegevens gebruikt.](my-account-portal-privacy-page.md)
