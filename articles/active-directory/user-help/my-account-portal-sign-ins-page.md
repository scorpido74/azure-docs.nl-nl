---
title: Uw recente aanmeldings activiteit bekijken en doorzoeken vanaf de pagina mijn aanmelding (preview)-Azure Active Directory | Microsoft Docs
description: Meer informatie over het weer geven en doorzoeken van uw recente aanmeldings activiteit vanaf de pagina Mijn aanmeldingen van de portal mijn account.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: 0b3b0d686ae10f9b376c977bf165eccddda32239
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83744516"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>Uw recente aanmeldings activiteit bekijken en doorzoeken via de pagina Mijn aanmeldingen (preview)

U kunt al uw recente aanmeldings activiteiten voor werk-of school accounts bekijken op de pagina **mijn aanmeldingen** van de portal **Mijn account** . Door uw aanmeldings geschiedenis te bekijken, kunt u controleren op ongebruikelijke activiteiten door u te helpen bij het volgende:

- Als iemand probeert uw wacht woord te raden.

- Als een aanvaller zich heeft aangemeld bij uw account en vanaf welke locatie.

- Welke apps de aanvaller probeerde te openen.

## <a name="view-your-recent-sign-in-activity"></a>Uw recente aanmeldingsactiviteiten bekijken

1. Meld u aan bij uw werk-of school account en ga vervolgens naar de https://myaccount.microsoft.com/ pagina.

2. Selecteer **mijn aanmeldingen (preview)** in het navigatie deel venster links of selecteer de koppeling **recente activiteit controleren** van het blok **mijn aanmeldingen (preview)** .

    ![Pagina Mijn account, met gemarkeerde recente koppelingen met activiteiten](media/my-account-portal/my-account-portal-sign-ins.png)

3. Vouw en Controleer elk van de aanmeldings items en zorg ervoor dat u ze herkent. Als u een aanmeldings item vindt dat niet bekend is, raden wij u ten zeerste aan uw wacht woord te wijzigen om uw account te beschermen als dit is aangetast.

    ![Pagina recente activiteiten met uitgebreide aanmeldings Details](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>Als een geslaagde aanmelding wordt weer geven

U moet uw eigen activiteit herkennen als normaal. Als u echter een geslaagde aanmelding van een vreemde locatie, browser of besturings systeem ziet, kan dit betekenen dat een aanvaller toegang tot uw account heeft verkregen. In deze situatie raden wij u aan om uw wacht woord onmiddellijk te wijzigen en gaat u vervolgens naar de pagina met [beveiligings gegevens](https://mysignins.microsoft.com/security-info) om uw beveiligings instellingen bij te werken.

Voordat u opgeeft dat er iets onjuist is, controleert u of er geen onechte positieve is (waarbij het item wel vraagbaar is). We bepalen bijvoorbeeld uw geschatte locatie en kaart op basis van uw IP-adres. Mobiele netwerken zijn met name moeilijk te lokaliseren, omdat ze het verkeer soms via locaties op afstand routeren. Als u zich dus hebt aangemeld met uw mobiele apparaat in de staat Washington, kan de locatie de aanmelding weer geven die afkomstig is van Californië. Daarom is het raadzaam om meer details dan alleen de locatie te controleren. Zorg er ook voor dat het besturings systeem, de browser en de app allemaal zinvol zijn.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Als er een mislukte aanmelding wordt weer geven

Een mislukte aanmelding, zonder sessie activiteit, betekent dat uw primaire verificatie methode (gebruikers naam/wacht woord) is mislukt. Dit kan betekenen dat u uw gebruikers naam of wacht woord hebt getypt, maar dit kan ook betekenen dat een aanvaller uw wacht woord wil raden. Als u denkt dat een aanvaller het wacht woord niet succesvol zou proberen te raden, hoeft u uw wacht woord niet te wijzigen, maar we raden u aan om u te registreren voor Azure Multi-Factor Authentication (MFA). Met MFA, zelfs als de hacker uw wacht woord uiteindelijk heeft geraden, is het niet voldoende om toegang te krijgen tot uw account.

Als er een niet-geslaagde aanmelding wordt weer geven, met een opmerking onder sessie activiteit met de melding dat er een **aanvullende verificatie is mislukt, ongeldige code**, betekent dit dat uw primaire authenticatie (gebruikers naam/wacht woord) is geslaagd, maar MFA is mislukt. Als dit een aanvaller is, raden ze uw wacht woord op de juiste wijze aan, maar kon de MFA-uitdaging nog steeds niet worden door gegeven. In dit geval wordt u aangeraden uw wacht woord nog steeds te wijzigen, omdat de aanvaller dat onderdeel recht heeft gekregen. Ga vervolgens naar de pagina met [beveiligings gegevens](https://mysignins.microsoft.com/security-info) om uw beveiligings instellingen bij te werken.

## <a name="search-for-specific-sign-in-activity"></a>Zoeken naar specifieke aanmeldings activiteit

U kunt uw recente aanmeldings activiteit doorzoeken op een van de beschik bare gegevens. U kunt bijvoorbeeld zoeken naar uw recente aanmeldings activiteit per besturings systeem, locatie, app, enzovoort.

1. Typ op de pagina **recente activiteit controleren** de informatie waarnaar u wilt zoeken in de **Zoek** balk. Typ bijvoorbeeld `My Account` om te zoeken naar alle activiteiten die worden verzameld door de app mijn account.

2. Selecteer de knop **zoeken** om te beginnen met zoeken.

    ![Pagina recente activiteiten, met de gemarkeerde zoek balk, zoek knop en resultaten](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u uw recente aanmeldings activiteit hebt bekeken, kunt u het volgende doen:

- Je [beveiligings gegevens](user-help-security-info-overview.md)weer geven of beheren.

- Uw verbonden [apparaten](my-account-portal-devices-page.md)weer geven of beheren.

- Uw [organisaties](my-account-portal-organizations-page.md)weer geven of beheren.

- Bekijk hoe uw organisatie [gebruikmaakt van uw privacy-gerelateerde gegevens](my-account-portal-privacy-page.md).
