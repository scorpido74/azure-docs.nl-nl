---
title: Microsoft Authenticator Beschik baarheid en beperkingen Foe Android in China | Microsoft Docs
description: Meer informatie over het verkrijgen van de Microsoft Authenticator app-beschik baarheid in China
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 05/06/2020
ms.author: curtand
ms.openlocfilehash: 3ba1b41859a543ab5e0393c5715dc49d452ea73c
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891835"
---
# <a name="microsoft-authenticator-for-android-in-the-public-cloud-in-china"></a>Microsoft Authenticator voor Android in de open bare Cloud in China

De Microsoft Authenticator-app voor Android kan worden gedownload in China. De Google Play Store is niet beschikbaar in China, dus u moet de app downloaden van andere Chinese app-markt plaatsen. De Microsoft Authenticator-app voor Android is momenteel beschikbaar in de volgende winkels in China:

- [Baidu](https://shouji.baidu.com/software/26638379.html)
- [Lenovo](https://www.lenovomm.com/appdetail/com.azure.authenticator/20197724)
- [Huawei](https://appgallery.cloud.huawei.com/uowap/index.html#/detailApp/C100262999?source=appshare&subsource=C100262999&shareTo=weixin&locale=zh_CN)
- [Samsung Galaxy Store](http://apps.samsung.com/appquery/appDetail.as?appId=com.azure.authenticator)

De meest recente build van de app bevindt zich in de Google Play Store, maar we updaten de app op alle andere app stores zo snel mogelijk. Omdat er geen aangepast Android-toepassings pakket (APK) is geïmplementeerd in een App Store, kan de app naadloos worden bijgewerkt vanaf een van de volgende locaties:

- Het archief dat is gedownload van
- De Google Play Store als de gebruiker regio's snijdt

## <a name="limitations"></a>Beperkingen

De Microsoft Authenticator-app voor Android maakt gebruik van Google Firebase Cloud Messa ging System en Google Play Services voor het ontvangen van push meldingen. Omdat geen van beide services beschikbaar is in China, zijn er enkele beperkingen ten aanzien van de functionaliteit van de app:

- Registratie van de verificator-app als een multi-factor Authentication-methode (MFA) met behulp van push meldingen werkt niet.

- [Aanmelding via de telefoon](../authentication/howto-authentication-sms-signin.md) kan niet worden ingesteld. Hiervoor moet de gebruiker de app instellen als een MFA-methode met push meldingen, die momenteel niet werken.

Als een gebruiker eerder heeft beheerd voor het instellen van een aanmelding via de telefoon of multi-factor Authentication met behulp van de app, kunnen ze een hand matige controle uitvoeren op meldingen aanvragen in de app en deze gebruiken voor identiteits verificatie.

## <a name="multi-factor-authentication-workaround"></a>Multi-factor Authentication-tijdelijke oplossing

In plaats van push meldingen voor multi-factor Authentication te gebruiken, kunnen gebruikers hun MFA instellen om verificatie codes op hun apparaat te ontvangen die ze kunnen gebruiken om hun identiteit te verifiëren. Deze verificatie codes zijn 30 seconden geldig en om ze te kunnen gebruiken, moeten beheerders hun tenants in staat stellen om verificatie uit te voeren met verificatie codes op basis van een tijd-wacht woord (mobiele TOTP).

## <a name="availability"></a>Beschikbaarheid

Microsoft Authenticator functie | Beschik baarheid in China
------------------------------- | ---------------------
MFA-registratie met push meldingen | Nee
Vooraf bestaand MFA-account identiteit verifiëren met push meldingen | Nee
Vooraf bestaand MFA-account dat hand matige controle uitvoert op meldingen | Ja
MFA-registratie/-authenticatie met alleen mobiele TOTP/verificatie codes | Ja
Registratie van de telefoon aanmelding | Nee
Bestaande aanmelding via de telefoon met push meldingen | Nee
Bestaande aanmeldings verificatie voor de telefoon door hand matige controle op verificatie aanvragen uit te voeren | Ja

## <a name="next-steps"></a>Volgende stappen

- [De Microsoft Authenticator-app downloaden en installeren](user-help-auth-app-download-install.md)
