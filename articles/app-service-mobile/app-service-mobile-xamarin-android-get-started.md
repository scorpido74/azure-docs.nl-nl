---
title: Aan de slag met Xamarin.Android-apps
description: Volg deze zelfstudie om aan de slag te gaan met Azure Mobile Apps voor Xamarin Android-ontwikkeling.
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: b42205436c88f9075423bfcaf9e5a9fd931ee4f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461365"
---
# <a name="create-a-xamarinandroid-app"></a>Een Xamarin.Android-app maken
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Overzicht
Deze zelfstudie laat zien hoe u een back-endservice toevoegt aan een Xamarin.Android-app in de cloud. Zie [What are Mobile Apps](app-service-mobile-value-prop.md) (Wat zijn Mobile Apps?) voor meer informatie.

Hieronder ziet u een schermafbeelding van de voltooide app:

![][0]

Het voltooien van deze zelfstudie is een vereiste voor alle andere Mobile Apps-zelfstudies voor Xamarin.Android-apps.

## <a name="prerequisites"></a>Vereisten
Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

* Een actief Azure-account. Als u geen account hebt, meld u zich aan voor een proefversie van Azure en ontvangt u maximaal 10 gratis mobiele apps. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.
* Visual Studio met Xamarin. Zie [Setup and install for Visual Studio and Xamarin](/visualstudio/cross-platform/setup-and-install) (Installeren en instellen voor Visual Studio en Xamarin) voor instructies.

## <a name="create-an-azure-mobile-app-backend"></a>Een back-end voor mobiele apps van Azure maken
Volg deze stappen voor het maken van een back-end voor mobiele apps.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

U hebt nu een back-end voor Azure Mobile Apps ingericht, die kan worden gebruikt door uw mobiele clienttoepassingen. Download vervolgens een serverproject voor een eenvoudige back-end voor takenlijsten en publiceer deze naar Azure.

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Een databaseverbinding maken en het client- en serverproject configureren
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>De Xamarin.Android-app uitvoeren
1. Open het Xamarin.Android-project.

2. Ga naar de [Azure-portal](https://portal.azure.com/) en navigeer naar de mobiele app die u hebt gemaakt. Zoek `Overview` op het blad naar de URL die het openbare eindpunt is voor uw mobiele app. Voorbeeld - de sitenaam voor mijn app-naam https://test123.azurewebsites.net"test123" is .

3. Open het `ToDoActivity.cs` bestand in deze map - xamarin.android/ZUMOAPPNAME/ToDoActivity.cs. De toepassingsnaam `ZUMOAPPNAME`is .

4. Vervang `ToDoActivity` in `ZUMOAPPURL` de klasse variabele door het bovenstaande openbare eindpunt.

    `const string applicationURL = @"ZUMOAPPURL";`

    Wordt
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Druk op de F5-toets om de app te implementeren en uit te voeren.

6. Typ in de app betekenisvolle tekst, zoals *De zelfstudie voltooien* en klik op de knop **Toevoegen.**

    ![][10]

    De gegevens van de aanvraag worden opgenomen in de takentabel. Items die zijn opgeslagen in de tabel, worden geretourneerd door de back-end voor mobiele apps en de gegevens verschijnen in de lijst.

   > [!NOTE]
   > U kunt de code die toegang heeft tot de back-end van uw mobiele app bekijken om gegevens op te vragen en in te voegen. Deze code vindt u in het C#-bestand ToDoActivity.cs.
   
## <a name="troubleshooting"></a>Problemen oplossen
Als u er problemen zijn met het bouwen van de oplossing, voert u NuGet Package Manager uit en werkt u de `Xamarin.Android`-ondersteuningspakketten bij. Quick Start-projecten bevatten mogelijk niet altijd de nieuwste versies.

Alle ondersteuningspakketten waarnaar wordt verwezen in uw project, moeten dezelfde versie hebben. Het [Azure Mobile Apps NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) heeft `Xamarin.Android.Support.CustomTabs`-afhankelijkheid voor het Android-platform, dus als uw project gebruikmaakt van nieuwere ondersteuningspakketten, moet u dit pakket met de vereiste versie rechtstreeks installeren om conflicten te voorkomen.

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
