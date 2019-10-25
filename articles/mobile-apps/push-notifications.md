---
title: Het belang van push meldingen in uw mobiele apps met Visual Studio App Center en Azure Notification Hubs
description: Meer informatie over de services zoals App Center waarmee u uw mobiele toepassings gebruikers kunt samen werken.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 05a9bc6ccd26aaecd3e0bf615880e0543b8604f7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795911"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Neem contact op met uw toepassings gebruikers door push meldingen te verzenden 

Of u nu consumenten-of bedrijfs toepassingen bouwt, u wilt dat gebruikers uw toepassing actief gebruiken. Er zijn vaak momenten waarop u het aantal nieuws, game updates, interessante aanbiedingen, product updates of relevante informatie met uw gebruikers wilt delen. Als u de betrokkenheid van uw gebruikers wilt verg Roten en deze wilt terugkrijgen naar uw toepassing, hebt u een manier om u te laten communiceren met uw gebruikers in real-time.

Push meldingen bieden een snelle en efficiënte manier om te communiceren met uw toepassings gebruikers. U kunt uw gebruikers op het juiste moment bereiken en op de hoogte stellen van de gewenste informatie, meestal in een pop-up of dialoog venster op een mobiel apparaat, ongeacht wat ze doen.

## <a name="value-of-push-notifications"></a>Waarde van push meldingen
Push meldingen bieden een waarde voor zowel eind gebruikers als het bedrijf.

Bedrijven kunnen:
- **Communiceer rechtstreeks met hun gebruikers** door hen op het juiste moment berichten te sturen op het ondersteunde platform.
- **Verbeter de gebruikers betrokkenheid** door realtime-updates en herinneringen naar uw gebruikers te sturen, zodat ze hun toepassing op regel matige basis kunnen benaderen.
- **Gebruikers behouden** en zich opnieuw bezig houden met inactieve gebruikers die de toepassing hebben gedownload, maar deze niet gebruiken om opnieuw actief te worden.
- **Verhoog de conversie snelheid** door het gedrag van eind gebruikers te analyseren, eind gebruikers te segmenteren en campagnes te gebruiken op basis van mobiele push meldingen.
- **Promoot producten en services** door push berichten en tijdige updates naar de gebruikers te verzenden.
- **Richt de gebruikers** in door hen persoonlijke push berichten te sturen.

Voor toepassings gebruikers, Push meldingen:
- **Geef in realtime waarden en informatie** op.
- **Herinner gebruikers** om de toepassing te gebruiken.

Gebruik de volgende services om Push meldingen in te scha kelen in uw mobiele apps.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
Met [app Center push](/appcenter/push/) service kunt u zich richten op uw gebruikers door hen gerichte berichten te sturen naar IOS-, Android-en Windows-gebruikers zonder dat ze het proces van het verzenden van meldingen naar apparaten met Push Notification Services (PNS) hoeven te beheren. Deze service is gebouwd op basis van Azure Notification Hubs en elimineert complexer voor het hand matig pushen van meldingen door een krachtig dash board te bieden.

**Belangrijkste functies**
- **Verzend push meldingen naar mobiele apparaten** op verschillende platforms.
- Gebruik meldingen om gegevens te verzenden naar een toepassing, een bericht weer te geven voor de gebruiker of een actie te activeren door de toepassing.
- Meldings doelen: 
    - Zend berichten verzenden naar **alle geregistreerde apparaten**.
    - Verzend meldingen naar de **doel groepen** op basis van apparaatgegevens en aangepaste eigenschappen.
    - Meldingen verzenden naar **specifieke gebruikers**.
    - Meldingen verzenden naar **specifieke apparaten**.
- **Uitgebreide telemetrie** op push, apparaat, fout is beschikbaar in app Center Portal.
- **Platform ondersteuning** : IOS, Android, MacOS, Xamarin, native, unit, Cordova.

**Referentie**
- [Meld u aan met App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Aan de slag met App Center push](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure Notification Hubs
[Notification hubs](/azure/notification-hubs/notification-hubs-push-notification-overview) bieden een gebruiks vriendelijke en uitgeschaalde push engine waarmee u meldingen kunt verzenden naar elk platform en elke back-end (Cloud of on-premises).

**Belangrijkste functies**
- **Verzend push meldingen** naar elk platform vanuit elke back-end, in de Cloud of on-premises.
- **Snel broadcasten** pushen naar miljoenen mobiele apparaten met één API-aanroep.
- Push meldingen aanpassen op basis van klant, taal en locatie.
- Definieer en meld **klant segmenten**dynamisch.
- **Schaal direct** naar miljoenen mobiele apparaten.
- **Platform ondersteuning** : IOS, Android, Windows, Kindle, Baidu.
        
**Referentie**
- [Azure-portal](https://portal.azure.com) 
- [Aan de slag met Azure Notification Hubs](/azure/notification-hubs/)   
- [Snelstartgidsen](/azure/notification-hubs/create-notification-hub-portal)
- [Voorbeelden](/azure/notification-hubs/samples)
