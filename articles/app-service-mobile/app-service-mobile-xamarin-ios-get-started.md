---
title: Aan de slag met Xamarin. iOS-apps
description: Volg deze zelfstudie om aan de slag te gaan met Mobile Apps voor Xamarin.iOS-ontwikkeling.
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 6c35189e7c841fa2724f1cfe84afc689d5510676
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459001"
---
# <a name="create-a-xamarinios-app"></a>Een Xamarin.iOS-app maken
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

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

4. Vervang `ZUMOAPPURL` variabele met open bare eind punt in `QSTodoService`-klasse.

    `const string applicationURL = @"ZUMOAPPURL";`

    steeds
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Druk op de toets F5 om de app te implementeren en uit te voeren in een iPhone-emulator.

6. Typ in de app zinvolle tekst, zoals *voltooi de zelf studie* en klik vervolgens op de knop +.

    ![][10]

    De gegevens van de aanvraag worden opgenomen in de takentabel. Items die zijn opgeslagen in de tabel, worden geretourneerd door de back-end voor mobiele apps en de gegevens verschijnen in de lijst.

   > [!NOTE]
   > U kunt de code die toegang heeft tot de back-end van uw mobiele app bekijken om gegevens op te vragen en in te voegen. Deze code vindt u in het C#-bestand ToDoActivity.cs.
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png
