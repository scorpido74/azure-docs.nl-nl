---
title: Aan de slag met Azure App Service Mobile Apps voor Xamarin.iOS-apps | Microsoft Docs
description: Volg deze zelfstudie om aan de slag te gaan met Mobile Apps voor Xamarin.iOS-ontwikkeling.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 26095757f8079f43e620e5f067f461f67c7042f9
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72024853"
---
# <a name="create-a-xamarinios-app"></a>Een Xamarin.iOS-app maken
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end en geïntegreerde services in de ontwikkeling van mobiele apps. Ontwikkel aars kunnen services **bouwen**, **testen** en **distribueren** om een continue integratie-en leverings pijplijn in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkel aars de status en het gebruik van hun app bewaken met behulp van de **analyse** -en **diagnose** Services en gebruikers benaderen met behulp van de **Push** service. Ontwikkel aars kunnen ook gebruikmaken van **auth** voor het verifiëren van hun gebruikers en **gegevens** service om app-gegevens in de Cloud op te slaan en te synchroniseren.
> Als u Cloud Services wilt integreren in uw mobiele toepassing, meldt u zich aan met App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) vandaag.

## <a name="overview"></a>Overzicht
Deze zelfstudie laat zien hoe u een cloudgebaseerde back-endservice toevoegt aan een mobiele Xamarin.iOS-app met een back-end voor mobiele apps van Azure.  U maakt zowel een nieuwe back-end voor mobiele apps als een eenvoudige Xamarin.iOS-app voor *takenlijsten* die app-gegevens opslaat in Azure.

Het volgen van deze zelfstudie is een vereiste voor alle andere Xamarin.iOS- zelfstudies over het gebruik van de Mobile Apps-functie in Azure App Service.

## <a name="prerequisites"></a>Vereisten
Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

* Een actief Azure-account. Als u geen account hebt, meldt u zich aan voor een proefversie van Azure en krijgt u maximaal tien gratis mobiele apps die u ook na de proefperiode kunt blijven gebruiken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.
* Visual Studio voor Mac. Zie [instellen en installeren voor Visual Studio voor Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* Een Mac met Xcode 9,0 of hoger.
  
## <a name="create-an-azure-mobile-app-backend"></a>Een back-end voor mobiele apps van Azure maken
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Een database verbinding maken en het client-en server project configureren
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>De Xamarin. iOS-app uitvoeren
1. Open het project Xamarin. iOS.

2. Ga naar de [Azure Portal](https://portal.azure.com/) en navigeer naar de mobiele app die u hebt gemaakt. Zoek op de Blade `Overview` naar de URL die het open bare eind punt is voor uw mobiele app. Voor beeld-de naam van de site van mijn app ' test123 ' wordt https://test123.azurewebsites.net.

3. Open het bestand `QSTodoService.cs` in deze map-xamarin. iOS/ZUMOAPPNAME. De naam van de toepassing is `ZUMOAPPNAME`.

4. Vervang in de klasse `QSTodoService` `ZUMOAPPURL`-variabele met een openbaar eind punt hierboven.

    `const string applicationURL = @"ZUMOAPPURL";`

    steeds
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Druk op de toets F5 om de app te implementeren en uit te voeren in een iPhone-emulator.

6. Typ in de app zinvolle tekst, zoals *voltooi de zelf studie* en klik vervolgens op de knop +.

    ![][10]

    Gegevens van de aanvraag worden opgenomen in de takentabel. Items die zijn opgeslagen in de tabel, worden geretourneerd door de back-end voor mobiele apps en de gegevens verschijnen in de lijst.

   > [!NOTE]
   > U kunt de code die toegang heeft tot de back-end van uw mobiele app bekijken om gegevens op te vragen en in te voegen. Deze code vindt u in het C#-bestand ToDoActivity.cs.
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png